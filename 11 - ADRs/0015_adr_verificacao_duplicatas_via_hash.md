# ADR 0015 - Verificação de Duplicatas via Hash SHA-256

## Status

Aceito

## Contexto

Usuários podem enviar o mesmo arquivo mais de uma vez. Era necessário decidir como tratar duplicatas para evitar desperdício de processamento pela LLM.

## Discussão e possibilidades

Optei por calcular o SHA-256 do conteúdo do arquivo antes do upload. O `GeradorHashSha256.cs` usa `SHA256.HashDataAsync()` e o `EnviarArquivoUseCase` chama `ObterPorHashAsync()` para verificar se já existe um upload com o mesmo hash.

Se o hash já existir e o processamento anterior tiver concluído com sucesso, o sistema retorna HTTP 200 com o resultado existente — sem guardar upload extra e sem consumir LLM. Se o processamento anterior tiver falhado, o sistema republica a mensagem para reprocessamento (ver [ADR 0021](0021_adr_retry_como_re_upload.md)).

SHA-256 é resistente a colisões e rápido para arquivos de até 5MB (ver [ADR 0016](0016_adr_limite_de_5mb_por_arquivo.md)). O overhead de cálculo é insignificante comparado ao custo de uma chamada à LLM.

Considerei como alternativas: comparação por nome de arquivo (frágil, pois nomes mudam) e não verificar (desperdício de LLM, que é o recurso mais caro do sistema).

## Decisão

Foi decidido verificar duplicatas via hash SHA-256 do conteúdo do arquivo antes de prosseguir com o upload.

## Consequências

**Positivas:**

* Evita processamento desnecessário pela LLM — o recurso mais caro do sistema.
* Idempotência natural — enviar o mesmo arquivo duas vezes retorna o mesmo resultado.

**Negativas:**

* Dois arquivos diferentes com o mesmo hash seriam tratados como duplicata. Na prática, a probabilidade de colisão SHA-256 é astronomicamente baixa.

---
Anterior: [ADR 0014 - Relatório como Hub de Agregação de Eventos](0014_adr_relatorio_como_hub_de_agregacao.md)  
Próximo: [ADR 0016 - Limite de 5MB por Arquivo](0016_adr_limite_de_5mb_por_arquivo.md)
