# Índice

Este arquivo serve como referência para cada um dos pontos de avaliação do Tech Challenge. Cada termo está linkado para o respectivo arquivo de documentação.

Se for sua primeira leitura, siga a navegação "Próximo" ao final de cada arquivo.

## Pontos de Avaliação

1. Arquitetura em 3 microsserviços independentes
    1. Repositórios:
        - [fiap-12soat-projeto-fase-5-upload](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-upload)
        - [fiap-12soat-projeto-fase-5-processamento](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-processamento)
        - [fiap-12soat-projeto-fase-5-relatorio](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-relatorio)
        - [fiap-12soat-projeto-fase-5-infra](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-infra)
        - [fiap-12soat-projeto-fase-5-documentacao](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-documentacao)
    2. Veja [ADR 0003 - Divisão em Microsserviços](../11%20-%20ADRs/0003_adr_divisao_em_microsservicos.md)
2. Bancos de dados isolados por serviço
    1. [Banco de dados - Upload (PostgreSQL)](../03%20-%20Sistemas/01%20-%20Upload/03%20-%20Banco%20de%20dados/1_banco_de_dados_upload.md)
    2. [Banco de dados - Processamento (PostgreSQL)](../03%20-%20Sistemas/02%20-%20Processamento/02%20-%20Banco%20de%20dados/1_banco_de_dados_processamento.md)
    3. [Banco de dados - Relatórios (PostgreSQL)](../03%20-%20Sistemas/03%20-%20Relatórios/03%20-%20Banco%20de%20dados/1_banco_de_dados_relatorio.md)
    4. Veja [ADR 0004 - Banco de Dados por Serviço](../11%20-%20ADRs/0004_adr_banco_de_dados_por_servico.md)
3. [Diagrama de Componentes](../02%20-%20Diagrama%20de%20componentes/1_diagrama_de_componentes.md)
4. Upload de diagrama
    1. [Funcionamento e fluxos - Upload](../03%20-%20Sistemas/01%20-%20Upload/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    2. [Endpoints - Upload](../03%20-%20Sistemas/01%20-%20Upload/02%20-%20Endpoints/1_endpoints_upload.md)
5. Consulta de status do processamento
    1. [Funcionamento e fluxos - Relatórios](../03%20-%20Sistemas/03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md) — estados Recebido → Em processamento → Analisado → Erro
    2. [Endpoints - Relatórios](../03%20-%20Sistemas/03%20-%20Relatórios/02%20-%20Endpoints/1_endpoints_relatorio.md)
6. Processamento e pipeline de IA
    1. [Funcionamento e fluxos - Processamento](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md) — abordagem, prompt engineering, guardrails, limitações, resiliência e persistência
    2. Veja [ADR 0018 - Google Gemini como LLM](../11%20-%20ADRs/0018_adr_google_gemini_como_llm.md)
7. Integração da IA no sistema
    1. [Funcionamento e fluxos - Processamento](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md) — como a IA é acionada, tratamento de falhas e persistência do resultado
    2. [Funcionamento e fluxos - Relatórios](../03%20-%20Sistemas/03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md) — como o relatório é gerado a partir da análise
8. Geração de relatório
    1. [Funcionamento e fluxos - Relatórios](../03%20-%20Sistemas/03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    2. [Endpoints - Relatórios](../03%20-%20Sistemas/03%20-%20Relatórios/02%20-%20Endpoints/1_endpoints_relatorio.md)
    3. Veja [ADR 0014 - Relatório como Hub de Agregação](../11%20-%20ADRs/0014_adr_relatorio_como_hub_de_agregacao.md)
9. Comunicação assíncrona entre serviços
    1. [Comunicação entre serviços](../06%20-%20Comunicação%20entre%20serviços/1_comunicacao_entre_servicos.md)
    2. Veja [ADR 0009 - SNS + SQS para Mensageria](../11%20-%20ADRs/0009_adr_sns_sqs_para_mensageria.md)
    3. Veja [ADR 0010 - MassTransit como Abstração de Mensageria](../11%20-%20ADRs/0010_adr_masstransit_como_abstracao.md)
10. Clean Architecture
    1. [Arquitetura interna - Upload](../03%20-%20Sistemas/01%20-%20Upload/04%20-%20Arquitetura%20interna/1_arquitetura_interna_upload.md)
    2. [Arquitetura interna - Processamento](../03%20-%20Sistemas/02%20-%20Processamento/03%20-%20Arquitetura%20interna/1_arquitetura_interna_processamento.md)
    3. [Arquitetura interna - Relatórios](../03%20-%20Sistemas/03%20-%20Relatórios/04%20-%20Arquitetura%20interna/1_arquitetura_interna_relatorio.md)
11. [Segurança](../07%20-%20Segurança/1_seguranca.md)
12. Qualidade e Testes
    1. [Qualidade - Upload](../10%20-%20Testes%20e%20qualidade/1_qualidade_upload.md)
    2. [Qualidade - Processamento](../10%20-%20Testes%20e%20qualidade/2_qualidade_processamento.md)
    3. [Qualidade - Relatórios](../10%20-%20Testes%20e%20qualidade/3_qualidade_relatorio.md)
    4. Veja [CI/CD](../08%20-%20CI%20%26%20CD/1_ci_cd.md)
13. CI/CD automatizado e proteção das branchs
    1. Veja [CI/CD](../08%20-%20CI%20%26%20CD/1_ci_cd.md)
    2. Pipelines:
        - Upload - [CI](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-upload/blob/main/.github/workflows/pr-validation.yaml) / [CD](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-upload/blob/main/.github/workflows/deploy.yaml)
        - Processamento - [CI](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-processamento/blob/main/.github/workflows/pr-validation.yaml) / [CD](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-processamento/blob/main/.github/workflows/deploy.yaml)
        - Relatórios - [CI](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-relatorio/blob/main/.github/workflows/pr-validation.yaml) / [CD](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-relatorio/blob/main/.github/workflows/deploy.yaml)
        - Infraestrutura - [CI](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-infra/blob/main/.github/workflows/pr-validation.yaml) / [CD](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-infra/blob/main/.github/workflows/deploy.yaml)
14. Kubernetes e Infraestrutura
    1. [Infraestrutura, Kubernetes e Escalabilidade](../04%20-%20Infraestrutura%2C%20Kubernetes%20e%20Escalabilidade/1_infraestrutura.md)
    2. Pastas k8s de cada repo: [Upload](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-upload/tree/main/k8s), [Processamento](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-processamento/tree/main/k8s), [Relatórios](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-relatorio/tree/main/k8s)
15. [Observabilidade e monitoramento](../09%20-%20Plano%20de%20Monitoramento/1_plano_de_monitoramento.md)
16. [Autenticação](../05%20-%20Autenticação/1_autenticacao.md)
17. [ADRs](../11%20-%20ADRs/0001_adr_aws_como_cloud_provider.md)

---
Anterior: [Introdução](1_introducao.md)  
Próximo: [Diagrama de componentes](../02%20-%20Diagrama%20de%20componentes/1_diagrama_de_componentes.md)
