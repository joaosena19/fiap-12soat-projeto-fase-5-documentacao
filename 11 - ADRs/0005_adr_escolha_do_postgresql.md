# ADR 0005 - Escolha do PostgreSQL

## Status

Aceito

## Contexto

Era necessário escolher o banco de dados para os três microsserviços. Os dados dos três serviços são naturalmente relacionais, e era necessário decidir qual engine utilizar.

## Discussão e possibilidades

Tenho afinidade com SQL e já usava PostgreSQL com Entity Framework Core nas fases anteriores. O PostgreSQL funciona muito bem com .NET via Npgsql e tem suporte nativo a JSONB, que é útil para armazenar campos de análise de IA com estrutura variável (Veja [ADR 0006 - Campos Mapeados como JSONB](0006_adr_campos_mapeados_como_jsonb.md)).

Considerei MongoDB como alternativa, pois os dados de análise de IA se encaixariam bem em documentos. Contudo, o MongoDB não está no free tier da AWS — o DocumentDB é pago e adicionar custo para algo que o PostgreSQL resolve com JSONB não faz sentido.

## Decisão

Foi decidido utilizar PostgreSQL no Amazon RDS para todos os três microsserviços, cada um com sua própria instância isolada.

## Consequências

**Positivas:**

* Gratuito e open-source.
* JSONB permite flexibilidade para campos de análise de IA sem tabelas filhas.
* EF Core com Npgsql é maduro e bem documentado.
* Mesma expertise para os três bancos simplifica operação.

**Negativas:**

* Nenhuma.

---
Anterior: [ADR 0004 - Banco de Dados por Serviço](0004_adr_banco_de_dados_por_servico.md)  
Próximo: [ADR 0006 - Campos Mapeados como JSONB](0006_adr_campos_mapeados_como_jsonb.md)
