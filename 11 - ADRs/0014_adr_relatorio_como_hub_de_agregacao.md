# ADR 0014 - Relatório como Hub de Agregação de Eventos

## Status

Aceito

## Contexto

O usuário precisa consultar o status de processamento de um diagrama. Era necessário decidir qual serviço expõe essa consulta.

## Discussão e possibilidades

O Relatório consome eventos de todos os estágios da pipeline: upload concluído, processamento iniciado, análise concluída, erro. Ele já tem o estado consolidado no aggregate `ResultadoDiagrama`, que reflete o status de toda a pipeline: `Recebido`, `EmProcessamento`, `Analisado`, `Erro`, `Rejeitado`.

O Relatório é o serviço que o usuário de fato deseja consultar — é onde os resultados estão. Faz sentido que a consulta de status também esteja aqui, pois quem pergunta "qual o status do meu diagrama?" geralmente quer ver o resultado.

A alternativa seria ter APIs em todos os três serviços, forçando o cliente a consultar múltiplos endpoints para montar o status completo. Isso fragmentaria a experiência sem benefício.

Na prática, o Relatório tem 6 consumers e o `GET /api/relatorio/{analiseDiagramaId}` retorna o status consolidado com todos os detalhes.

## Decisão

Foi decidido que o Relatório é o hub de agregação de eventos e o ponto único de consulta de status para o usuário.

Veja mais em [Funcionamento e fluxos - Relatório](../03%20-%20Sistemas/03%20-%20Relatórios/01%20-%20Funcionamento%20e%20fluxos/1_funcionamento_e_fluxos.md).

## Consequências

**Positivas:**

* Ponto único de consulta — o cliente não precisa consultar múltiplos serviços.
* Estado consolidado reflete toda a pipeline num único aggregate.

**Negativas:**

* O Relatório depende de receber todos os eventos corretamente. Se uma mensagem for perdida, o estado ficará desatualizado.

---
Anterior: [ADR 0013 - Processamento como Worker](0013_adr_processamento_como_worker.md)  
Próximo: [ADR 0015 - Verificação de Duplicatas via Hash SHA-256](0015_adr_verificacao_duplicatas_via_hash.md)
