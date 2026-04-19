# Plano de monitoramento

## New Relic

O New Relic é a ferramenta de monitoramento do projeto. A configuração é feita via variáveis de ambiente no ConfigMap do Kubernetes, incluindo distributed tracing, profiler .NET e envio de logs estruturados.

## Log estruturado na aplicação

A aplicação utiliza a interface `IAppLogger` (abstração sobre o `ILogger` do .NET) para registrar logs estruturados via Serilog. Cada use case está dentro de uma lógica de try/catch que captura `DomainException` (regras de negócio) e `Exception` (erros inesperados), logando com contexto enriquecido.

Além dos logs de use case, os consumers de mensageria também logam cada mensagem recebida e processada, incluindo o `CorrelationId` e o `AnaliseDiagramaId` para rastreabilidade completa do pipeline.

## CorrelationId

O `CorrelationId` é gerado e propagado entre todas as APIs e mensageria. O middleware HTTP cuida do contexto de requisição, os filtros MassTransit cuidam do contexto de mensageria, e o `CorrelationIdEnricher` atua como fallback no Serilog.

Uma busca por `CorrelationId` no New Relic retorna todos os logs de todos os serviços envolvidos naquela operação, do upload até a geração do relatório.

## Custom events para o pipeline

O pipeline Upload → Processamento → Relatório é monitorado através de **custom events do New Relic**, registrados pela implementação `NewRelicMetricsService` (interface `IMetricsService`). Cada evento é enviado via `NewRelic.RecordCustomEvent()` com atributos contextuais incluindo `appName`, `CorrelationId` e `AnaliseDiagramaId`.

### Eventos de Upload

| Evento | Quando é disparado | Atributos |
|--------|-------------------|-----------|
| `UploadDiagramaRecebido` | Requisição de upload recebida | `AnaliseDiagramaId`, `CorrelationId` |
| `UploadDiagramaAceito` | Upload validado e aceito (segurança OK) | `AnaliseDiagramaId`, `CorrelationId` |
| `UploadDiagramaRejeitado` | Upload rejeitado por falha de segurança (malware, tipo inválido) | `AnaliseDiagramaId`, `Motivo`, `CorrelationId` |
| `UploadDiagramaComFalha` | Erro interno durante o upload | `AnaliseDiagramaId`, `OrigemErro`, `CorrelationId` |
| `TentativaUploadRepetida` | Tentativa de upload duplicado para mesmo diagrama | `AnaliseDiagramaId`, `Tentativas`, `CorrelationId` |

### Eventos de Processamento

| Evento | Quando é disparado | Atributos |
|--------|-------------------|-----------|
| `ProcessamentoDiagramaIniciado` | Processamento do diagrama iniciado | `AnaliseDiagramaId`, `CorrelationId` |
| `ProcessamentoDiagramaConcluido` | LLM concluiu a análise com sucesso | `AnaliseDiagramaId`, `DuracaoMs`, `CorrelationId` |
| `ProcessamentoDiagramaFalha` | Erro na chamada ao LLM ou processamento | `AnaliseDiagramaId`, `OrigemErro`, `CorrelationId` |
| `ProcessamentoDiagramaRejeitado` | LLM retornou resposta inválida (validação falhou) | `AnaliseDiagramaId`, `OrigemRejeicao`, `CorrelationId` |

### Eventos de Relatório

| Evento | Quando é disparado | Atributos |
|--------|-------------------|-----------|
| `AnaliseDiagramaRecebida` | Resultado de análise recebido pelo serviço de Relatório | `AnaliseDiagramaId`, `CorrelationId` |
| `AnaliseDiagramaConcluida` | Análise persistida com sucesso no banco | `AnaliseDiagramaId`, `CorrelationId` |
| `FalhaProcessamentoRecebida` | Mensagem de falha recebida do Processamento | `AnaliseDiagramaId`, `CorrelationId` |
| `RejeicaoProcessamentoRecebida` | Mensagem de rejeição recebida do Processamento | `AnaliseDiagramaId`, `CorrelationId` |
| `RejeicaoUploadRecebida` | Mensagem de rejeição recebida do Upload | `AnaliseDiagramaId`, `CorrelationId` |
| `RelatorioGerado` | Relatório gerado com sucesso (PDF, Markdown ou JSON) | `AnaliseDiagramaId`, `TipoRelatorio`, `CorrelationId` |
| `RelatorioComFalha` | Erro ao gerar relatório | `AnaliseDiagramaId`, `CorrelationId` |

## Dashboard New Relic

