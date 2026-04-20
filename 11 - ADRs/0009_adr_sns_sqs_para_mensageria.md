# ADR 0009 - SNS + SQS para Mensageria

## Status

Aceito

## Contexto

Os microsserviços se comunicam exclusivamente por mensagens assíncronas. Era necessário escolher o broker de mensageria.

## Discussão e possibilidades

O padrão SNS + SQS da AWS resolve o problema com uma vantagem importante: fan-out nativo. O SNS permite que uma mensagem publicada seja entregue a múltiplas filas SQS. No projeto, a mensagem `upload_diagrama_concluido` precisa ser entregue tanto para o Processamento quanto para o Relatório — com SNS + SQS, isso é apenas uma configuração de subscription, sem lógica adicional no código.

O SQS garante entrega com retry e dead-letter queue. Ambos são serverless — sem servidor para gerenciar.

Considerei RabbitMQ como alternativa, mas precisaria ser hospedado e gerenciado. Adicionaria complexidade operacional sem benefício para o projeto.

## Decisão

Foi decidido utilizar Amazon SNS + SQS como broker de mensageria para toda a comunicação assíncrona entre microsserviços.

Veja mais em [Comunicação entre serviços](../06%20-%20Comunicação%20entre%20serviços/1_comunicacao_entre_servicos.md).

## Consequências

**Positivas:**

* Serverless — sem servidor para gerenciar.
* Fan-out nativo via SNS para múltiplos consumers.
* SQS garante entrega com retry e dead-letter queue.

**Negativas:**

* Vendor lock-in — trocar de broker exigiria a mensageria. O MassTransit (Veja [ADR 0010 - MassTransit como Abstração de Mensageria](0010_adr_masstransit_como_abstracao.md)) mitiga parcialmente esse risco no código.

---
Anterior: [ADR 0008 - Repositório de Infra Centralizado + Banco nos Repos](0008_adr_repo_de_infra_e_infra_nos_repos.md)  
Próximo: [ADR 0010 - MassTransit como Abstração de Mensageria](0010_adr_masstransit_como_abstracao.md)
