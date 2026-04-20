# Qualidade - Upload

## Frameworks de Teste
- **xUnit** 2.9.3 (framework de testes)
- **Moq** 4.20.72 (mocking)
- **Shouldly** 4.2.1 (asserções fluentes)
- **coverlet** 6.0.4 (cobertura de código)
- **Microsoft.AspNetCore.Mvc.Testing** 10.0.0 (testes de integração)
- **Microsoft.EntityFrameworkCore.InMemory** 9.0.4 (banco in-memory para testes)

## Test Coverage

Veja o [relatório completo de cobertura](Anexos/test_coverage_upload_completo.zip) (download do HTML).

![Test Coverage - Upload](Anexos/test_coverage_upload_resumo.png)

O projeto de Upload possui 38 arquivos de teste, cobrindo testes unitários (domínio, application, infrastructure) e testes de integração (controllers, middleware, health checks, autenticação).

## Proteção de Branch

A branch `main` está protegida contra push direto. Toda alteração precisa ser feita via Pull Request, e o CI Gate deve passar com sucesso antes do merge.

![Proteção de Branches](../08%20-%20CI%20%26%20CD/Anexos/protecao_branch.png)

---
Anterior: [Plano de monitoramento](../09%20-%20Plano%20de%20Monitoramento/1_plano_de_monitoramento.md)  
Próximo: [Qualidade - Processamento](2_qualidade_processamento.md)
