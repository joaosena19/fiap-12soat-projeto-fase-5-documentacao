# ADR 0016 - Limite de 5MB por Arquivo

## Status

Aceito

## Contexto

Era necessário definir um limite de tamanho para os uploads de diagramas.

## Discussão e possibilidades

Diagramas de arquitetura são tipicamente imagens leves — PNG, JPEG, WebP. Um arquivo acima de 5MB está fora do escopo esperado: provavelmente seria um PDF com dezenas de páginas ou uma imagem de altíssima resolução. A LLM não precisa de resolução alta para analisar um diagrama — melhor que o usuário reduza a resolução.

Aceitar arquivos grandes consumiria armazenamento no S3 desnecessariamente, sem benefício na qualidade da análise. Além disso, arquivos maiores aumentam o tempo de upload, hash SHA-256 e transferência para a LLM.

A validação é feita em duas camadas:

1. **Kestrel** — `[RequestSizeLimit]` no controller rejeita antes de ler o body completo.
2. **Domain** — o value object `Tamanho` valida semanticamente (`TamanhoMaximoBytes = 5 * 1024 * 1024`).

A primeira camada protege a infraestrutura (não gasta memória com arquivos grandes), a segunda garante a regra de negócio no domínio.

## Decisão

Foi decidido limitar o tamanho de upload a 5MB por arquivo, com validação em duas camadas (infraestrutura e domínio).

## Consequências

**Positivas:**

* Protege contra abuso e desperdício de armazenamento.
* Mantém o tempo de processamento previsível.
* Validação em duas camadas garante proteção tanto na infraestrutura quanto no domínio.

**Negativas:**

* Diagramas em altíssima resolução são rejeitados. O usuário precisa reduzir a resolução antes de enviar, o que é aceitável para o caso de uso.

---
Anterior: [ADR 0015 - Verificação de Duplicatas via Hash SHA-256](0015_adr_verificacao_duplicatas_via_hash.md)  
Próximo: [ADR 0017 - ClamAV como Serviço Dedicado no Cluster](0017_adr_clamav_como_servico_dedicado.md)
