# ADR 0001 - AWS como Cloud Provider

## Status

Aceito

## Contexto

Era necessário escolher um cloud provider para hospedar toda a infraestrutura do projeto: cluster Kubernetes, bancos de dados, armazenamento de arquivos, mensageria e observabilidade.

## Discussão e possibilidades

Avaliei os três principais cloud providers: AWS, Azure e GCP. Todos oferecem serviços equivalentes para o que o projeto precisa (Kubernetes gerenciado, banco de dados, armazenamento de objetos, mensageria).

Optei pela AWS por dois motivos: familiaridade com o ecossistema e a completude dos serviços para o que o projeto exige. Já tinha experiência com EKS, RDS, S3, SQS, SNS e IAM, e não havia nenhuma vantagem concreta em Azure ou GCP que justificasse a curva de aprendizado.

## Decisão

Foi decidido utilizar a AWS como cloud provider para toda a infraestrutura do projeto.

Os serviços utilizados são:

- **EKS** para o cluster Kubernetes
- **RDS PostgreSQL** para os bancos de dados
- **S3** para armazenamento de arquivos
- **SNS + SQS** para mensageria
- **IAM** para controle de acesso

## Consequências

**Positivas:**

* Ecossistema completo para todas as necessidades do projeto.
* Familiaridade com os serviços, sem curva de aprendizado.
* Documentação vasta e comunidade ativa.

**Negativas:**

* Vendor lock-in — trocar de cloud provider exigiria reconfigurar toda a infraestrutura Terraform e adaptar os serviços específicos (SQS, SNS, S3).

---
Anterior: [Qualidade - Infraestrutura](../10%20-%20Testes%20e%20qualidade/4_qualidade_infra.md)  
Próximo: [ADR 0002 - Escolha do .NET](0002_adr_escolha_do_dotnet.md)
