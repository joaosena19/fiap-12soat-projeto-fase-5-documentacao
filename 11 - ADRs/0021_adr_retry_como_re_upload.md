# ADR 0021 - Retry como Re-upload

## Status

Aceito

## Contexto

Se o processamento de um diagrama falhar (LLM indisponível, erro transitório), era necessário decidir como o usuário pode retentar.

## Discussão e possibilidades

Não existe endpoint de "retry" — o usuário simplesmente envia o arquivo novamente. O hash SHA-256 (Veja [ADR 0015 - Verificação de Duplicatas via Hash SHA-256](0015_adr_verificacao_duplicatas_via_hash.md)) detecta que é o mesmo arquivo e o sistema decide o que fazer com base no estado atual:

- **Se o processamento anterior já concluiu com sucesso:** o sistema retorna HTTP 200 com o resultado existente, sem guardar upload extra e sem consumir LLM.
- **Se o processamento anterior falhou:** o sistema republica a mensagem para o Processamento, que aceita reprocessamento quando o status é `Falha`.

Essa abordagem é mais simples do que expor um endpoint `POST /api/retry/{id}`, que exigiria que o cliente rastreasse IDs de falha. Com o re-upload, o usuário envia o arquivo e o sistema resolve.

## Decisão

Foi decidido que o mecanismo de retry é o próprio re-upload do arquivo. O sistema detecta duplicatas via hash e toma a ação apropriada.

## Consequências

**Positivas:**

* Simples para o usuário — o fluxo de retry é idêntico ao fluxo normal de upload.
* Idempotente — enviar o mesmo arquivo repetidamente nunca causa duplicatas nem desperdício.
* Sem endpoint adicional para gerenciar.

**Negativas:**

* O usuário não sabe explicitamente se está fazendo um upload novo ou um retry. Por design, isso é transparente.

---
Anterior: [ADR 0020 - Presigned URLs do S3](0020_adr_presigned_urls_do_s3.md)  
Próximo: [ADR 0022 - New Relic para Observabilidade](0022_adr_new_relic_para_observabilidade.md)
