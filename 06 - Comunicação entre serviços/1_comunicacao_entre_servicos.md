# Comunicação entre serviços

## Visão geral

Os três serviços (Upload, Processamento, Relatório) se comunicam exclusivamente por mensageria assíncrona via **MassTransit** com **Amazon SNS/SQS**. Não existe comunicação HTTP direta entre eles.

O diagrama C2 (Containers) mostra as conexões entre os serviços:

![Diagrama C2 - Containers](../02%20-%20Diagrama%20de%20componentes/Anexos/c2.svg)

## Tecnologias

- **MassTransit** como abstração de mensageria sobre Amazon SQS/SNS
- **Amazon SNS** (Simple Notification Service) como tópicos pub/sub
- **Amazon SQS** (Simple Queue Service) como filas de consumo
- **JSON** como formato de serialização (camelCase, com `JsonStringEnumConverter`)
- **CorrelationId** propagado automaticamente via filtros MassTransit (`PublishCorrelationIdFilter`, `SendCorrelationIdFilter`, `ConsumeCorrelationIdFilter`)

## Padrão de comunicação: SNS → SQS (fan-out)

Todos os eventos seguem o padrão SNS → SQS. O serviço produtor publica no tópico SNS, e cada serviço consumidor tem sua própria fila SQS subscrita ao tópico.

O caso mais relevante de fan-out é o tópico `UploadDiagramaConcluido`: quando o Upload publica neste tópico, **duas filas** recebem a mensagem — uma para o Processamento e outra para o Relatório.

## Fluxo do pipeline

```
Upload ──► SNS: UploadDiagramaConcluido ──┬──► SQS (Processamento): inicia análise LLM
                                           └──► SQS (Relatório): cria registro inicial

Upload ──► SNS: UploadDiagramaRejeitado ──────► SQS (Relatório): registra rejeição

Processamento ──► SNS: ProcessamentoDiagramaIniciado ──► SQS (Relatório): atualiza status

Processamento ──► SNS: ProcessamentoDiagramaAnalisado ──► SQS (Relatório): registra análise + gera relatórios

Processamento ──► SNS: ProcessamentoDiagramaErro ──► SQS (Relatório): registra falha/rejeição

Relatório ──► SNS: SolicitarGeracaoRelatorios ──► SQS (Relatório): gera relatórios (uso interno)
```

## Mensagens

### Upload → Processamento / Relatório

#### UploadDiagramaConcluidoDto

Publicada quando o upload é aceito. Consumida pelo Processamento e pelo Relatório.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `CorrelationId` | `string` | ID de rastreamento da operação |
| `AnaliseDiagramaId` | `Guid` | Identificador único da análise |
| `NomeOriginal` | `string` | Nome original do arquivo enviado |
| `Extensao` | `string` | Extensão do arquivo (`.png`, `.pdf`, etc.) |
| `Tamanho` | `long` | Tamanho em bytes |
| `Hash` | `string` | SHA-256 do conteúdo |
| `NomeFisico` | `string` | Nome do arquivo no S3 |
| `LocalizacaoUrl` | `string` | URL do arquivo no S3 |
| `DataCriacao` | `DateTimeOffset` | Timestamp de criação |

#### UploadDiagramaRejeitadoDto

Publicada quando o upload é rejeitado por falha de segurança. Consumida apenas pelo Relatório.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `CorrelationId` | `string` | ID de rastreamento da operação |
| `AnaliseDiagramaId` | `Guid` | Identificador único da análise |
| `MotivoRejeicao` | `string` | Motivo da rejeição (erros concatenados) |
| `DataRejeicao` | `DateTimeOffset` | Timestamp da rejeição |

### Processamento → Relatório

#### ProcessamentoDiagramaIniciadoDto

Publicada quando o processamento começa. O Relatório atualiza o status para "em processamento".

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `CorrelationId` | `string` | ID de rastreamento |
| `AnaliseDiagramaId` | `Guid` | Identificador da análise |
| `NomeOriginal` | `string` | Nome original do arquivo |
| `Extensao` | `string` | Extensão do arquivo |
| `DataInicio` | `DateTimeOffset` | Timestamp de início |

#### ProcessamentoDiagramaAnalisadoDto

