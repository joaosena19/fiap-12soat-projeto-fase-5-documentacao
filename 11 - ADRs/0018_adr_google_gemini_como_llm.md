# ADR 0018 - Google Gemini como LLM

## Status

Aceito

## Contexto

O serviço de Processamento precisa de uma LLM multimodal para analisar imagens de diagramas de arquitetura. Era necessário escolher o provider.

## Discussão e possibilidades

O Google Gemini oferece modelos multimodais (texto + imagem) com tier gratuito generoso. Iniciei com o Gemini e os resultados foram excelentes mesmo no tier gratuito — não cheguei a testar outros providers pois não havia motivo.

O Gemini tem suporte nativo a structured output (JSON schema), forçando a LLM a retornar exatamente os campos esperados. Também tem suporte a OCR diretamente na análise de imagens.

Para contornar os limites de requisições do tier gratuito, configurei 4 modelos como fallback: `gemini-3.1-flash-lite-preview`, `gemini-3-flash-preview`, `gemini-2.5-flash` e `gemini-2.5-flash-lite`. Se um modelo retorna 429 (rate limit) ou 503 (indisponível), o sistema avança para o próximo.

A integração usa a biblioteca `GenerativeAI.Microsoft`, que implementa a interface `IChatClient` do `Microsoft.Extensions.AI`. Trocar para OpenAI ou Anthropic exigiria apenas mudar a factory.

Alternativas descartadas:

- **OpenAI GPT-4o:** pago, sem free tier suficiente para um projeto acadêmico.
- **Claude (Anthropic):** pago, mesma razão.
- **AWS Bedrock:** custo adicional.
- **Modelos locais:** infraestrutura pesada para hospedar, sem benefício para o escopo do projeto.

## Decisão

Foi decidido utilizar o Google Gemini como LLM para análise de diagramas, com 4 modelos em fallback para contornar limites do tier gratuito.

Veja mais em [Funcionamento e fluxos - Processamento](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md).

## Consequências

**Positivas:**

* Tier gratuito generoso para uso acadêmico.
* Qualidade excelente de análise mesmo nos modelos gratuitos.
* Structured output e OCR nativos.
* Desacoplamento do provider via `Microsoft.Extensions.AI`.

**Negativas:**

* Rate limits no tier gratuito — mitigado pela estratégia de 4 modelos em fallback.
* Dependência de serviço externo — se o Gemini ficar indisponível, o processamento para. Na prática, os 4 modelos em fallback tornam isso raro.

---
Anterior: [ADR 0017 - ClamAV como Serviço Dedicado no Cluster](0017_adr_clamav_como_servico_dedicado.md)  
Próximo: [ADR 0019 - DomainException como Error no Processamento](0019_adr_domain_exception_como_error_no_processamento.md)
