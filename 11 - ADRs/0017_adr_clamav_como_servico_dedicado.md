# ADR 0017 - ClamAV como Serviço Dedicado no Cluster

## Status

Aceito

## Contexto

Arquivos enviados por usuários precisam ser escaneados contra malware antes de serem aceitos. Era necessário escolher como implementar o scan.

## Discussão e possibilidades

Optei pelo ClamAV, que é um antivírus open-source, leve e gratuito. Ele roda como um Deployment + Service (ClusterIP) no Kubernetes, escutando na porta 3310 via protocolo TCP clamd. A comunicação é feita pela biblioteca nClam no .NET.

O scan é fail-closed: se o ClamAV estiver indisponível, o upload é rejeitado. Preferi essa abordagem pois aceitar um arquivo sem scan seria um risco de segurança.

Considerei como alternativa utilizar um serviço externo da AWS (ex: Amazon GuardDuty ou terceiros). Contudo, o custo seria desnecessário — o ClamAV resolve o problema sem custos adicionais e roda dentro do próprio cluster.

O deploy usa a imagem `clamav/clamav:stable` com limits de 500m CPU e 1Gi de memória, exposto internamente via ClusterIP.

## Decisão

Foi decidido utilizar o ClamAV como serviço dedicado no cluster Kubernetes para scan de malware em arquivos enviados.

## Consequências

**Positivas:**

* Gratuito e open-source.
* Fail-closed — arquivos nunca são aceitos sem scan.
* Roda dentro do cluster, sem dependência externa.

**Negativas:**

* Mais um Deployment para gerenciar no cluster (monitoramento, atualizações de assinatura).
* As assinaturas de vírus precisam ser atualizadas periodicamente. A imagem `clamav/clamav:stable` faz isso automaticamente via `freshclam`.

---
Anterior: [ADR 0016 - Limite de 5MB por Arquivo](0016_adr_limite_de_5mb_por_arquivo.md)  
Próximo: [ADR 0018 - Google Gemini como LLM](0018_adr_google_gemini_como_llm.md)
