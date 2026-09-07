# Respostas - Estudo de Caso: quando a fragmentação supera a autonomia

**1. Qual forma de acoplamento mais atrasava a equipe?**

O acoplamento de implantação. O sintoma que justifica essa escolha é: *"Uma nova regra de autorização obrigava a implantar seis processos na mesma janela"*. Isso amarrava o ciclo de vida dos serviços, exigindo entregas coordenadas, lentas e com alto risco de falha em cascata.

**2. Qual critério revelou que quatro processos eram um único bounded context?**

Os critérios de regras e mudanças conjuntas. A frase do texto que sustenta essa escolha é: *"Vínculo, vigência, categoria de plano e regra contratual sempre participavam da mesma decisão — 'este beneficiário está elegível?' — e mudavam sob as mesmas políticas"*.

**3. Por que uma consolidação preserva as fronteiras e a outra não?**

A primeira alternativa preserva as fronteiras porque agrupa apenas as fatias que compartilham o mesmo propósito e regras de negócio (Elegibilidade). A segunda misturaria domínios que possuem autoridades distintas, regras e ciclos de vida diferentes, forçando Elegibilidade a compartilhar o mesmo espaço e banco de dados com Autorização e Auditoria.

**4. O que aconteceria se a mensageria fosse ligada sem definir as três regras?**

Se a equipe ignorasse o comportamento de repetição, a trilha de auditoria passaria a exibir eventos duplicados. Sistemas de eventos operam com entregas do tipo *at-least-once* logo, uma oscilação de rede faria a auditoria registrar a mesma decisão de autorização múltiplas vezes, corrompendo a precisão dos registros.

**5. Que dado tornaria visível um sinal de revisão?**

Para o sinal *"uma das regras passa a precisar de um ciclo de implantação isolado"*, a equipe precisaria medir o índice de bloqueio de deploy por dependência interna. O dado concreto seria rastrear quantas vezes um módulo pronto precisou esperar dias para ir a produção porque o macrosserviço inteiro estava travado devido a um bug no código de outro módulo vizinho.