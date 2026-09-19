# Respostas às Questões para Discussão

**1. A página descreve três fases da arquitetura do LinkedIn. Nomeie cada fase e diga o que caracterizava a comunicação entre as partes em cada uma.**
* **Fase 1 (Monolito Leo):** Comunicação interna direta no mesmo processo e acesso direto aos bancos de dados.
* **Fase 2 (Serviços Síncronos):** Comunicação via rede através de chamadas síncronas (RPC e REST), onde cada serviço esperava a resposta do outro.
* **Fase 3 (Log Distribuído/Kafka):** Comunicação assíncrona por meio de um registro imutável compartilhado (log), onde produtores escrevem e consumidores leem no seu próprio ritmo.

**2. Explique por que uma cadeia de chamadas síncronas entre muitos serviços produz disponibilidade combinada pior que a de qualquer serviço isolado.**
Porque a falha ou lentidão de um único elo quebra ou atrasa toda a requisição. Matematicamente, as disponibilidades de cada serviço se multiplicam (ex: 99% x 99% = 98%), tornando a disponibilidade da cadeia inteira sempre menor do que a do seu elo mais fraco.

**3. A propriedade central do Kafka é que ler não consome a mensagem. Explique as três consequências que o caso deriva dessa propriedade.**
1. O produtor desconhece os consumidores (apenas escreve e finaliza seu trabalho).
2. Novos consumidores podem ser adicionados no futuro e reprocessar todo o histórico de dados guardados.
3. Consumidores lentos não travam nem sobrecarregam o produtor, pois o atraso gera apenas armazenamento em disco, não fila na origem.

**4. Compare um agrupamento local e um agregador quanto à origem das mensagens que cada um recebe e quanto ao domínio de falha que cada um delimita.**
* **Origem:** O cluster *local* recebe mensagens geradas apenas dentro do seu próprio datacenter. O cluster *agregador* recebe espelhamentos de mensagens de todos os clusters locais.
* **Domínio de falha:** Essa topologia isola incidentes. Se houver falha, ela fica restrita ao cluster local daquele datacenter, sem causar um apagão global nos dados agregados ou afetar as outras regiões.

**5. Compare o trabalho operacional da equipe antes e depois da adoção do Kafka, citando o ferramental que a empresa precisou construir.**
* **Antes:** O esforço estava em programar e gerenciar integrações manuais ("tubulações") de código entre dezenas de serviços par a par.
* **Depois:** O trabalho migrou para a manutenção de infraestrutura e operação de um sistema de armazenamento distribuído em larga escala.
* **Ferramental criado:** *Cruise Control* (manutenção automática de cluster), *Brooklin* (espelhamento entre clusters) e *Bean Counter* (auditoria dos fluxos).

**6. O paper de 2011 lista replicação como trabalho futuro, não como recurso já entregue. Explique por que essa lacuna era aceitável para o caso de uso original do LinkedIn e o que o mecanismo de ISR, criado depois, muda para quem opera um cluster hoje.**
* **Por que era aceitável:** O uso inicial do Kafka focava em logs de rastreamento e telemetria (análise de uso), onde a perda pontual de alguns dados em caso de queima de disco não gerava impactos críticos no negócio.
* **O que o ISR muda:** Hoje, com o *In-Sync Replica* (réplicas em sincronia), os dados só são confirmados para o produtor após serem copiados para outros discos. Isso garante **tolerância a falhas**, permitindo que o Kafka seja usado de forma segura para transações financeiras e dados críticos sem o risco de perda permanente.