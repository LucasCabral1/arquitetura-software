# Respostas - Estudo de Caso: Integração Hospital, Operadora e Laboratório

**1. Para o pedido ao laboratório, você escolheria acesso direto, contrato oficial por adaptador ou mensageria? Que evidência mudaria essa escolha?**

Escolheria o **contrato oficial SOAP/XML** por meio de um adaptador. A justificativa é que essa abordagem evita a complexidade adicional de gerenciar filas de mensagens, lidar com duplicidade e aumentar a carga de operação, sendo uma solução mais facilitada. A evidência que mudaria essa escolha seria a necessidade rigorosa de garantir a entrega em cenários de alta indisponibilidade do parceiro, o que justificaria o custo operacional da mensageria.

**2. A tradução entre SOAP/TISS e o vocabulário da plataforma deveria ficar no gateway ou no adaptador? Por quê?**

A tradução deve ficar no **adaptador/ACL**. A justificativa é que o gateway é mais voltado para controles de baixo nível, enquanto a gestão de formato de dados, contratos e tradução de semântica de domínio deve ser responsabilidade do ACL.

**3. O mapeamento entre matricula_plano e o identificador da operadora pertence à plataforma ou à operadora?**

Pertence à **operadora**. A justificativa é que essas regras de negócio e identificadores externos são de domínio do parceiro e devem estar na operadora, cabendo ao adaptador da plataforma isolar e lidar com essa dependência para não contaminar o modelo interno.

**4. Para avisar que um exame ficou pronto, você escolheria polling, polling adaptativo ou webhook? Qual risco você aceita explicitamente com essa escolha?**

Escolheria a estratégia de **Webhook ou evento**. Essa escolha é justificada pelo fato de que o webhook gera menos tráfego e simplifica a notificação de resultados. Em contrapartida, o uso de polling poderia sobrecarregar o sistema (se for muito frequente) ou atrasar a entrega do resultado (se o intervalo for muito espaçado). O risco e o custo assumidos explicitamente com o webhook envolvem manter toda a estrutura de um endpoint autenticado, além de gerenciar a ordenação e a repetição das requisições recebidas.

**5. Que identificador de negócio, retenção e comportamento de duplicidade o ADR-002 precisa registrar antes de introduzir mensageria com idempotência?**

O ADR-002 deve registrar a criação e utilização de uma **chave de idempotência** atrelada ao identificador de negócio da solicitação. O comportamento de duplicidade que deve ser documentado e garantido é o de verificar a existência dessa chave e não processar ou considerar chaves repetidas, permitindo um reprocessamento seguro em caso de falhas.