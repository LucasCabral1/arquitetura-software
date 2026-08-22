## Cenário 1 - Agenda - Força dominante: Consistência

Fonte do estímulo: dois atendentes realizando agendamentos simultaneamente.

Estímulo: solicitação concorrente de confirmação para o mesmo horário e profissional.

Ambiente: operação normal.

Artefato: módulo de Agenda.

Resposta: o sistema confirma apenas uma reserva e rejeita ou solicita remarcação para a outra tentativa.

Medida: 100% dos conflitos de reserva detectados, nenhuma dupla confirmação para o mesmo horário durante uma janela de observação de 15 dias  .                                                              


## Cenário 2 - Triagem Administrativa - Força dominante: Extensibilidade 

Fonte do estímulo: Equipe de TI. 

Estímulo: inclusão de uma nova etapa na triagem administrativa.

Ambiente: desenvolvimento do sistema.

Artefato: módulo de triagem administrativa.

Resposta: o sistema incorpora a nova etapa ao fluxo sem interromper as etapas existentes.

Medida: nova etapa implantada em até 5 dia útil, sem defeitos críticos e sem necessidade de modificar módulos já existentes durante a implantação.


## Cenário 3 - Faturamento - Força dominante: Vazão

Fonte do estímulo: sistemas internos que enviam registros financeiros.

Estímulo: envio em lote de grande volume de registros para consolidação e faturamento.

Ambiente: operação normal.

Artefato: módulo de faturamento.

Resposta: o sistema consolida, valida, ajusta e envia os registros em lote para a operadora sem perda de dados

Medida: processar pelo menos 50.000 registros por hora com taxa de erro inferior a 0,1% durante a janela de observação.