### Dashboard completo

O dashboard possui duas variáveis seletoras: **Serviço** (filtra por aplicação) e **AnaliseDiagramaId** (filtra por um diagrama específico).

![Dashboard Geral](Anexos/nr_dashboard_geral.png)

### Variáveis e filtros

- **Serviço** (`{{servico}}`): permite escolher entre `UploadService`, `ProcessamentoService` e `RelatorioService`
- **AnaliseDiagramaId** (`{{analiseDiagramaId}}`): campo texto para filtrar por um diagrama específico

![Filtros e Seletores](Anexos/nr_filtro_seletor_servicos_analise_diagrama_id.png)

### Widgets

**Recursos K8s + HPA**

Tabela consolidada que exibe, para cada container (`upload-service`, `processamento-service`, `relatorio-service`), o status, consumo percentual de CPU e Memória, réplicas ativas, limite HPA e contagem de restarts. Possui thresholds de warning (70%) e critical (90%) para CPU e Memória, e alerta de restarts.

![Recursos K8s + HPA](Anexos/nr_recursos_k8s_hpa.png)

```sql
SELECT latest(status) AS 'Status', (max(cpuUsedCores) / max(cpuLimitCores)) * 100 AS 'CPU%', (max(memoryWorkingSetBytes) / max(memoryLimitBytes)) * 100 AS 'Mem%', uniqueCount(podName) AS 'Réplicas Atuais', 5 AS 'Réplicas Máximas', sum(restartCount) AS 'Restarts', latest(podName) AS 'Pod' FROM K8sContainerSample WHERE containerName IN ('upload-service', 'processamento-service', 'relatorio-service') AND status = 'Running' FACET containerName AS 'Container' SINCE 1 minute ago
```

**Latência geral (P50 / P90 / P99)**

Gráfico de linhas com os percentis de latência das transações HTTP e do tempo de processamento LLM. Mostra duas séries: latência das APIs (Transaction) e latência das chamadas ao LLM (`ProcessamentoDiagramaConcluido.DuracaoMs`).

![Latência Geral](Anexos/nr_latencia_geral.png)

```sql
SELECT percentile(duration, 50) * 1000 AS 'P50 (ms)', percentile(duration, 90) * 1000 AS 'P90 (ms)', percentile(duration, 99) * 1000 AS 'P99 (ms)' FROM Transaction WHERE appName IN ({{servico}}) SINCE 1 day ago TIMESERIES AUTO
```

```sql
SELECT percentile(DuracaoMs, 50) AS 'LLM P50 (ms)', percentile(DuracaoMs, 90) AS 'LLM P90 (ms)', percentile(DuracaoMs, 99) AS 'LLM P99 (ms)' FROM ProcessamentoDiagramaConcluido WHERE appName IN ({{servico}}) SINCE 1 day ago TIMESERIES AUTO
```

**Volume de uploads**

Billboard com contadores de uploads recebidos, aceitos, rejeitados por segurança e repetidos. Thresholds de warning e critical para rejeições de segurança.

![Volume de Uploads](Anexos/nr_volume_uploads.png)

```sql
SELECT count(*) AS 'Recebidos' FROM UploadDiagramaRecebido WHERE appName IN ({{servico}}) SINCE 1 day ago
```

```sql
SELECT count(*) AS 'Aceitos' FROM UploadDiagramaAceito WHERE appName IN ({{servico}}) SINCE 1 day ago
```

```sql
SELECT count(*) AS 'Rejeitados (Segurança)' FROM UploadDiagramaRejeitado WHERE appName IN ({{servico}}) SINCE 1 day ago
```

```sql
SELECT count(*) AS 'Repetidos' FROM TentativaUploadRepetida WHERE appName IN ({{servico}}) SINCE 1 day ago
```

**Funil do pipeline (IDs únicos)**

Billboard que mostra o funil de conversão do pipeline por IDs únicos de `AnaliseDiagramaId`: uploads aceitos → processamentos iniciados → rejeitados pelo LLM → análises concluídas → relatórios gerados. Permite identificar onde o pipeline está perdendo diagramas.

![Funil do Pipeline](Anexos/nr_funil_pipeline.png)

