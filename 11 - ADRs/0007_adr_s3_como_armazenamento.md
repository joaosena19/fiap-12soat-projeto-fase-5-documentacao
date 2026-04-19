# ADR 0007 - S3 como Armazenamento de Arquivos

## Status

Aceito

## Contexto

Era necessário armazenar os diagramas enviados pelos usuários e os relatórios gerados (Markdown, PDF). Esses arquivos precisam ser acessíveis de forma segura e durável.

## Discussão e possibilidades

O Amazon S3 é o padrão da AWS para armazenamento de objetos. Tem durabilidade de 99.999999999% (11 noves), suporte nativo a presigned URLs (ver [ADR 0020](0020_adr_presigned_urls_do_s3.md)) e integração direta com IAM para controle de acesso.

Considerei duas alternativas:

1. **EFS (Elastic File System):** sistema de arquivos montável no cluster. Mais caro que o S3 e desnecessário para o padrão de acesso do projeto (upload → armazenamento → download esporádico).
2. **Armazenar no banco de dados:** antipattern para binários. Inflaria o banco, complicaria backups e não traria nenhum benefício.

O S3 resolve o problema de forma simples e com custo mínimo.

## Decisão

Foi decidido utilizar o Amazon S3 como armazenamento de arquivos para diagramas enviados e relatórios gerados.

O bucket é provisionado via Terraform no repositório de infraestrutura (`fiap-12soat-projeto-fase-5-infra`). O Upload envia arquivos ao S3 via SDK, e o Relatório lê do S3 para download e escreve Markdown/PDF no S3.

## Consequências

**Positivas:**

* Durabilidade altíssima e custo baixo.
* Suporte nativo a presigned URLs para acesso temporário seguro.
* Integração direta com IAM — sem necessidade de credenciais no código.

**Negativas:**

* Dependência do S3 da AWS. Trocar de provider exigiria adaptar a camada de armazenamento.

---
Anterior: [ADR 0006 - Campos Mapeados como JSONB](0006_adr_campos_mapeados_como_jsonb.md)  
Próximo: [ADR 0008 - Repositório de Infra Centralizado + Banco nos Repos](0008_adr_repo_de_infra_e_infra_nos_repos.md)
