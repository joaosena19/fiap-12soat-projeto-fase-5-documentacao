# ADR 0012 - Emissão de Token no Upload

## Status

Aceito

## Contexto

O sistema precisa autenticar chamadas à API. Era necessário decidir onde emitir o token JWT.

## Discussão e possibilidades

Considerei criar um serviço de autenticação dedicado (Lambda, microsserviço de identidade). Contudo, isso seria desproporcional — adicionaria um componente inteiro para uma funcionalidade trivial. O `TokenService.cs` no Upload tem poucas linhas e resolve o problema.

O Upload em específico foi o serviço selecionado pois ele é a porta de entrada do sistema, o primeiro contato é através do envio do diagrama.

O endpoint `POST /api/autenticacao/token` recebe o `clientId` e retorna o JWT com claims `sub`, `client_id`, `jti` e `iat`.

## Decisão

Foi decidido que o Upload é responsável pela emissão do token JWT. Não há serviço de autenticação dedicado.

## Consequências

**Positivas:**

* Simplicidade — sem componente adicional para gerenciar.
* Funciona para o escopo do projeto.

**Negativas:**

* A autenticação está acoplada ao serviço de Upload. Se no futuro a autenticação precisar de funcionalidades mais complexas (multi-tenant, refresh token, OAuth), seria necessário extrair para um serviço dedicado.

---
Anterior: [ADR 0011 - ID Único por Todo o Sistema (AnaliseDiagramaId)](0011_adr_id_unico_analise_diagrama_id.md)  
Próximo: [ADR 0013 - Processamento como Worker](0013_adr_processamento_como_worker.md)
