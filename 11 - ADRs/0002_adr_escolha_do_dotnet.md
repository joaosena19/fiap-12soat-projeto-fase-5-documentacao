# ADR 0002 - Escolha do .NET

## Status

Aceito

## Contexto

Era necessário escolher a linguagem e o framework para os três microsserviços do projeto: Upload, Processamento e Relatório.

## Discussão e possibilidades

Tenho familiaridade com .NET e já era a stack das fases anteriores do projeto. O ecossistema é maduro para os três perfis de serviço que o projeto precisa: APIs REST (Upload e Relatório), Workers (Processamento) e Entity Framework Core para acesso a dados.

Para o serviço de Processamento, considerei brevemente outras linguagens como Python, que é mais comum em projetos de IA. Contudo, o processamento da IA é delegado para um serviço externo (Google Gemini API), logo a linguagem do host não é relevante — o serviço apenas monta a requisição HTTP e interpreta a resposta.

Alternativas como Java/Spring, Node.js e Go não ofereciam vantagem que justificasse a troca. A produtividade com .NET é alta e não havia motivo para mudar.

## Decisão

Foi decidido utilizar .NET 10 (LTS) como linguagem e framework para os três microsserviços.

## Consequências

**Positivas:**

* Produtividade imediata — sem curva de aprendizado.
* Ecossistema maduro para APIs, Workers e EF Core com PostgreSQL.
* `Microsoft.Extensions.AI` permite integração com a LLM de forma desacoplada do provider.

**Negativas:**

* .NET não é a escolha mais comum para projetos de IA, o que pode gerar estranhamento. Na prática, isso não é um problema pois a IA é delegada para um serviço externo.

---
Anterior: [ADR 0001 - AWS como Cloud Provider](0001_adr_aws_como_cloud_provider.md)  
Próximo: [ADR 0003 - Divisão em Microsserviços](0003_adr_divisao_em_microsservicos.md)
