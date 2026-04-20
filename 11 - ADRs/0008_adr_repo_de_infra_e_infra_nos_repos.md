# ADR 0008 - Repositório de Infra Centralizado + Banco nos Repos

## Status

Aceito

## Contexto

Com a divisão em microsserviços, foi necessário decidir como organizar o Terraform. Existem recursos naturalmente compartilhados entre todos os serviços (cluster EKS, VPC, subnets, S3, SNS, SQS, IAM, New Relic) e recursos que pertencem exclusivamente a um serviço (banco de dados).

## Discussão e possibilidades

Foram consideradas três abordagens:

1. **Tudo centralizado:** simples de gerenciar, mas acopla o ciclo de vida dos bancos à infraestrutura geral.
2. **Tudo descentralizado:** máxima independência, mas duplicação de configurações de rede e cluster.
3. **Abordagem híbrida:** repositório central para infraestrutura compartilhada e banco provisionado em cada repo.

A abordagem híbrida foi a escolha natural. O cluster EKS, VPC e subnets são compartilhados — não faz sentido duplicá-los. O banco de dados é o recurso que deve ficar isolado, pois cada serviço tem schema e configuração independentes (Veja [ADR 0004 - Banco de Dados por Serviço](0004_adr_banco_de_dados_por_servico.md)).

Na prática, a organização ficou:

| Repositório | Responsabilidade Terraform |
|---|---|
| `fiap-12soat-projeto-fase-5-infra` | VPC, subnets, EKS, S3 buckets, SNS topics, SQS queues, IAM, New Relic |
| `fiap-12soat-projeto-fase-5-upload` | RDS PostgreSQL do Upload |
| `fiap-12soat-projeto-fase-5-processamento` | RDS PostgreSQL do Processamento |
| `fiap-12soat-projeto-fase-5-relatorio` | RDS PostgreSQL do Relatório |

## Decisão

Foi decidido manter um repositório de infraestrutura geral (`fiap-12soat-projeto-fase-5-infra`) para recursos compartilhados e delegar o provisionamento do banco de dados ao repositório de cada microsserviço.

## Consequências

**Positivas:**

* Recursos compartilhados ficam centralizados sem duplicação.
* Cada microsserviço tem autonomia total sobre seu banco de dados.
* O pipeline de deploy de cada serviço pode provisionar seu próprio banco sem depender de outros repositórios.

**Negativas:**

* Nenhuma.

---
Anterior: [ADR 0007 - S3 como Armazenamento de Arquivos](0007_adr_s3_como_armazenamento.md)  
Próximo: [ADR 0009 - SNS + SQS para Mensageria](0009_adr_sns_sqs_para_mensageria.md)
