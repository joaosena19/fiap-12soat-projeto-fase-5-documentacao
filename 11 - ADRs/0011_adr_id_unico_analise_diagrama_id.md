# ADR 0011 - ID Único por Todo o Sistema (AnaliseDiagramaId)

## Status

Aceito

## Contexto

Cada diagrama percorre três serviços: Upload → Processamento → Relatório. Era necessário decidir como correlacionar os dados entre eles.

## Discussão e possibilidades

Optei por gerar um único GUID (`AnaliseDiagramaId`) no Upload e propagá-lo por todo o sistema. Cada serviço usa esse ID como chave de correlação, com unique constraint no banco. Dado um `AnaliseDiagramaId`, é possível consultar o estado do diagrama em qualquer serviço.

A alternativa seria cada serviço gerar seu próprio ID e manter um mapeamento via lookup (ex: Processamento busca "qual é o meu ProcessamentoId local para este UploadId do Upload?"). Isso adicionaria complexidade sem benefício — o ID único resolve o problema de forma mais simples e direta.

## Decisão

Foi decidido utilizar um ID único (`AnaliseDiagramaId`) gerado no Upload e propagado por todo o sistema como chave de correlação.

## Consequências

**Positivas:**

* Rastreabilidade ponta a ponta com um único identificador.
* Consulta simples em qualquer serviço pelo mesmo ID.
* Unique constraint protege contra duplicatas.

**Negativas:**

* O Upload é responsável por gerar o ID para todo o sistema — se a lógica de geração mudar, impacta a correlação nos outros serviços.

---
Anterior: [ADR 0010 - MassTransit como Abstração de Mensageria](0010_adr_masstransit_como_abstracao.md)  
Próximo: [ADR 0012 - Emissão de Token no Upload](0012_adr_emissao_de_token_no_upload.md)
