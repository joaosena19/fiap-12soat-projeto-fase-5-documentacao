# Qualidade - Infraestrutura

## Test Coverage

O repositório de infraestrutura não possui testes unitários, pois é composto apenas por módulos Terraform. A validação é feita pelo `terraform validate` na pipeline de CI.

## Proteção de Branch

A branch `main` está protegida contra push direto. Toda alteração precisa ser feita via Pull Request, e o CI Gate deve passar com sucesso antes do merge.

![Proteção de Branches](../08%20-%20CI%20%26%20CD/Anexos/protecao_branch.png)

---
Anterior: [Qualidade - Relatório](3_qualidade_relatorio.md)  
Próximo: [ADR 0001 - AWS como Cloud Provider](../11%20-%20ADRs/0001_adr_aws_como_cloud_provider.md)
