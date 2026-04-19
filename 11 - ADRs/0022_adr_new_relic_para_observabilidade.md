# ADR 0022 - New Relic para Observabilidade

## Status

Aceito

## Contexto

Com três microsserviços, mensageria assíncrona e LLM externa, era necessário ter observabilidade centralizada para monitorar a saúde do sistema e diagnosticar problemas.

## Discussão e possibilidades

O New Relic é um serviço completo que cobre APM, logs, métricas e distributed tracing num único lugar. O plano gratuito é generoso para uso acadêmico. Sendo gerenciado, não preciso hospedar nem manter infraestrutura de observabilidade.

A integração com .NET é feita via OpenTelemetry, e o provisionamento é via Terraform no repositório de infraestrutura (`fiap-12soat-projeto-fase-5-infra/infra/newrelic.tf`). Métricas customizadas são emitidas via `IMetricsService` nos três serviços.

Considerei as seguintes alternativas:

1. **Datadog:** completo, mas caro. O plano gratuito é limitado demais para monitorar 3 microsserviços com APM.
2. **Grafana + Prometheus:** precisa hospedar e gerenciar (deploy, storage, alerting). Adicionaria complexidade operacional significativa para um projeto acadêmico.
3. **CloudWatch:** limitado para distributed tracing entre microsserviços. Funciona para logs e métricas básicas, mas não substitui um APM completo.

## Decisão

Foi decidido utilizar o New Relic como plataforma de observabilidade centralizada para todos os microsserviços.

Veja mais em [Plano de monitoramento](../09%20-%20Plano%20de%20Monitoramento/1_plano_de_monitoramento.md).

## Consequências

**Positivas:**

* APM, logs, métricas e distributed tracing num único lugar.
* Plano gratuito generoso para uso acadêmico.
* Gerenciado — sem infraestrutura de observabilidade para manter.

**Negativas:**

* Vendor lock-in — dashboards, alertas e queries NRQL são específicos do New Relic.

---
Anterior: [ADR 0021 - Retry como Re-upload](0021_adr_retry_como_re_upload.md)
