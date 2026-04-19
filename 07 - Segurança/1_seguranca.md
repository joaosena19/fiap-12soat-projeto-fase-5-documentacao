# Segurança

## Validação de arquivos

O upload de arquivos passa por um pipeline de segurança em múltiplas camadas. A validação acontece em dois momentos: primeiro no domínio (durante a criação do aggregate), depois na infraestrutura (validação avançada de assinatura, conteúdo e malware).

### Validação simples (Domínio)

Na criação do aggregate `UploadDiagrama`, os value objects aplicam as primeiras regras de segurança antes de qualquer processamento:

- **NomeOriginal**: rejeita nomes vazios, com mais de 255 caracteres, com bytes nulos, caracteres de controle, e bloqueia tentativas de path traversal (`..`, `/`, `\`). Também rejeita caracteres inválidos cross-platform (`<`, `>`, `:`, `"`, `|`, `?`, `*`).
- **ExtensaoInformada**: valida que a extensão do arquivo corresponde ao formato MIME declarado. Apenas formatos de imagem (`.png`, `.jpg`, `.jpeg`, `.gif`, `.bmp`, `.webp`) e PDF (`.pdf`) são aceitos. Se o `Content-Type` diz `image/png` mas o arquivo tem extensão `.exe`, o upload é rejeitado.
- **Tamanho**: rejeita arquivos vazios e arquivos que excedem 5 MB.

Estas validações lançam `DomainException` e impedem que o arquivo sequer chegue ao pipeline de segurança avançado.

### Validação avançada (Infraestrutura)

Após a criação do aggregate, o `ValidacaoSegurancaArmazenamentoService` executa três validações em sequência:

**1. Validação de assinatura (magic bytes)**

O `ValidadorAssinaturaArquivo` lê os primeiros bytes do arquivo e compara com as assinaturas conhecidas de cada formato (usando a biblioteca `FileSignatures`).

Para WebP, a validação é feita manualmente pois a biblioteca não suporta o formato. O validador verifica os bytes RIFF (`0x52 0x49 0x46 0x46`) e WEBP (`0x57 0x45 0x42 0x50`) nos offsets corretos do header.

Se a assinatura detectada não corresponde ao media type esperado para a extensão, o erro `AssinaturaArquivoInvalida` é registrado.

**2. Validação de conteúdo**

O `ValidadorConteudoArquivo` analisa o conteúdo interno do arquivo de acordo com seu tipo:

**Imagens** (`ValidadorConteudoImagem`): carrega a imagem com ImageSharp e inspeciona os metadados EXIF e XMP em busca de padrões XSS (`<script`, `javascript:`, `onerror=`, `eval(`, `document.cookie`, etc.). Se os metadados contêm conteúdo suspeito, o erro `ConteudoSuspeito` é registrado. Se a imagem não carregar, é considerada corrompida (`ArquivoCorrompido`).

**PDFs** (`ValidadorConteudoPdf`): abre o PDF com PdfPig e verifica se possui ao menos uma página. Em seguida, analisa o conteúdo raw do PDF procurando padrões perigosos como `/JavaScript`, `/OpenAction`, `/Launch`, `/SubmitForm`, `/EmbeddedFile`, `/RichMedia` e `/XFA`. Se encontrados, o erro `ConteudoSuspeito` é registrado.

#### 3. Detecção de malware (ClamAV)

O `VerificadorMalwareArquivo` envia o arquivo para o ClamAV via protocolo clamd (biblioteca `nClam`). O ClamAV roda como um pod separado no Kubernetes, acessível pelo service `clamav-service` na porta `3310`.

Os resultados possíveis são:

| Resultado | Comportamento |
|-----------|---------------|
| `Clean` | Arquivo limpo, prossegue normalmente |
| `VirusDetected` | Registra o nome do vírus detectado e adiciona erro `MalwareDetectado` |
| `Error` | Lança `InvalidOperationException` — upload bloqueado |
| Resultado desconhecido | Lança `InvalidOperationException` — upload bloqueado |

### Fail Closed: ClamAV indisponível

Se o ClamAV estiver indisponível, o `VerificadorMalwareArquivo` lança `InvalidOperationException` e o upload retorna erro 500. O arquivo não é armazenado nem processado — a aplicação adota **Fail Closed**.

O deployment do ClamAV possui liveness e readiness probes na porta 3310, com `initialDelaySeconds` de 120s e 90s respectivamente pois o ClamAV precisa baixar as definições de vírus antes de ficar pronto.

## Hash SHA-256 e deduplicação

Antes de qualquer validação de segurança, o `GeradorHashSha256` calcula o hash SHA-256 do conteúdo do arquivo. O hash é armazenado no aggregate `UploadDiagrama` e persistido no banco de dados.

Quando um novo upload chega, o use case consulta o banco pelo hash. Se já existe um registro com o mesmo hash:

- **Se o upload anterior foi aceito**: retorna os dados existentes e republica a mensagem de processamento. Nenhuma revalidação de segurança é necessária.
- **Se o upload anterior foi rejeitado**: retorna os dados existentes informando que o arquivo já foi rejeitado. Não permite reenvio do mesmo arquivo malicioso.

## Comunicação entre serviços

### Mensageria (SQS/SNS)

A comunicação entre os três serviços (Upload → Processamento → Relatório) é feita via MassTransit com Amazon SQS/SNS. As credenciais AWS são injetadas via Kubernetes Secrets no deploy, e o acesso aos tópicos e filas é controlado por IAM roles associadas aos pods via IRSA (IAM Roles for Service Accounts).

Os serviços não se comunicam diretamente por HTTP entre si — o único canal inter-serviço é a mensageria.

### Autenticação JWT

A API de Upload expõe o endpoint de geração de token JWT. A API de Relatório exige autenticação JWT em todos os endpoints. O token é assinado com HMAC-SHA256 e a chave simétrica é compartilhada entre os serviços via Kubernetes Secret.

O serviço de Processamento é um worker e não expõe API, portanto não participa do fluxo de autenticação HTTP.

### CorrelationId

Todas as requisições e mensagens carregam um `CorrelationId` (UUID) que permite rastrear uma operação do upload até a geração do relatório. Essencial para auditoria e investigação de incidentes.

## Riscos e limitações

### Chave JWT simétrica compartilhada
A chave de assinatura JWT é simétrica (HMAC-SHA256). Qualquer serviço que possua a chave pode gerar tokens válidos. Se a chave for comprometida, um atacante pode forjar tokens. O uso de chaves assimétricas (RS256) seria mais seguro, porém mais complexo de gerenciar neste cenário.

### Endpoints de Upload sem autenticação
Os endpoints `POST /api/upload/diagrama` e `GET /api/upload/diagramas` não exigem autenticação. Um atacante pode enviar arquivos sem se autenticar. A proteção fica nas camadas de validação de segurança (tamanho, formato, assinatura, conteúdo, malware) e na deduplicação por hash. Um rate limiter ajudaria a mitigar ataques de volumetria, mas não foi implementado.

### ClamAV como ponto único de falha
Se o pod do ClamAV ficar indisponível, nenhum upload é processado (Fail Closed). O deployment usa uma única réplica. Em cenários de alta disponibilidade, seria necessário mais réplicas ou um serviço de scan externo.

### Definições de vírus
O ClamAV baixa as definições de vírus na inicialização do pod, usando `emptyDir` como volume. Se o pod reiniciar, as definições são baixadas novamente. Não há persistência das definições entre restarts, o que pode causar uma janela de indisponibilidade durante o download.

### Comunicação interna sem TLS
A comunicação entre o pod de Upload e o ClamAV (`clamav-service:3310`) acontece sem TLS, pois ambos estão no mesmo cluster Kubernetes e o protocolo clamd não suporta TLS nativamente. Se o cluster for compartilhado com outros tenants, isso pode ser um risco.

### Sem WAF ou rate limiting
Não há Web Application Firewall (WAF) nem rate limiting na frente das APIs. A mitigação de DDoS e ataques de volumetria depende inteiramente da infraestrutura AWS (ELB, security groups).

---
Anterior: [Comunicação entre serviços](../06%20-%20Comunicação%20entre%20serviços/1_comunicacao_entre_servicos.md)  
Próximo: [CI/CD](../08%20-%20CI%20%26%20CD/1_ci_cd.md)
