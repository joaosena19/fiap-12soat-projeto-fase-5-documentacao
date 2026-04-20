# ADR 0003 - Divisão em Microsserviços

## Status

Aceito

## Contexto

O sistema de análise de diagramas precisa receber uploads, processar via LLM e gerar relatórios. O Tech Challenge exige a divisão em microsserviços independentes.

## Discussão e possibilidades

Não houve discussão, pois é um requisito obrigatório do Tech Challenge.

A divisão seguiu a pipeline natural do sistema: **Upload → Processamento → Relatório**. Cada etapa tem perfil diferente — Upload é IO-bound (recebe arquivos, valida, envia ao S3), Processamento é CPU-bound com chamada à LLM, e Relatório é misto (consome eventos e expõe API). Essa divisão permite escalar cada serviço independentemente: se o processamento de LLM ficar lento, é possível escalar apenas o Processamento sem afetar os outros.

A comunicação entre os serviços é 100% assíncrona via SNS/SQS (Veja [ADR 0009 - SNS + SQS para Mensageria](0009_adr_sns_sqs_para_mensageria.md)).

## Decisão

Foi decidido dividir o sistema em três microsserviços independentes:

- [fiap-12soat-projeto-fase-5-upload](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-upload)
- [fiap-12soat-projeto-fase-5-processamento](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-processamento)
- [fiap-12soat-projeto-fase-5-relatorio](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-relatorio)

Além deles, foram criados repositórios para infraestrutura e documentação:

- [fiap-12soat-projeto-fase-5-infra](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-infra)
- [fiap-12soat-projeto-fase-5-documentacao](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-documentacao)

## Consequências

**Positivas:**

* Cada serviço pode escalar independentemente.
* Deploy, testes e manutenção isolados por responsabilidade.
* Falha em um serviço não derruba todo o sistema.

**Negativas:**

* Complexidade operacional maior (5 repositórios, 3 bancos, mensageria, infraestrutura distribuída).

---
Anterior: [ADR 0002 - Escolha do .NET](0002_adr_escolha_do_dotnet.md)  
Próximo: [ADR 0004 - Banco de Dados por Serviço](0004_adr_banco_de_dados_por_servico.md)
