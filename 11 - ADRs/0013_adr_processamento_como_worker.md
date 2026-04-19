# ADR 0013 - Processamento como Worker

## Status

Aceito

## Contexto

O serviço de Processamento precisa analisar diagramas via LLM. Era necessário decidir se seria uma API REST ou um Worker orientado a mensagens.

## Discussão e possibilidades

O processamento é disparado por mensagem, não por requisição HTTP. A análise via LLM leva segundos — não cabe num request/response síncrono. Se fosse uma API, o usuário teria que fazer polling ou aguardar uma resposta síncrona de uma operação lenta, o que é uma experiência ruim.

Como Worker, o serviço consome mensagens do SQS via MassTransit com controle fino de concorrência (`ConcurrentMessageLimit = 1`). Não há overhead de HTTP server, load balancer nem health check de API.

Considerei também uma Lambda triggerada por SQS. Contudo, seria mais caro desnecessariamente. Só precisaríamos de escalabilidade tão elástica se o processamento da IA fosse interno, mas delegamos para o Gemini (ver [ADR 0018](0018_adr_google_gemini_como_llm.md)). Com uma Lambda por mensagem, o custo seria proporcional ao volume, enquanto o Worker no cluster EKS já está provisionado.

## Decisão

Foi decidido que o serviço de Processamento é um Worker (sem endpoints HTTP), consumindo mensagens via MassTransit/SQS.

## Consequências

**Positivas:**

* Sem overhead de HTTP server e load balancer.
* Controle fino de concorrência por consumer.
* Processamento assíncrono — o usuário não espera.

**Negativas:**

* Não é possível consultar o Processamento diretamente. O status é consultado via Relatório (ver [ADR 0014](0014_adr_relatorio_como_hub_de_agregacao.md)).

---
Anterior: [ADR 0012 - Emissão de Token no Upload](0012_adr_emissao_de_token_no_upload.md)  
Próximo: [ADR 0014 - Relatório como Hub de Agregação de Eventos](0014_adr_relatorio_como_hub_de_agregacao.md)
