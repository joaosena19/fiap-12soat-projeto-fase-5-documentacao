# Arquitetura interna - Processamento

## Visão geral

O serviço de Processamento segue Clean Architecture com quatro projetos: Domain, Application, Infrastructure e Worker. A diferença fundamental em relação ao serviço de Upload é o ponto de entrada: aqui não existe API HTTP. O Worker consome mensagens via MassTransit/SQS, e o Consumer instancia os componentes de Clean Architecture da mesma forma que um endpoint faria.

![Estrutura da solution](estrutura_solution_processamento.png)

## Camadas

### Domain (Entities / Enterprise Business Rules)

O aggregate root `ProcessamentoDiagrama` implementa uma máquina de estados que governa todo o ciclo de vida do processamento. Os estados são: `Pendente`, `EmProcessamento`, `Concluido`, `Rejeitado` e `Erro`.

```csharp
[AggregateRoot]
public class ProcessamentoDiagrama
{
    public Guid Id { get; private set; }
    public Guid AnaliseDiagramaId { get; private set; }
    public StatusProcessamento StatusProcessamento { get; private set; } = null!;
    public DadosOrigem? DadosOrigem { get; private set; }
    public AnaliseResultado? AnaliseResultado { get; private set; }
    public TentativasRealizadas TentativasRealizadas { get; private set; } = null!;
    [...]

    public static ProcessamentoDiagrama Criar(Guid analiseDiagramaId)
    {
        return new ProcessamentoDiagrama(
            Uuid.NewSequential(),
            analiseDiagramaId,
            new StatusProcessamento(StatusProcessamentoEnum.Pendente),
            null,
            null,
            new TentativasRealizadas(0),
            new DataCriacao(DateTimeOffset.UtcNow),
            new DataAtualizacao(DateTimeOffset.UtcNow));
    }

    public void IniciarProcessamento()
    {
        StatusProcessamento = StatusProcessamento.TransicionarPara(StatusProcessamentoEnum.EmProcessamento);
        DataAtualizacao = new DataAtualizacao(DateTimeOffset.UtcNow);
    }

    public void ConcluirProcessamento(string descricaoAnalise, IReadOnlyCollection<string>? componentesIdentificados, IReadOnlyCollection<string>? riscosArquiteturais, IReadOnlyCollection<string>? recomendacoesBasicas, int tentativasRealizadas)
    {
        StatusProcessamento = StatusProcessamento.TransicionarPara(StatusProcessamentoEnum.Concluido);
        AnaliseResultado = AnaliseResultado.Criar(descricaoAnalise, componentesIdentificados, riscosArquiteturais, recomendacoesBasicas);
        TentativasRealizadas = new TentativasRealizadas(tentativasRealizadas);
        [...]
    }
    [...]
}
```

O value object `StatusProcessamento` encapsula as regras de transição de estado. Transições inválidas (como de `Concluido` para `Pendente`) lançam `DomainException`. A entidade `DadosOrigem` guarda referência ao arquivo no S3, e `AnaliseResultado` armazena a saída da LLM.

### Application (Use Cases / Application Business Rules)

O UseCase `ProcessarDiagramaUseCase` orquestra o fluxo completo: obtenção do aggregate, validação, chamada ao serviço de LLM e tratamento tripartido do resultado (sucesso, rejeição, falha).

```csharp
public class ProcessarDiagramaUseCase
{
    public async Task ExecutarAsync(ProcessarDiagramaDto processarDiagramaDto, IProcessamentoDiagramaGateway gateway, IDiagramaAnaliseService llmService, IProcessamentoDiagramaMessagePublisher messagePublisher, IMetricsService metrics, IAppLogger logger)
    {
        [...]
        var processamentoDiagrama = await ObterProcessamentoValidadoAsync(analiseDiagramaId, gateway);

        ValidarDadosObrigatorios(processarDiagramaDto, logger);

        await SinalizarInicioProcessamentoAsync(processamentoDiagrama, processarDiagramaDto, gateway, messagePublisher, metrics);

        var resultado = await llmService.AnalisarDiagramaAsync(analiseDiagramaId, processarDiagramaDto.NomeFisico, processarDiagramaDto.LocalizacaoUrl, processarDiagramaDto.Extensao);

        if (resultado.Sucesso)
            await TratarSucessoAsync(processamentoDiagrama, resultado, inicioProcessamento, gateway, messagePublisher, metrics, logger);
        else if (resultado.Rejeitado)
            await TratarRejeicaoAsync(processamentoDiagrama, resultado, gateway, messagePublisher, metrics, logger);
        else
            await TratarFalhaAsync(processamentoDiagrama, resultado, gateway, messagePublisher, metrics, logger);
    }
}
```

Os contratos de Application definem as interfaces que a camada de Infrastructure implementa:

```
Application/
├── Contracts/
│   ├── Gateways/
│   │   └── IProcessamentoDiagramaGateway.cs
│   ├── LLM/
│   │   └── IDiagramaAnaliseService.cs
│   ├── Messaging/
│   │   └── IProcessamentoDiagramaMessagePublisher.cs
│   └── Monitoramento/
│       ├── IAppLogger.cs
│       ├── ICorrelationIdAccessor.cs
│       └── IMetricsService.cs
├── ProcessamentoDiagrama/
│   ├── Dtos/
│   │   ├── ProcessarDiagramaDto.cs
│   │   └── ResultadoAnaliseDto.cs
│   └── UseCases/
│       └── ProcessarDiagramaUseCase.cs
```

