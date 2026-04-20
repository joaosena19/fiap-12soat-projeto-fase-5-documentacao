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
    1. [Upload - Funcionamento e fluxos](../03%20-%20Sistemas/01%20-%20Upload/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    2. [Upload - Endpoints](../03%20-%20Sistemas/01%20-%20Upload/02%20-%20Endpoints/1_endpoints_upload.md)

2. Criação de um processo de análise
    1. [Processamento - Funcionamento e fluxos](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)

3. Consulta de status do processamento
    1. [Relatórios - Funcionamento e fluxos](../03%20-%20Sistemas/03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    2. [Relatórios - Endpoints](../03%20-%20Sistemas/03%20-%20Relatórios/02%20-%20Endpoints/1_endpoints_relatorio.md)

4. Geração de relatório
    1. [Relatórios - Funcionamento e fluxos](../03%20-%20Sistemas/03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)

5. Arquitetura em microsserviços
    1. Repositórios:
        - [fiap-12soat-projeto-fase-5-upload](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-upload)
        - [fiap-12soat-projeto-fase-5-processamento](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-processamento)
        - [fiap-12soat-projeto-fase-5-relatorio](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-relatorio)
        - [fiap-12soat-projeto-fase-5-infra](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-infra)
    2. Veja [Diagrama de Componentes](../02%20-%20Diagrama%20de%20componentes/1_diagrama_de_componentes.md)
    3. Veja [ADR 0003 - Divisão em Microsserviços](../11%20-%20ADRs/0003_adr_divisao_em_microsservicos.md)

6. Comunicação via REST e ao menos um fluxo assíncrono
    1. [Upload - Funcionamento e fluxos](../03%20-%20Sistemas/01%20-%20Upload/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    2. [Processamento - Funcionamento e fluxos](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    3. [Relatórios - Funcionamento e fluxos](../03%20-%20Sistemas/03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    4. Veja [Comunicação entre serviços](../06%20-%20Comunicação%20entre%20serviços/1_comunicacao_entre_servicos.md)
    5. Veja [ADR 0009 - SNS/SQS para Mensageria](../11%20-%20ADRs/0009_adr_sns_sqs_para_mensageria.md)
    6. Veja [ADR 0010 - MassTransit como Abstração](../11%20-%20ADRs/0010_adr_masstransit_como_abstracao.md)

7. Aplicação da Clean Architecture
    1. Veja [Arquitetura interna - Upload](../03%20-%20Sistemas/01%20-%20Upload/04%20-%20Arquitetura%20interna/1_arquitetura_interna_upload.md)
    2. Veja [Arquitetura interna - Processamento](../03%20-%20Sistemas/02%20-%20Processamento/03%20-%20Arquitetura%20interna/1_arquitetura_interna_processamento.md)
    3. Veja [Arquitetura interna - Relatórios](../03%20-%20Sistemas/03%20-%20Relatórios/04%20-%20Arquitetura%20interna/1_arquitetura_interna_relatorio.md)

8. Cada serviço com responsabilidade clara, banco de dados próprio e testes automatizados
    1. [Upload - Funcionamento e fluxos](../03%20-%20Sistemas/01%20-%20Upload/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    2. [Upload - Banco de dados](../03%20-%20Sistemas/01%20-%20Upload/03%20-%20Banco%20de%20dados/1_banco_de_dados_upload.md)
    3. [Processamento - Funcionamento e fluxos](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    4. [Processamento - Banco de dados](../03%20-%20Sistemas/02%20-%20Processamento/02%20-%20Banco%20de%20dados/1_banco_de_dados_processamento.md)
    5. [Relatórios - Funcionamento e fluxos](../03%20-%20Sistemas/03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    6. [Relatórios - Banco de dados](../03%20-%20Sistemas/03%20-%20Relatórios/03%20-%20Banco%20de%20dados/1_banco_de_dados_relatorio.md)
    7. [Qualidade - Upload](../10%20-%20Testes%20e%20qualidade/1_qualidade_upload.md)
    8. [Qualidade - Processamento](../10%20-%20Testes%20e%20qualidade/2_qualidade_processamento.md)
    9. [Qualidade - Relatórios](../10%20-%20Testes%20e%20qualidade/3_qualidade_relatorio.md)

9. Abordagens de IA
    1. [Processamento - Funcionamento e fluxos](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    2. Veja [ADR 0018 - Google Gemini como LLM](../11%20-%20ADRs/0018_adr_google_gemini_como_llm.md)

10. Pipeline claro de IA
    1. [Processamento - Funcionamento e fluxos](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)

11. Justificativa da abordagem de IA escolhida
    1. [Processamento - Funcionamento e fluxos](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    2. Veja [ADR 0018 - Google Gemini como LLM](../11%20-%20ADRs/0018_adr_google_gemini_como_llm.md)

12. Discussão de limitações do modelo de IA
    1. [Processamento - Funcionamento e fluxos](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)

13. Como a IA é acionada
    1. [Processamento - Funcionamento e fluxos](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)

14. Como o sistema trata falhas da IA
    1. [Processamento - Funcionamento e fluxos](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    2. Veja [ADR 0019 - DomainException como Erro no Processamento](../11%20-%20ADRs/0019_adr_domain_exception_como_error_no_processamento.md)

15. Como o resultado da IA é persistido
    1. [Processamento - Funcionamento e fluxos](../03%20-%20Sistemas/02%20-%20Processamento/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    2. [Processamento - Banco de dados](../03%20-%20Sistemas/02%20-%20Processamento/02%20-%20Banco%20de%20dados/1_banco_de_dados_processamento.md)

16. Como o relatório é gerado a partir da análise
    1. [Relatórios - Funcionamento e fluxos](../03%20-%20Sistemas/03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md)
    2. Veja [ADR 0014 - Relatório como Hub de Agregação](../11%20-%20ADRs/0014_adr_relatorio_como_hub_de_agregacao.md)

17. Infraestrutura
    1. Veja [Infraestrutura, Kubernetes e Escalabilidade](../04%20-%20Infraestrutura%2C%20Kubernetes%20e%20Escalabilidade/1_infraestrutura.md)
    2. Pastas k8s de cada repo: [Upload](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-upload/tree/main/k8s), [Processamento](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-processamento/tree/main/k8s), [Relatórios](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-relatorio/tree/main/k8s)
    3. Veja [CI & CD](../08%20-%20CI%20%26%20CD/1_ci_cd.md)
    4. Pipelines:
        - Upload — [CI](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-upload/blob/main/.github/workflows/pr-validation.yaml) / [CD](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-upload/blob/main/.github/workflows/deploy.yaml)
        - Processamento — [CI](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-processamento/blob/main/.github/workflows/pr-validation.yaml) / [CD](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-processamento/blob/main/.github/workflows/deploy.yaml)
        - Relatórios — [CI](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-relatorio/blob/main/.github/workflows/pr-validation.yaml) / [CD](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-relatorio/blob/main/.github/workflows/deploy.yaml)
        - Infraestrutura — [CI](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-infra/blob/main/.github/workflows/pr-validation.yaml) / [CD](https://github.com/joaosena19/fiap-12soat-projeto-fase-5-infra/blob/main/.github/workflows/deploy.yaml)

18. Logs estruturados
    1. Veja [Plano de Monitoramento](../09%20-%20Plano%20de%20Monitoramento/1_plano_de_monitoramento.md)
    2. Veja [ADR 0022 - New Relic para Observabilidade](../11%20-%20ADRs/0022_adr_new_relic_para_observabilidade.md)

19. Tratamento de erros
    1. Veja [Plano de Monitoramento](../09%20-%20Plano%20de%20Monitoramento/1_plano_de_monitoramento.md)

20. Testes unitários
    1. [Qualidade - Upload](../10%20-%20Testes%20e%20qualidade/1_qualidade_upload.md)
    2. [Qualidade - Processamento](../10%20-%20Testes%20e%20qualidade/2_qualidade_processamento.md)
    3. [Qualidade - Relatórios](../10%20-%20Testes%20e%20qualidade/3_qualidade_relatorio.md)
    4. [Qualidade - Infraestrutura](../10%20-%20Testes%20e%20qualidade/4_qualidade_infra.md)

21. Segurança
    1. Veja [Segurança](../07%20-%20Segurança/1_seguranca.md)

---
Anterior: [Introdução](1_introducao.md)  
Próximo: [Diagrama de componentes](../02%20-%20Diagrama%20de%20componentes/1_diagrama_de_componentes.md)
