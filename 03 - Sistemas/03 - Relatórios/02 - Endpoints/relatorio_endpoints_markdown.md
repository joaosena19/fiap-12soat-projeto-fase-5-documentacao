# Relatório de Análises API
API para consulta de relatórios de análises de diagramas de componentes

## Version: v1

**Contact information:**  
João Dainese  
joaosenadainese@gmail.com  

### /api/relatorio

#### GET
##### Summary:

Lista todos os resultados de diagramas com status detalhado por relatório.

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | OK |
| 401 | Unauthorized |
| 500 | Internal Server Error |

### /api/relatorio/{analiseDiagramaId}

#### GET
##### Summary:

Busca uma análise de diagrama pelo AnaliseDiagramaId.

##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| analiseDiagramaId | path |  | Yes | string (uuid) |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | OK |
| 401 | Unauthorized |
| 404 | Not Found |
| 500 | Internal Server Error |

#### POST
##### Summary:

Solicita a geração de relatórios pendentes ou com erro.

##### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| analiseDiagramaId | path |  | Yes | string (uuid) |

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | OK |
| 202 | Accepted |
| 207 | Multi-Status |
| 400 | Bad Request |
| 401 | Unauthorized |
| 404 | Not Found |
| 500 | Internal Server Error |
