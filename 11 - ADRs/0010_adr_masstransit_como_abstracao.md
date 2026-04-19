# ADR 0010 - MassTransit como Abstração de Mensageria

## Status

Aceito

## Contexto

Era necessário decidir como os microsserviços .NET interagem com o SNS/SQS (ver [ADR 0009](0009_adr_sns_sqs_para_mensageria.md)). A alternativa era usar o AWS SDK diretamente ou uma biblioteca de abstração.

## Discussão e possibilidades

O MassTransit abstrai o broker de mensageria: consumers, publish, retry, serialização e controle de concorrência — tudo via interfaces .NET. Trocar de SQS para RabbitMQ ou Kafka exigiria apenas mudar a configuração de transporte, sem alterar os consumers.

A alternativa seria usar o AWS SDK diretamente. Isso daria mais controle sobre a interação com o SQS, mas geraria muito boilerplate: serialização/deserialização manual, polling, tratamento de retry, dead-letter queue. O MassTransit resolve tudo isso de forma declarativa.

No projeto, os consumers são tipados (`UploadDiagramaConcluidoConsumer`, etc.) e a configuração de `ConcurrentMessageLimit` e `PrefetchCount` é feita via MassTransit, o que permite controle fino da concorrência por consumer.

## Decisão

Foi decidido utilizar o MassTransit como abstração de mensageria nos três microsserviços.

## Consequências

**Positivas:**

* Portabilidade — trocar de broker exige apenas mudar a configuração de transporte.
* Consumers tipados e declarativos, sem boilerplate de polling e serialização.
* Suporte nativo a retry, circuit breaker e controle de concorrência.

**Negativas:**

* Mais uma dependência no projeto.
* Curva de aprendizado para quem não conhece o MassTransit.

---
Anterior: [ADR 0009 - SNS + SQS para Mensageria](0009_adr_sns_sqs_para_mensageria.md)  
Próximo: [ADR 0011 - ID Único por Todo o Sistema (AnaliseDiagramaId)](0011_adr_id_unico_analise_diagrama_id.md)
