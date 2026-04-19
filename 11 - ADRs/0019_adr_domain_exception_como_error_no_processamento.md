# ADR 0019 - DomainException como Error no Processamento

## Status

Aceito

## Contexto

No serviço de Processamento, qualquer exceção de domínio (`DomainException`) precisa ser logada. Era necessário decidir o nível de log apropriado.

## Discussão e possibilidades

Nos serviços com API (Upload e Relatório), `DomainException` é logada como `Information`, pois geralmente representa erro de input do usuário — algo esperado e comum. O usuário enviou dados inválidos, o sistema rejeitou com a mensagem apropriada.

No Processamento, a situação é diferente. O usuário não está interagindo — é um background job que consome mensagens. Se uma `DomainException` ocorre aqui, significa que o código tem um bug: uma transição de estado inválida, dados inconsistentes vindos da mensagem, ou uma validação de domínio que deveria ter sido tratada antes de publicar a mensagem.

Considerei três níveis:

1. **Information:** errado, pois no Processamento não é erro do usuário. Logaria um bug como informação normal.
2. **Warning:** errado, pois `Warning` é para degradações onde a aplicação conseguiu se recuperar sozinha. Uma `DomainException` no Processamento é uma falha que precisa de atenção.
3. **Error:** correto, pois requer atenção do desenvolvedor.

O `ProcessarDiagramaUseCase.cs` captura `DomainException` no catch block e loga como `LogError`.

## Decisão

Foi decidido que toda `DomainException` no serviço de Processamento é logada como `Error`, diferente dos outros serviços onde é `Information`.

## Consequências

**Positivas:**

* Bugs no Processamento são visíveis imediatamente nos dashboards de monitoramento.
* Não mascara problemas reais como informação normal.

**Negativas:**

* Se uma `DomainException` for lançada por um cenário legítimo (ex: mensagem duplicada que chega após processamento), será logada como Error desnecessariamente. Na prática, cenários legítimos são tratados antes de chegar ao catch de `DomainException`.

---
Anterior: [ADR 0018 - Google Gemini como LLM](0018_adr_google_gemini_como_llm.md)  
Próximo: [ADR 0020 - Presigned URLs do S3](0020_adr_presigned_urls_do_s3.md)
