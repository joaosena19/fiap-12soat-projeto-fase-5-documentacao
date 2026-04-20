# Autenticação

## Contexto

A autenticação é feita por **client credentials** diretamente na API de Upload. Para este MVP não havia necessidade de acesso por usuários, tampouco um serviço exclusivo de autenticação.

## Fluxo de Autenticação

### Geração de token

O token JWT é gerado pelo endpoint `POST /api/autenticacao/token` na API de Upload. Funciona nos seguintes passos:

1. O cliente envia `clientId` e `clientSecret` no body da requisição
2. O `AutenticacaoService` valida as credenciais contra os valores configurados em variáveis de ambiente (`ApiCredentials:ClientId` e `ApiCredentials:ClientSecret`)
3. Se válidas, o `TokenService` gera um token JWT assinado com HMAC-SHA256
4. O token é retornado com tipo `Bearer` e validade de 1 hora

O token contém as claims `sub` (clientId), `jti` (identificador único), `iat` (timestamp de emissão) e `client_id`.

### Validação de token

As APIs validam o token JWT em cada requisição usando o middleware padrão do .NET (`AddJwtBearer`). A validação verifica:

- **IssuerSigningKey**: chave simétrica configurada via variável de ambiente
- **Issuer**: deve ser o issuer configurado (padrão: `UploadDiagramaApi`)
- **Audience**: deve ser o audience configurado (padrão: `AuthorizedServices`)
- **Lifetime**: token não pode estar expirado
- **ClockSkew**: `TimeSpan.Zero` (sem tolerância)

---
Anterior: [Infraestrutura, Kubernetes e Escalabilidade](../04%20-%20Infraestrutura,%20Kubernetes%20e%20Escalabilidade/1_infraestrutura.md)  
Próximo: [Comunicação entre serviços](../06%20-%20Comunicação%20entre%20serviços/1_comunicacao_entre_servicos.md)
