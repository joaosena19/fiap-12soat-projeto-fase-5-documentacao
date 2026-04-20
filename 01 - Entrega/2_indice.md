# Índice

Este arquivo serve como referência para cada um dos pontos de avaliação do Tech Challenge. Cada termo está linkado para o respectivo arquivo de documentação.

Se for sua primeira leitura, siga a navegação "Próximo" ao final de cada arquivo.

## Repositórios

- [fiap-12soat-projeto-fase-5-upload](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-upload)
- [fiap-12soat-projeto-fase-5-processamento](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-processamento)
- [fiap-12soat-projeto-fase-5-relatorio](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-relatorio)
- [fiap-12soat-projeto-fase-5-infra](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-infra)
- [fiap-12soat-projeto-fase-5-documentacao](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-documentacao)

## Pontos de Avaliação

1. Upload de diagrama (imagem ou PDF)
    1. [Fluxo de envio de diagrama](../03%20-%20Sistemas/01%20-%20Upload/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#fluxo-de-envio-de-diagrama)
    2. [Endpoints - Upload](../03%20-%20Sistemas/01%20-%20Upload/02%20-%20Endpoints/1_endpoints_upload.md)

2. Criação de um processo de análise
    1. [Fluxo de processamento](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#fluxo-de-processamento)

3. Consulta de status do processamento
    1. [Pontos de entrada HTTP — Relatórios](../03%20-%20Sistemas/03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#pontos-de-entrada-http-endpoints)
    2. [Ciclo de vida do aggregate — Relatórios](../03%20-%20Sistemas/03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#ciclo-de-vida-do-aggregate)
    3. [Endpoints - Relatórios](../03%20-%20Sistemas/03%20-%20Relatórios/02%20-%20Endpoints/1_endpoints_relatorio.md)

4. Geração de relatório
    1. [Geração de relatórios via Strategy Pattern](../03%20-%20Sistemas/03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#geração-de-relatórios-via-strategy-pattern)
    2. [Fluxo completo — da análise ao relatório](../03%20-%20Sistemas/03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#fluxo-completo--da-análise-ao-relatório)
    3. Veja [ADR 0014 - Relatório como Hub de Agregação](../11%20-%20ADRs/0014_adr_relatorio_como_hub_de_agregacao.md)

5. Arquitetura em microsserviços
    1. Repositórios:
        - [fiap-12soat-projeto-fase-5-upload](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-upload)
        - [fiap-12soat-projeto-fase-5-processamento](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-processamento)
        - [fiap-12soat-projeto-fase-5-relatorio](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-relatorio)
        - [fiap-12soat-projeto-fase-5-infra](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-infra)
    2. [Diagrama de Componentes](../02%20-%20Diagrama%20de%20componentes/1_diagrama_de_componentes.md)
    3. Veja [ADR 0003 - Divisão em Microsserviços](../11%20-%20ADRs/0003_adr_divisao_em_microsservicos.md)

6. Comunicação via REST e ao menos um fluxo assíncrono
    1. [Mensagens publicadas — Upload](../03%20-%20Sistemas/01%20-%20Upload/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#mensagens-publicadas)
    2. [Mensagens publicadas — Processamento](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#mensagens-publicadas)
    3. [Fluxo do pipeline de mensagens](../06%20-%20Comunicação%20entre%20serviços/1_comunicacao_entre_servicos.md#fluxo-do-pipeline)
    4. [Catálogo de mensagens](../06%20-%20Comunicação%20entre%20serviços/1_comunicacao_entre_servicos.md#mensagens)
    5. Veja [Comunicação entre serviços](../06%20-%20Comunicação%20entre%20serviços/1_comunicacao_entre_servicos.md)
    6. Veja [ADR 0009 - SNS + SQS para Mensageria](../11%20-%20ADRs/0009_adr_sns_sqs_para_mensageria.md)
    7. Veja [ADR 0010 - MassTransit como Abstração de Mensageria](../11%20-%20ADRs/0010_adr_masstransit_como_abstracao.md)

7. Aplicação da Clean Architecture
    1. Veja [Arquitetura interna - Upload](../03%20-%20Sistemas/01%20-%20Upload/04%20-%20Arquitetura%20interna/1_arquitetura_interna_upload.md)
    2. Veja [Arquitetura interna - Processamento](../03%20-%20Sistemas/02%20-%20Processamento/03%20-%20Arquitetura%20interna/1_arquitetura_interna_processamento.md)
    3. Veja [Arquitetura interna - Relatórios](../03%20-%20Sistemas/03%20-%20Relatórios/04%20-%20Arquitetura%20interna/1_arquitetura_interna_relatorio.md)

8. Cada serviço com responsabilidade clara, banco de dados próprio e testes automatizados
    1. [Funcionamento e fluxos - Upload](../03%20-%20Sistemas/01%20-%20Upload/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    2. [Banco de dados - Upload (PostgreSQL)](../03%20-%20Sistemas/01%20-%20Upload/03%20-%20Banco%20de%20dados/1_banco_de_dados_upload.md)
    3. [Funcionamento e fluxos - Processamento](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    4. [Banco de dados - Processamento (PostgreSQL)](../03%20-%20Sistemas/02%20-%20Processamento/02%20-%20Banco%20de%20dados/1_banco_de_dados_processamento.md)
    5. [Funcionamento e fluxos - Relatórios](../03%20-%20Sistemas/03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    6. [Banco de dados - Relatórios (PostgreSQL)](../03%20-%20Sistemas/03%20-%20Relatórios/03%20-%20Banco%20de%20dados/1_banco_de_dados_relatorio.md)
    7. [Qualidade e testes - Upload](../10%20-%20Testes%20e%20qualidade/1_qualidade_upload.md)
    8. [Qualidade e testes - Processamento](../10%20-%20Testes%20e%20qualidade/2_qualidade_processamento.md)
    9. [Qualidade e testes - Relatórios](../10%20-%20Testes%20e%20qualidade/3_qualidade_relatorio.md)
    10. Veja [ADR 0004 - Banco de Dados por Serviço](../11%20-%20ADRs/0004_adr_banco_de_dados_por_servico.md)

9. Abordagens de IA (detecção de componentes, classificação de riscos, guardrails, prompt engineering)
    1. [Pipeline de IA](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#pipeline-de-ia)
    2. [Prompt engineering e guardrails](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#prompt-engineering-e-guardrails)
    3. Veja [ADR 0018 - Google Gemini como LLM](../11%20-%20ADRs/0018_adr_google_gemini_como_llm.md)

10. Pipeline claro de IA
    1. [Pipeline de IA](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#pipeline-de-ia)
    2. [Estratégia de resiliência com 4 modelos](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#estratégia-de-resiliência-com-4-modelos)

11. Justificativa da abordagem de IA escolhida
    1. [Abordagem escolhida](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#abordagem-escolhida)
    2. Veja [ADR 0018 - Google Gemini como LLM](../11%20-%20ADRs/0018_adr_google_gemini_como_llm.md)

12. Discussão de limitações do modelo de IA
    1. [Limitações do modelo](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#limitações-do-modelo)

13. Como a IA é acionada
    1. [Integração com a LLM](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#integração-com-a-llm)

14. Como o sistema trata falhas da IA
    1. [Tratamento tripartido do resultado](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#tratamento-tripartido-do-resultado)
    2. Veja [ADR 0019 - DomainException como Erro no Processamento](../11%20-%20ADRs/0019_adr_domain_exception_como_error_no_processamento.md)

15. Como o resultado da IA é persistido
    1. [Persistência do resultado da IA](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#persistência-do-resultado-da-ia)
    2. [Banco de dados - Processamento (PostgreSQL)](../03%20-%20Sistemas/02%20-%20Processamento/02%20-%20Banco%20de%20dados/1_banco_de_dados_processamento.md)

16. Como o relatório é gerado a partir da análise
    1. [Fluxo completo — da análise ao relatório](../03%20-%20Sistemas/03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#fluxo-completo--da-análise-ao-relatório)
    2. [Geração de relatórios via Strategy Pattern](../03%20-%20Sistemas/03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#geração-de-relatórios-via-strategy-pattern)
    3. Veja [ADR 0014 - Relatório como Hub de Agregação](../11%20-%20ADRs/0014_adr_relatorio_como_hub_de_agregacao.md)

17. Infraestrutura
    1. [Infraestrutura, Kubernetes e Escalabilidade](../04%20-%20Infraestrutura%2C%20Kubernetes%20e%20Escalabilidade/1_infraestrutura.md)
    2. [Amazon EKS e escalabilidade (HPA)](../04%20-%20Infraestrutura%2C%20Kubernetes%20e%20Escalabilidade/1_infraestrutura.md#amazon-eks)
    3. [Mensageria SNS/SQS — Infraestrutura](../04%20-%20Infraestrutura%2C%20Kubernetes%20e%20Escalabilidade/1_infraestrutura.md#mensageria-snssqs)
    4. Pastas k8s: [Upload](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-upload/tree/main/k8s), [Processamento](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-processamento/tree/main/k8s), [Relatórios](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-relatorio/tree/main/k8s)
    5. [Integração Contínua (CI)](../08%20-%20CI%20%26%20CD/1_ci_cd.md#integração-contínua-ci)
    6. [Deploy Contínuo (CD)](../08%20-%20CI%20%26%20CD/1_ci_cd.md#deploy-contínuo-cd)
    7. Pipelines: Upload — [CI](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-upload/blob/main/.github/workflows/pr-validation.yaml) / [CD](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-upload/blob/main/.github/workflows/deploy.yaml) | Processamento — [CI](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-processamento/blob/main/.github/workflows/pr-validation.yaml) / [CD](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-processamento/blob/main/.github/workflows/deploy.yaml) | Relatórios — [CI](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-relatorio/blob/main/.github/workflows/pr-validation.yaml) / [CD](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-relatorio/blob/main/.github/workflows/deploy.yaml) | Infraestrutura — [CI](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-infra/blob/main/.github/workflows/pr-validation.yaml) / [CD](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-infra/blob/main/.github/workflows/deploy.yaml)
    8. Veja [ADR 0001 - AWS como Cloud Provider](../11%20-%20ADRs/0001_adr_aws_como_cloud_provider.md)
    9. Veja [ADR 0008 - Repositório de Infra e Infra nos Repos](../11%20-%20ADRs/0008_adr_repo_de_infra_e_infra_nos_repos.md)

18. Logs estruturados
    1. [Log estruturado na aplicação](../09%20-%20Plano%20de%20Monitoramento/1_plano_de_monitoramento.md#log-estruturado-na-aplicação)
    2. [CorrelationId](../09%20-%20Plano%20de%20Monitoramento/1_plano_de_monitoramento.md#correlationid)
    3. [Custom events para o pipeline](../09%20-%20Plano%20de%20Monitoramento/1_plano_de_monitoramento.md#custom-events-para-o-pipeline)
    4. [Dashboard New Relic](../09%20-%20Plano%20de%20Monitoramento/1_plano_de_monitoramento.md#dashboard-new-relic)
    5. Veja [ADR 0022 - New Relic para Observabilidade](../11%20-%20ADRs/0022_adr_new_relic_para_observabilidade.md)

19. Tratamento de erros
    1. [Padrão try/catch nos use cases](../09%20-%20Plano%20de%20Monitoramento/1_plano_de_monitoramento.md#padrão-trycatch-nos-use-cases)
    2. [Tratamento tripartido do resultado — Processamento](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md#tratamento-tripartido-do-resultado)
    3. Veja [ADR 0019 - DomainException como Erro no Processamento](../11%20-%20ADRs/0019_adr_domain_exception_como_error_no_processamento.md)

20. Testes unitários
    1. [Qualidade e testes - Upload](../10%20-%20Testes%20e%20qualidade/1_qualidade_upload.md)
    2. [Qualidade e testes - Processamento](../10%20-%20Testes%20e%20qualidade/2_qualidade_processamento.md)
    3. [Qualidade e testes - Relatórios](../10%20-%20Testes%20e%20qualidade/3_qualidade_relatorio.md)
    4. [Qualidade e testes - Infraestrutura](../10%20-%20Testes%20e%20qualidade/4_qualidade_infra.md)
    5. [Validação de cobertura ≥ 80% (CI gate)](../08%20-%20CI%20%26%20CD/1_ci_cd.md#validação-de-cobertura--80)
    6. [Proteção de branch](../08%20-%20CI%20%26%20CD/1_ci_cd.md#proteção-de-branch)

21. Segurança
    1. [Validação de arquivos](../07%20-%20Segurança/1_seguranca.md#validação-de-arquivos)
    2. [Segurança na comunicação entre serviços](../07%20-%20Segurança/1_seguranca.md#segurança-na-comunicação-entre-serviços)
    3. [Riscos e limitações](../07%20-%20Segurança/1_seguranca.md#riscos-e-limitações)
    4. Veja [ADR 0017 - ClamAV como Serviço Dedicado](../11%20-%20ADRs/0017_adr_clamav_como_servico_dedicado.md)
    5. Veja [ADR 0012 - Emissão de Token no Upload](../11%20-%20ADRs/0012_adr_emissao_de_token_no_upload.md)

---
Anterior: [Introdução](1_introducao.md)  
Próximo: [Diagrama de componentes](../02%20-%20Diagrama%20de%20componentes/1_diagrama_de_componentes.md)
