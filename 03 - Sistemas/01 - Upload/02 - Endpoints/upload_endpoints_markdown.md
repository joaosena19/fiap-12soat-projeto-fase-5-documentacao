# Upload de Diagramas API
API para upload e validação de diagramas de componentes

## Version: v1

**Contact information:**  
João Dainese  
joaosenadainese@gmail.com  

### /api/autenticacao/token

#### POST
##### Summary:

Gera token JWT com base em clientId e clientSecret.

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | OK |
| 400 | Bad Request |
| 401 | Unauthorized |

### /api/upload/diagrama

#### POST
##### Summary:

Envia um arquivo de diagrama para análise.

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | OK |
| 202 | Accepted |
| 400 | Bad Request |
| 401 | Unauthorized |
| 500 | Internal Server Error |

### /api/upload/diagramas

#### GET
##### Summary:

Lista todos os uploads de diagramas.

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | OK |
| 401 | Unauthorized |
| 500 | Internal Server Error |
