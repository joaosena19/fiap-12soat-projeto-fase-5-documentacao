# Autenticação

## Contexto

A autenticação é feita por **client credentials** diretamente na API de Upload. O sistema não tem o conceito de "usuário final" com login e senha — o acesso é feito por sistemas e integrações que se autenticam com um par `clientId`/`clientSecret` e recebem um token JWT.

## Fluxo de Autenticação

### Geração de token

O token JWT é gerado pelo endpoint `POST /api/autenticacao/token` na API de Upload. Funciona nos seguintes passos:

1. O cliente envia `clientId` e `clientSecret` no body da requisição
2. O `AutenticacaoService` valida as credenciais contra os valores configurados em variáveis de ambiente (`ApiCredentials:ClientId` e `ApiCredentials:ClientSecret`)
3. Se válidas, o `TokenService` gera um token JWT assinado com HMAC-SHA256
4. O token é retornado com tipo `Bearer` e validade de 1 hora

O token contém as claims `sub` (clientId), `jti` (identificador único), `iat` (timestamp de emissão) e `client_id`.

### Validação de token

As APIs que exigem autenticação (atualmente apenas o serviço de Relatório) validam o token JWT em cada requisição usando o middleware padrão do .NET (`AddJwtBearer`). A validação verifica:

- **IssuerSigningKey**: chave simétrica configurada via variável de ambiente
- **Issuer**: deve ser o issuer configurado (padrão: `UploadDiagramaApi`)
- **Audience**: deve ser o audience configurado (padrão: `AuthorizedServices`)
- **Lifetime**: token não pode estar expirado
- **ClockSkew**: `TimeSpan.Zero` (sem tolerância)

## Proteção dos endpoints

| Serviço | Endpoint | Protegido |
|---------|----------|-----------|
| Upload | `POST /api/upload/diagrama` | Não |
| Upload | `GET /api/upload/diagramas` | Não |
| Upload | `POST /api/autenticacao/token` | Não (`[AllowAnonymous]`) |
| Relatório | `GET /api/relatorio` | Sim (`[Authorize]`) |
| Relatório | `GET /api/relatorio/{analiseDiagramaId}` | Sim (`[Authorize]`) |
| Relatório | `POST /api/relatorio/{analiseDiagramaId}` | Sim (`[Authorize]`) |
| Processamento | (worker, sem API) | N/A |

Os endpoints de upload foram deixados abertos pois o upload de diagramas não contém dados sensíveis. Já os endpoints de relatório exigem autenticação pois expõem os resultados das análises.

## CorrelationId

O `CorrelationId` é propagado entre todas as APIs e mensageria. Cada requisição HTTP passa pelo `CorrelationIdMiddleware`, que lê o header `X-Correlation-ID` (ou gera um novo GUID se ausente) e o propaga via `CorrelationContext` e `LogContext` do Serilog.

Na mensageria (MassTransit + Amazon SQS/SNS), os filtros `PublishCorrelationIdFilter`, `SendCorrelationIdFilter` e `ConsumeCorrelationIdFilter` propagam o `CorrelationId` entre as mensagens.

---
Anterior: [Infraestrutura, Kubernetes e Escalabilidade](../04%20-%20Infraestrutura,%20Kubernetes%20e%20Escalabilidade/1_infraestrutura.md)  
Próximo: [Comunicação entre serviços](../06%20-%20Comunicação%20entre%20serviços/1_comunicacao_entre_servicos.md)