Publicada quando o LLM conclui a análise com sucesso. O Relatório registra a análise e dispara a geração de relatórios.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `CorrelationId` | `string` | ID de rastreamento |
| `AnaliseDiagramaId` | `Guid` | Identificador da análise |
| `DescricaoAnalise` | `string` | Descrição geral da análise feita pelo LLM |
| `ComponentesIdentificados` | `List<string>` | Componentes identificados no diagrama |
| `RiscosArquiteturais` | `List<string>` | Riscos arquiteturais identificados |
| `RecomendacoesBasicas` | `List<string>` | Recomendações de melhoria |
| `DataConclusao` | `DateTimeOffset` | Timestamp de conclusão |

#### ProcessamentoDiagramaErroDto

Publicada quando o processamento falha (erro no LLM, resposta inválida, etc.). Inclui informações de retry.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `CorrelationId` | `string` | ID de rastreamento |
| `AnaliseDiagramaId` | `Guid` | Identificador da análise |
| `Motivo` | `string` | Descrição do erro |
| `OrigemErro` | `string?` | Origem do erro (ex: "Processamento") |
| `TentativasRealizadas` | `int` | Número de tentativas feitas |
| `Rejeitado` | `bool` | Se a análise foi definitivamente rejeitada |
| `PodeRetentar` | `bool` | Se é possível retentar o processamento |
| `DataErro` | `DateTimeOffset` | Timestamp do erro |

### Relatório → Relatório (interno)

#### SolicitarGeracaoRelatoriosDto

Publicada pelo próprio serviço de Relatório após registrar uma análise concluída. Desacopla o registro da análise da geração dos relatórios.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `CorrelationId` | `string` | ID de rastreamento |
| `AnaliseDiagramaId` | `Guid` | Identificador da análise |
| `TiposRelatorio` | `List<TipoRelatorioEnum>` | Tipos de relatório a gerar |

## Infraestrutura SNS/SQS

Todos os tópicos SNS e filas SQS são provisionados via Terraform no repositório de infraestrutura. As subscriptions SNS → SQS utilizam `raw_message_delivery = true` para evitar o envelope SNS e entregar o JSON diretamente.

### Tópicos SNS

| Tópico | Produtor | Filas SQS subscritas |
|--------|----------|---------------------|
| `UploadDiagramaConcluido` | Upload | Processamento, Relatório (fan-out) |
| `UploadDiagramaRejeitado` | Upload | Relatório |
| `ProcessamentoDiagramaIniciado` | Processamento | Relatório |
| `ProcessamentoDiagramaAnalisado` | Processamento | Relatório |
| `ProcessamentoDiagramaErro` | Processamento | Relatório |
| `SolicitarGeracaoRelatorios` | Relatório | Relatório (self-consume) |

### Configuração por serviço

**Upload**: publica em 2 tópicos, não consome filas. Configuração em `Mensageria:Topicos:UploadDiagramaConcluido` e `Mensageria:Topicos:UploadDiagramaRejeitado`.

**Processamento**: consome 1 fila (`UploadDiagramaConcluido` com `ConcurrentMessageLimit = 1` e `PrefetchCount = 1` para processamento sequencial), publica em 3 tópicos (Iniciado, Analisado, Erro).

**Relatório**: consome 6 filas (todas as mensagens do pipeline), publica em 1 tópico interno (SolicitarGeracaoRelatorios).

### Configurações de fila

| Configuração | Processamento | Demais filas |
|---|---|---|
| `visibility_timeout_seconds` | 600 (10 min) | 120 (2 min) |
| `message_retention_seconds` | 86400 (24h) | 86400 (24h) |
| `receive_wait_time_seconds` | 20 (long polling) | 20 (long polling) |

O visibility timeout da fila do Processamento é maior (10 minutos) pois a análise pelo LLM pode demorar.

## CorrelationId na mensageria

Cada mensagem publicada inclui o `CorrelationId` no payload. Os filtros MassTransit (`PublishCorrelationIdFilter`, `SendCorrelationIdFilter`, `ConsumeCorrelationIdFilter`) propagam o ID de correlação automaticamente entre todos os serviços.

---
Anterior: [Autenticação](../05%20-%20Autenticação/1_autenticacao.md)  
Próximo: [Segurança](../07%20-%20Segurança/1_seguranca.md)
