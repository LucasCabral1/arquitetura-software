# Recordar

**1. Qual é a diferença essencial entre um broker e um mediator?**

Um broker distribui mensagens entre produtor e consumidor sem decidir nada sobre a ordem ou o sentido de negócio do fluxo; ele só roteia. Um mediator conhece o processo inteiro, decide a sequência dos passos e coordena os participantes — assume, de propósito, a decisão que o broker se recusa a tomar.

**2. Como se nomeia corretamente um evento? E um comando? Dê um exemplo de cada, usando o domínio da situação acima.**

Um evento é nomeado no particípio passado, porque afirma um fato já ocorrido: `ResultadoLaboratorialDisponibilizado` segue esse padrão. Um comando é nomeado no infinitivo ou como uma ordem direta, porque pede uma ação a alguém: `GerarCobranca` segue esse padrão. Nomear um comando no particípio (como se já tivesse acontecido) é exatamente o tipo de erro que gerou a discussão de dez minutos da situação.

**3. Classifique cada um dos seis nomes da situação (ResultadoLaboratorialDisponibilizado.v1, GerarCobranca, hospital.events, billing.resultados.v1, hospital.events.dlx e billing.resultados.v1.dlq) numa das categorias evento, comando, exchange de domínio, fila de trabalho, exchange de dead-letter ou fila de dead-letter.**

`ResultadoLaboratorialDisponibilizado.v1` é evento, pelo particípio e pela versão explícita. `GerarCobranca` é comando, pelo verbo no infinitivo pedindo uma ação. `hospital.events` é a exchange de domínio, pelo nome genérico do canal. `billing.resultados.v1` é a fila de trabalho de um consumidor específico. `hospital.events.dlx` é a exchange de dead-letter, pelo sufixo `.dlx`. `billing.resultados.v1.dlq` é a fila de dead-letter correspondente, pelo sufixo `.dlq`.

**4. Defina, numa frase cada, entrega pelo menos uma vez, idempotência, ordenação e dead-letter queue.**

Entrega pelo menos uma vez admite que a mesma mensagem chegue mais de uma vez. Idempotência é a propriedade que faz repetir a mensagem não repetir o efeito de negócio. Ordenação exige declarar qual sequência importa e sob qual chave. Dead-letter queue é a fila que guarda mensagens rejeitadas para decisão controlada, sem apagá-las.

# Compreender

**1. Descreva, em uma sequência de passos concreta, o que acontece quando o consumidor de Faturamento grava o efeito de uma cobrança no SQLite e o processo cai antes de confirmar a mensagem ao RabbitMQ. Explique o que o broker faz a seguir com aquela mensagem e por que ele não tem nenhuma forma de saber que o efeito já havia sido registrado do outro lado.**

O consumidor recebe a mensagem, grava a linha em `billing_effects` e cai antes de enviar o ack ao RabbitMQ. Da perspectiva do broker, a mensagem nunca foi confirmada, então ela permanece disponível e é reentregue quando o consumidor volta ao ar. O broker não tem acesso ao SQLite do consumidor: ele só sabe se recebeu ou não uma confirmação, e por isso reentrega por precaução em vez de assumir que o trabalho foi concluído.

**2. A pessoa da situação, que propôs ignorar todos os redeliveries, está tentando resolver um problema real com a ferramenta errada. Explique por que a entrega repetida é uma consequência necessária de garantir que nenhuma mensagem se perca diante de falhas ambíguas como a do item anterior, e diga o que a equipe perderia de fato se simplesmente desligasse o redelivery em vez de tratar a repetição.**

O broker não consegue distinguir "o consumidor processou e a confirmação se perdeu na rede" de "o consumidor nunca processou": as duas situações parecem idênticas do lado de fora. Reentregar é a escolha segura porque assume o cenário mais caro, que é perder trabalho, em vez do cenário mais raro, que é duplicar uma confirmação. Desligar o redelivery trocaria duplicidade visível, que a idempotência resolve, por perda silenciosa de resultados de exame, que nenhum mecanismo do laboratório detecta.

**3. No log do consumidor de Faturamento aparece a linha `processed=False attempts=2` para um `event_id` que já tinha gerado efeito antes, mas a tabela `billing_effects` mostra só uma linha de cobrança para essa mesma identidade. Um colega, olhando só a linha do log, conclui que há um bug porque "a mensagem foi processada duas vezes". Usando esse log e essa tabela como evidência, diferencie tentativa, confirmação e efeito de negócio, e explique por que a conclusão do colega está errada.**

Tentativa é cada vez que o consumidor viu a mensagem e registrou isso em `processed_events`; confirmação é o ack enviado ao broker, que encerra a entrega daquela cópia específica; efeito de negócio é a linha em `billing_effects`, que só existe na primeira vez. O colega confundiu tentativa com efeito: `attempts=2` conta quantas vezes o consumidor viu aquele `event_id`, contagem que cresce a cada entrega repetida, enquanto `billing_effects` registra o efeito de negócio, que a mesma transação garante existir uma única vez. A tabela com uma única linha é exatamente a evidência de que o sistema funcionou como projetado.

**4. Alguém na equipe propõe resolver a duplicidade guardando os `event_id` já vistos num `set()` em memória, dentro do próprio processo Python do consumidor, em vez de gravar cada tentativa no SQLite. Explique por que essa alternativa falha na primeira vez que o serviço reinicia ou que uma segunda réplica do consumidor entra no ar, e diga o que ter `event_id` como chave primária durável resolve que o `set()` em memória não resolve.**

Um `set()` em memória existe só enquanto o processo está vivo: um reinício zera o histórico de `event_id` vistos, e uma segunda réplica começa com o próprio `set()` vazio, sem conhecimento do que a primeira já processou. `event_id` como chave primária numa tabela durável sobrevive a reinício e é compartilhada entre réplicas que apontam para o mesmo banco, porque a garantia passou a ser uma propriedade dos dados, não uma variável de um processo específico.