# ADR 0006 - Campos Mapeados como JSONB

## Status

Aceito

## Contexto

Os dados de análise de IA (componentes identificados, riscos arquiteturais, recomendações) são listas de strings de tamanho variável. Era necessário decidir como armazená-los no PostgreSQL.

## Discussão e possibilidades

A alternativa clássica seria criar tabelas filhas com FK para cada tipo de dado (uma tabela `componentes_identificados`, outra `riscos_arquiteturais`, etc.). Contudo, esses dados nunca são consultados individualmente — sempre são lidos e escritos junto com o aggregate. Criar tabelas filhas adicionaria complexidade sem benefício, pois não há consultas do tipo "busque todos os riscos de todos os diagramas".

O JSONB do PostgreSQL (Veja [ADR 0005 - Escolha do PostgreSQL](0005_adr_escolha_do_postgresql.md)) permite armazenar listas e objetos complexos diretamente na coluna, evitando a explosão de tabelas. Se no futuro for necessário consultar esses dados individualmente, o PostgreSQL permite indexação e consultas em JSONB.

No Relatório, os campos `relatorios`, `erros` e `analise_resultado` são mapeados como JSONB explícito via `ResultadoDiagramaConfiguration.cs`. No Processamento, as listas `componentes_identificados`, `riscos_arquiteturais` e `recomendacoes_basicas` são serializadas via `JsonSerializer` em `HasConversion` na configuração do EF Core.

## Decisão

Foi decidido mapear campos de estrutura variável como JSONB no PostgreSQL, tanto para dados de análise de IA quanto para entidades filhas do aggregate.

## Consequências

**Positivas:**

* Evita explosão de tabelas para dados que são sempre lidos/escritos junto com o aggregate.
* Flexibilidade para estruturas de tamanho variável.
* PostgreSQL permite indexação em JSONB se necessário no futuro.

**Negativas:**

* Sem constraints de FK nos dados aninhados — a integridade é garantida pelo código, não pelo banco.
* Consultas em JSONB são menos performáticas que consultas em tabelas normalizadas. Na prática, isso não é um problema pois esses dados nunca são consultados isoladamente.

---
Anterior: [ADR 0005 - Escolha do PostgreSQL](0005_adr_escolha_do_postgresql.md)  
Próximo: [ADR 0007 - S3 como Armazenamento de Arquivos](0007_adr_s3_como_armazenamento.md)
