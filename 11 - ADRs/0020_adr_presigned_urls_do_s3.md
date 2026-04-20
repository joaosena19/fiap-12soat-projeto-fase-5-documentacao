# ADR 0020 - Presigned URLs do S3

## Status

Aceito

## Contexto

Relatórios em Markdown e PDF são armazenados no S3 (Veja [ADR 0007 - S3 como Armazenamento de Arquivos](0007_adr_s3_como_armazenamento.md)). Era necessário decidir como o usuário acessa esses arquivos sem expor o bucket publicamente.

## Discussão e possibilidades

Presigned URLs são URLs temporárias geradas pelo S3 que dão acesso a um objeto específico por tempo limitado. O bucket permanece privado (sem ACL pública) e o cliente baixa diretamente do S3, sem proxy no backend.

O `S3ArmazenamentoArquivoService.cs` no Relatório gera a URL via `GetPreSignedURL()` com expiração configurável em dias (`_presignedUrlExpiracaoDias`). A URL é gerada no momento da consulta e incluída na resposta da API.

Considerei as seguintes alternativas:

1. **Proxy no backend:** o cliente faria download via API do Relatório, que buscaria o arquivo no S3 e retornaria como stream. Funciona, mas adiciona latência e overhead no serviço.
2. **Bucket público:** o bucket seria acessível publicamente via URL fixa. Inseguro — qualquer pessoa com a URL teria acesso permanente.
3. **Download via API com streaming:** similar ao proxy, com a mesma desvantagem de overhead.

Presigned URLs resolvem o problema de forma elegante: acesso temporário, sem proxy, sem exposição do bucket.

## Decisão

Foi decidido utilizar presigned URLs do S3 para acesso a relatórios Markdown e PDF, com expiração configurável.

## Consequências

**Positivas:**

* Bucket permanece privado — sem risco de exposição acidental.
* Download direto do S3 — sem overhead no backend.
* Expiração configurável — acesso temporário por design.

**Negativas:**

* URLs expiram — se o usuário tentar acessar após o prazo, precisa solicitar uma nova URL via API.

---
Anterior: [ADR 0019 - DomainException como Error no Processamento](0019_adr_domain_exception_como_error_no_processamento.md)  
Próximo: [ADR 0021 - Retry como Re-upload](0021_adr_retry_como_re_upload.md)