Um aspecto importante: **não existem Presenters neste serviço**. O Worker não responde a um cliente HTTP — o resultado do processamento é comunicado via mensagens publicadas por `IProcessamentoDiagramaMessagePublisher`.

### Infrastructure (Interface Adapters — implementação)

O Handler funciona como o "Controller" da Clean Architecture. Ele vai além de simplesmente instanciar o UseCase — implementa lógica de **deduplicação** e **recuperação de dados** que pertence à fronteira entre infraestrutura e aplicação:

```csharp
public class ProcessamentoDiagramaHandler : BaseHandler
{
    private static readonly HashSet<StatusProcessamentoEnum> StatusTerminaisOuEmAndamento =
    [
        StatusProcessamentoEnum.EmProcessamento,
        StatusProcessamentoEnum.Concluido,
        StatusProcessamentoEnum.Rejeitado
    ];

    public async Task IniciarProcessamentoAsync(ProcessarDiagramaDto processarDiagramaDto, IProcessamentoDiagramaGateway gateway, IDiagramaAnaliseService llmService, IProcessamentoDiagramaMessagePublisher messagePublisher, IMetricsService metrics, IAppLogger logger)
    {
        var processamentoExistente = await gateway.ObterPorAnaliseDiagramaIdAsync(processarDiagramaDto.AnaliseDiagramaId);

        if (DeveIgnorar(processamentoExistente, processarDiagramaDto.AnaliseDiagramaId, logger))
            return;

        processarDiagramaDto = TentarRecuperarLocalizacaoUrl(processarDiagramaDto, processamentoExistente, logger);
        [...]

        await ProcessarDiagramaAsync(processarDiagramaDto, gateway, llmService, messagePublisher, metrics);
    }
    [...]
}
```

O Handler verifica se já existe um processamento em estado terminal (concluído, rejeitado, em andamento) e descarta duplicatas. Se a mensagem veio sem URL do arquivo (cenário de retry), tenta recuperar do registro já existente no banco.

O `DiagramaAnaliseService` é a implementação de `IDiagramaAnaliseService` e contém a integração com LLM. Ele implementa fallback entre múltiplos modelos de linguagem configurados e usa Polly para resiliência (retry com backoff exponencial, circuit breaker):

```csharp
public class DiagramaAnaliseService : IDiagramaAnaliseService
{
    [...]
    public async Task<ResultadoAnaliseDto> AnalisarDiagramaAsync(Guid analiseDiagramaId, string nomeFisico, string localizacaoUrl, string extensao)
    {
        var imagemBase64 = await _downloader.BaixarImagemComoBase64Async(localizacaoUrl);
        var prompt = MontarPromptAnalise(extensao);
        var tentativas = 0;

        foreach (var modeloConfig in _modelos)
        {
            tentativas++;
            [...]
            var resultado = await _pipeline.ExecuteAsync(async cancellationToken =>
            {
                return await ChamarLlmAsync(modeloConfig, prompt, imagemBase64, extensao, cancellationToken);
            }, cancellationToken);
            [...]
        }
        [...]
    }
}
```

### Worker (Frameworks & Drivers)

O ponto de entrada é o Consumer MassTransit. Assim como o endpoint no serviço de Upload instancia componentes diretamente, o Consumer faz o mesmo:

```csharp
public class UploadDiagramaConcluidoConsumer : IConsumer<UploadDiagramaConcluidoDto>
{
    private readonly AppDbContext _context;
    private readonly IDiagramaAnaliseService _llmService;
    private readonly IProcessamentoDiagramaMessagePublisher _messagePublisher;
    private readonly ILoggerFactory _loggerFactory;
    [...]

    public async Task Consume(ConsumeContext<UploadDiagramaConcluidoDto> context)
    {
        [...]
        var handler = new ProcessamentoDiagramaHandler(_loggerFactory);
        var gateway = new ProcessamentoDiagramaRepository(_context);
        var metrics = new NewRelicMetricsService();
        [...]

        await handler.IniciarProcessamentoAsync(processarDiagramaDto, gateway, _llmService, _messagePublisher, metrics, logger);
        [...]
    }
}
```

O `IDiagramaAnaliseService` é injetado via DI do .NET no Consumer por ser mais complexo (requer configuração de múltiplos modelos, pipeline do Polly, downloader). O Handler, Gateway e Metrics são instanciados diretamente.

O MassTransit é configurado com processamento sequencial (`ConcurrentMessageLimit = 1`, `PrefetchCount = 1`) pois cada mensagem envolve chamada a LLM.

## Fluxo completo — Processar Diagrama

1. A mensagem `UploadDiagramaConcluidoDto` chega via SQS
2. O Consumer instancia Handler, Gateway e Metrics
3. O Handler verifica deduplicação: se já existe processamento em estado terminal, ignora
4. O Handler tenta recuperar a URL do S3 caso a mensagem venha sem (cenário de retry)
5. O Handler cria o registro inicial com constraint check para evitar concorrência
6. O Handler instancia o UseCase e chama `ExecutarAsync`
7. O UseCase transiciona o aggregate para `EmProcessamento`, persiste e publica evento
8. O UseCase chama o serviço de LLM com fallback entre modelos
9. Conforme o resultado (sucesso/rejeição/falha), o aggregate transiciona de estado e publica a mensagem correspondente

---
Anterior: [Banco de dados - Processamento](../02%20-%20Banco%20de%20dados/1_banco_de_dados_processamento.md)  
Próximo: [Funcionamento e fluxos - Relatórios](../../03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