```sql
SELECT filter(uniqueCount(AnaliseDiagramaId), WHERE eventType() = 'UploadDiagramaAceito') AS '1. Uploads Aceitos', filter(uniqueCount(AnaliseDiagramaId), WHERE eventType() = 'ProcessamentoDiagramaIniciado') AS '2. Proc. Iniciados', filter(uniqueCount(AnaliseDiagramaId), WHERE eventType() = 'ProcessamentoDiagramaRejeitado') AS '3. Rejeitados (LLM)', filter(uniqueCount(AnaliseDiagramaId), WHERE eventType() = 'AnaliseDiagramaConcluida') AS '4. Análises Concluídas', filter(uniqueCount(AnaliseDiagramaId), WHERE eventType() = 'RelatorioGerado') AS '5. Relatório Gerado (≥1)' FROM UploadDiagramaAceito, ProcessamentoDiagramaIniciado, ProcessamentoDiagramaRejeitado, AnaliseDiagramaConcluida, RelatorioGerado WHERE appName IN ({{servico}}) SINCE 1 day ago
```

**Volume do pipeline ao longo do tempo**

Gráfico de barras empilhadas que mostra o volume de eventos do pipeline ao longo do tempo: uploads aceitos, processamentos iniciados, concluídos, rejeitados e relatórios gerados.

![Volume do Pipeline ao Longo do Tempo](Anexos/nr_volume_pipeline_longo_do_tempo.png)

```sql
SELECT filter(count(*), WHERE eventType() = 'UploadDiagramaAceito') AS 'Upload Aceito', filter(count(*), WHERE eventType() = 'ProcessamentoDiagramaIniciado') AS 'Proc. Iniciado', filter(count(*), WHERE eventType() = 'ProcessamentoDiagramaConcluido') AS 'Proc. Concluído', filter(count(*), WHERE eventType() = 'ProcessamentoDiagramaRejeitado') AS 'Proc. Rejeitado', filter(count(*), WHERE eventType() = 'RelatorioGerado') AS 'Relatório Gerado' FROM UploadDiagramaAceito, ProcessamentoDiagramaIniciado, ProcessamentoDiagramaConcluido, ProcessamentoDiagramaRejeitado, RelatorioGerado WHERE appName IN ({{servico}}) SINCE 1 day ago TIMESERIES AUTO
```

**Falhas e rejeições**

Billboard consolidado que contabiliza erros HTTP 5xx, falhas de upload, falhas de processamento, rejeições do LLM e falhas de relatório. Thresholds de warning e critical para cada tipo.

![Falhas e Rejeições](Anexos/nr_falhas_rejeicoes.png)

```sql
SELECT count(*) AS '5xx HTTP' FROM Transaction WHERE (httpResponseCode LIKE '5%' OR http.statusCode >= 500) AND appName IN ({{servico}}) SINCE 1 day ago
```

```sql
SELECT count(*) AS 'Upload Falha' FROM UploadDiagramaComFalha WHERE appName IN ({{servico}}) SINCE 1 day ago
```

```sql
SELECT count(*) AS 'Proc. Falha' FROM ProcessamentoDiagramaFalha WHERE appName IN ({{servico}}) SINCE 1 day ago
```

```sql
SELECT count(*) AS 'Proc. Rejeitado' FROM ProcessamentoDiagramaRejeitado WHERE appName IN ({{servico}}) SINCE 1 day ago
```

```sql
SELECT count(*) AS 'Relatório Falha' FROM RelatorioComFalha WHERE appName IN ({{servico}}) SINCE 1 day ago
```

**Falhas vs rejeições por etapa**

Gráfico de barras empilhadas que separa falhas (erros de infraestrutura) de rejeições (validação do LLM) ao longo do tempo, permitindo identificar se os problemas são de infra ou de qualidade das respostas do LLM.

![Falhas vs Rejeições por Etapa](Anexos/nr_falhas_rejeicoes_por_etapa.png)

```sql
SELECT filter(count(*), WHERE eventType() = 'UploadDiagramaComFalha') AS 'Upload Falha (Infra)', filter(count(*), WHERE eventType() = 'ProcessamentoDiagramaFalha') AS 'Proc. Falha (LLM)', filter(count(*), WHERE eventType() = 'ProcessamentoDiagramaRejeitado') AS 'Proc. Rejeitado (LLM Validação)', filter(count(*), WHERE eventType() = 'RelatorioComFalha') AS 'Relatório Falha' FROM UploadDiagramaComFalha, ProcessamentoDiagramaFalha, ProcessamentoDiagramaRejeitado, RelatorioComFalha WHERE appName IN ({{servico}}) SINCE 1 day ago TIMESERIES AUTO
```

**Logs de erro**

Tabela com os últimos 100 logs de nível Error, exibindo timestamp, mensagem, produtor (aplicação / UseCase / Mensageria), `AnaliseDiagramaId`, `CorrelationId` e `trace.id`.

