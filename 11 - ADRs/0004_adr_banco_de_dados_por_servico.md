# ADR 0004 - Banco de Dados por Serviço

## Status

Aceito

## Contexto

Com a divisão em microsserviços, era necessário decidir se os serviços compartilhariam o mesmo banco de dados ou se cada um teria o seu. O Tech Challenge exige banco de dados isolado por microsserviço.

## Discussão e possibilidades

Não houve discussão, pois é um requisito obrigatório do Tech Challenge.

Cada serviço tem modelo de dados diferente: Upload armazena dados do diagrama enviado (`UploadDiagramas`), Processamento armazena o estado de processamento e resultado da análise (`ProcessamentoDiagramas`), e Relatório armazena o estado consolidado e relatórios gerados (`ResultadosDiagrama`). A separação garante que cada serviço tem autonomia total sobre seu schema e ciclo de vida.

## Decisão

Foi decidido que cada microsserviço tem seu próprio banco de dados isolado, com `AppDbContext` e connection string independentes.

Veja mais em [Banco de dados - Upload](../03%20-%20Sistemas/01%20-%20Upload/03%20-%20Banco%20de%20dados/1_banco_de_dados_upload.md), [Banco de dados - Processamento](../03%20-%20Sistemas/02%20-%20Processamento/02%20-%20Banco%20de%20dados/1_banco_de_dados_processamento.md) e [Banco de dados - Relatório](../03%20-%20Sistemas/03%20-%20Relatórios/03%20-%20Banco%20de%20dados/1_banco_de_dados_relatorio.md).

## Consequências

**Positivas:**

* Independência total de deploy e schema.
* Isolamento de falhas — problema em um banco não afeta os outros.
* Cada serviço evolui seu modelo de dados sem coordenação.

**Negativas:**

* Três bancos para gerenciar, com provisionamento e monitoramento independentes.

---
Anterior: [ADR 0003 - Divisão em Microsserviços](0003_adr_divisao_em_microsservicos.md)  
Próximo: [ADR 0005 - Escolha do PostgreSQL](0005_adr_escolha_do_postgresql.md)
