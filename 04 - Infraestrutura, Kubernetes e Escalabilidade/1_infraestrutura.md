# Infraestrutura, Kubernetes e Escalabilidade

## Visão geral

Toda a infraestrutura é provisionada via Terraform e os serviços rodam em um cluster Amazon EKS. Cada repositório possui seu próprio diretório `terraform/` para provisionar recursos específicos (banco de dados), enquanto o repositório de infraestrutura centraliza os recursos compartilhados (VPC, EKS, NLB, SNS/SQS, S3, New Relic).

| Recurso | Repositório Terraform |
|---------|----------------------|
| VPC, EKS, NLB, SNS/SQS, S3, IAM, New Relic | [fiap-12soat-projeto-fase-5-infra](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-infra) |
| RDS PostgreSQL (Upload) | [fiap-12soat-projeto-fase-5-upload/terraform](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-upload/tree/main/terraform) |
| RDS PostgreSQL (Processamento) | [fiap-12soat-projeto-fase-5-processamento/terraform](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-processamento/tree/main/terraform) |
| RDS PostgreSQL (Relatório) | [fiap-12soat-projeto-fase-5-relatorio/terraform](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-relatorio/tree/main/terraform) |

## Rede

### VPC

Uma VPC dedicada com CIDR `10.1.0.0/16`, DNS habilitado (hostnames + support). As subnets são públicas, distribuídas em 3 availability zones (`us-east-1a`, `us-east-1b`, `us-east-1c`), com CIDRs calculados automaticamente via `cidrsubnet`.

### Network Load Balancer (NLB)

Um NLB internet-facing distribui o tráfego externo para os serviços via NodePort:

| Serviço | Porta NLB | NodePort | Target Group |
|---------|-----------|----------|--------------|
| Upload | 84 | 30084 | `upload-tg` |
| Relatório | 86 | 30086 | `relatorio-tg` |

O Processamento não tem entrada no NLB pois é um worker sem API.

O security group do NLB permite tráfego de entrada nas portas 84 e 86 de qualquer origem (`0.0.0.0/0`). Uma regra adicional no security group do cluster EKS permite tráfego do NLB para os nodes nas portas 30084-30086.

## Amazon EKS

### Cluster

- **Versão Kubernetes**: 1.33
- **Modo de autenticação**: API
- **Região**: us-east-1

### Node Group

- **Tipo de instância**: `t3.small`
- **Disco**: 20 GB por node
- **Scaling**: mínimo 1, desejado 3, máximo 4 nodes
- **Update config**: max_unavailable = 1

### IAM

Duas roles são utilizadas:

- **EKS Cluster Role**: com policy `AmazonEKSClusterPolicy`
- **EKS Node Role**: com policies `AmazonEKSWorkerNodePolicy`, `AmazonEKS_CNI_Policy`, `AmazonEC2ContainerRegistryReadOnly`, e uma policy custom para acesso ao bucket S3 de upload (`s3:PutObject`, `s3:GetObject`, `s3:DeleteObject`, `s3:ListBucket`)

## Kubernetes — Deployments

### Upload

- **Imagem**: `joaodainese/upload-service`
- **Porta**: 8080 (container) → 80 (Service) → 30084 (NodePort)
- **Resources**: requests 50m CPU / 256Mi RAM, limits 500m CPU / 512Mi RAM
- **Probes**: liveness (`/health/live`), readiness (`/health/ready`), startup (`/health/startup`)
- **Configuração**: ConfigMap `upload-config` + Secret `upload-secret`

### ClamAV

- **Imagem**: `clamav/clamav:stable`
- **Porta**: 3310 (clamd)
- **Resources**: requests 100m CPU / 512Mi RAM, limits 500m CPU / 1Gi RAM
- **Probes**: liveness e readiness TCP na porta 3310, com `initialDelaySeconds` de 120s e 90s (tempo para download das definições)
- **Service**: `clamav-service` (ClusterIP, porta 3310)

### Processamento

- **Imagem**: `joaodainese/processamento-service`
- **Sem porta exposta** (worker, não recebe HTTP)
- **Resources**: requests 50m CPU / 256Mi RAM, limits 500m CPU / 512Mi RAM
- **Probe**: liveness via `pidof dotnet` (não tem endpoint HTTP)
- **Configuração**: ConfigMap `processamento-config` + Secret `processamento-secret`

### Relatório

- **Imagem**: `joaodainese/relatorio-service`
- **Porta**: 8080 (container) → 80 (Service) → 30086 (NodePort)
- **Resources**: requests 50m CPU / 256Mi RAM, limits 500m CPU / 512Mi RAM
- **Probes**: liveness (`/health/live`), readiness (`/health/ready`), startup (`/health/startup`)
- **Configuração**: ConfigMap `relatorio-config` + Secret `relatorio-secret`

## Escalabilidade (HPA)

Todos os três serviços possuem Horizontal Pod Autoscaler configurado com a mesma política:

| Configuração | Valor |
|---|---|
| Métrica | CPU Utilization |
| Target | 70% |
| Min replicas | 1 |
| Max replicas | 5 |

O Processamento tem `ConcurrentMessageLimit = 1` e `PrefetchCount = 1` na fila SQS, o que significa que cada pod processa uma mensagem por vez. O HPA escala horizontalmente quando o CPU de um pod ultrapassa 70%, adicionando pods que passam a competir por mensagens na fila.

## Armazenamento (S3)

O bucket `fiap-12soat-fase5-upload-diagramas` armazena os arquivos de diagrama enviados pelo serviço de Upload. Configurações:

- **Versionamento**: habilitado
- **Criptografia**: AES256 (server-side)
- **Acesso público**: totalmente bloqueado (block_public_acls, block_public_policy, ignore_public_acls, restrict_public_buckets)

## Banco de Dados

Cada serviço possui sua própria instância RDS PostgreSQL, provisionada via Terraform no repositório de cada serviço. As instâncias compartilham a mesma configuração base:

- **Engine**: PostgreSQL
- **Storage**: gp3, criptografado
- **Acesso**: privado (não publicly accessible), acessível apenas via VPC
- **Backups**: automáticos com retenção configurável
- **ORM**: Entity Framework Core (code-first)

As tabelas são criadas automaticamente pelo Entity Framework Core na inicialização da aplicação. Veja os detalhes de cada banco em:

- [Banco de dados - Upload](../03%20-%20Sistemas/01%20-%20Upload/03%20-%20Banco%20de%20dados/1_banco_de_dados_upload.md)
- [Banco de dados - Processamento](../03%20-%20Sistemas/02%20-%20Processamento/02%20-%20Banco%20de%20dados/1_banco_de_dados_processamento.md)
- [Banco de dados - Relatório](../03%20-%20Sistemas/03%20-%20Relatórios/03%20-%20Banco%20de%20dados/1_banco_de_dados_relatorio.md)

## Mensageria (SNS/SQS)

Os tópicos SNS e filas SQS são provisionados no repositório de infraestrutura. Todas as filas usam long polling (`receive_wait_time_seconds = 20`) e retenção de 24 horas. Veja detalhes em [Comunicação entre serviços](../06%20-%20Comunicação%20entre%20serviços/1_comunicacao_entre_servicos.md).

---
Anterior: [Arquitetura interna - Relatórios](../03%20-%20Sistemas/03%20-%20Relatórios/04%20-%20Arquitetura%20interna/1_arquitetura_interna_relatorio.md)  
Próximo: [Autenticação](../05%20-%20Autenticação/1_autenticacao.md)