![Logs de Erro](Anexos/nr_logs_de_erro.png)

```sql
SELECT timestamp, message, concat(if(application IS NULL, '-', application), ' / ', if(UseCase IS NULL, '-', UseCase), ' / ', if(Mensageria IS NULL, '-', Mensageria)) AS 'Produtor', AnaliseDiagramaId, CorrelationId, trace.id FROM Log WHERE level IN ('Error', 'ERROR', 'error', '4', 4) AND application IN ({{servico}}) ORDER BY timestamp DESC SINCE 30 days ago LIMIT 100
```

**Logs de warning**

Tabela semelhante aos logs de erro, mas para nível Warning. Inclui também `DomainErrorType` para identificar erros de domínio tratados.

![Logs de Warning](Anexos/nr_logs_de_warning.png)

```sql
SELECT timestamp, message, concat(if(application IS NULL, '-', application), ' / ', if(UseCase IS NULL, '-', UseCase), ' / ', if(Mensageria IS NULL, '-', Mensageria)) AS 'Produtor', DomainErrorType, AnaliseDiagramaId, CorrelationId, trace.id FROM Log WHERE level IN ('Warning', 'WARNING', 'warning', 'Warn', 'WARN', '3', 3) AND application IN ({{servico}}) ORDER BY timestamp DESC SINCE 30 days ago LIMIT 100
```

**Erros de domínio / use case**

Tabela agrupada por mensagem, mostrando ocorrências de erros de domínio com `DomainErrorType`, serviço e use case de origem. Filtra logs de Warning e Information que possuem `DomainErrorType`.

![Erros de Domínio / Use Case](Anexos/nr_erros_dominio.png)

```sql
SELECT count(*) AS 'Ocorrências', latest(DomainErrorType) AS 'Tipo', latest(application) AS 'Serviço', latest(UseCase) AS 'UseCase' FROM Log WHERE level IN ('Warning', 'WARNING', 'warning', 'Warn', 'WARN', '3', 3, 'Information', 'INFORMATION', 'information', '2', 2) AND DomainErrorType IS NOT NULL AND application IN ({{servico}}) FACET message AS 'Mensagem' SINCE 1 day ago LIMIT 50
```

**Timeline de eventos por AnaliseDiagramaId**

Tabela que exibe **todos** os 16 custom events do pipeline em ordem cronológica, filtráveis por `AnaliseDiagramaId`. Para cada evento mostra timestamp, tipo, serviço, mensagem e extras (origem do erro, tipo de rejeição, tipo de relatório, duração, tentativas). É o widget mais útil para rastrear o ciclo de vida completo de uma análise.

![Timeline de Eventos](Anexos/nr_timeline_eventos_por_analise_diagrama_id.png)

```sql
SELECT timestamp, eventType() AS 'Evento', appName AS 'Serviço', Motivo AS 'Mensagem', concat(if(OrigemErro IS NOT NULL, concat('Origem: ', OrigemErro, ' | '), ''), if(OrigemRejeicao IS NOT NULL, concat('Rejeição: ', OrigemRejeicao, ' | '), ''), if(TipoRelatorio IS NOT NULL, concat('Tipo: ', TipoRelatorio, ' | '), ''), if(DuracaoMs IS NOT NULL, concat(DuracaoMs, 'ms | '), ''), if(Tentativas IS NOT NULL, concat(Tentativas, ' tent.'), '')) AS 'Extras', AnaliseDiagramaId, CorrelationId FROM UploadDiagramaRecebido, UploadDiagramaAceito, UploadDiagramaRejeitado, UploadDiagramaComFalha, TentativaUploadRepetida, ProcessamentoDiagramaIniciado, ProcessamentoDiagramaConcluido, ProcessamentoDiagramaFalha, ProcessamentoDiagramaRejeitado, AnaliseDiagramaRecebida, AnaliseDiagramaConcluida, FalhaProcessamentoRecebida, RejeicaoProcessamentoRecebida, RejeicaoUploadRecebida, RelatorioGerado, RelatorioComFalha WHERE ({{analiseDiagramaId}} = '%' OR AnaliseDiagramaId = {{analiseDiagramaId}}) AND appName IN ({{servico}}) ORDER BY timestamp DESC SINCE 30 days ago LIMIT 200
```

---
Anterior: [CI/CD](../08%20-%20CI%20%26%20CD/1_ci_cd.md)  
Próximo: [Qualidade - Upload](../10%20-%20Testes%20e%20qualidade/1_qualidade_upload.md)
