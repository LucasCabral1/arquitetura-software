# Respostas às Questões para Discussão

**1. Liste as quatro etapas da migração descritas no vídeo de 2018, na ordem em que ocorreram, e explique por que e-mail e filas vieram antes da quebra do monólito.**
* **Etapa 1:** Serviços de envio de e-mail.
* **Etapa 2:** Serviços de gestão de filas e mensagens.
* **Etapa 3:** Quebra da plataforma central em microsserviços, migrados um a um.
* **Etapa 4:** Migração do restante de uma só vez devido à insustentabilidade do data center.
* **Por que e-mail e filas vieram antes:** Porque são aplicações *stateless* (sem estado), de baixo risco e falha tolerável. A migração deles serviu como um laboratório de baixo impacto para que a equipe aprendesse a operar na nuvem com segurança, antes de estrangular o monolito principal do negócio.

**2. Explique as três condições técnicas que precisam existir para que 60% da capacidade de pico rode em instâncias spot, e relacione cada uma a um mecanismo estudado neste módulo.**
1. **Aplicações sem estado (*stateless*):** Os processos não podem salvar dados de negócio na própria máquina, pois se a instância for encerrada pela AWS, os dados não são perdidos.
2. **Orquestrador automatizado:** É necessário um sistema que monitore os avisos de interrupção (de 2 minutos) e recrie automaticamente as instâncias perdidas em novos servidores, sem depender de intervenção humana (Infraestrutura como Código).
3. **Margem de segurança (folga):** A capacidade instalada não pode estar no limite. É preciso ter réplicas suficientes rodando simultaneamente para que a perda repentina de algumas instâncias não derrube o serviço enquanto as substitutas não sobem.

**3. Compare as três formas de compra de instância citadas no caso quanto a preço, compromisso e risco de interrupção, e diga qual parcela da curva de demanda cada uma cobre.**
* **Instância Reservada:** Tem alto desconto e alto compromisso (contratos de 1 a 3 anos), com risco zero de interrupção. Cobre a **base previsível da demanda** (o vale entre os picos).
* **Instância Sob Demanda:** É a mais cara, não exige compromisso de prazo e não sofre interrupção. Cobre a **folga operacional e o tráfego imprevisto**.
* **Instância Spot:** Possui o maior desconto, sem compromisso de prazo, mas com risco contínuo de interrupção (o provedor pode tomar com 2 minutos de aviso). Cobre a maior parte do **volume no horário de pico**.

**4. O caso afirma que a elasticidade permitiu atribuir um custo por pedido. Explique por que essa grandeza não existia no desenho anterior, com infraestrutura em data center próprio.**
No data center físico, os servidores precisavam ser comprados com capacidade máxima para suportar o pico, mas ficavam ligados e ociosos no resto do dia. O custo da infraestrutura era um bloco mensal fixo que não se dividia pelas vendas. Com a elasticidade da nuvem, a infraestrutura é provisionada apenas em resposta às transações que estão acontecendo. Ao atrelar a capacidade computacional ativamente à demanda do momento, o negócio consegue chegar ao custo unitário exato por pedido.

**5. Entre 2018 e hoje, a unidade de escala mudou de instância EC2 para contêiner orquestrado. Descreva o que essa mudança altera na densidade de uso de cada máquina e relacione isso à redução de 40% no custo.**
Enquanto a máquina virtual (EC2) carrega um sistema operacional pesado para cada aplicação, o contêiner é muito mais leve e compartilha recursos do hospedeiro. Essa mudança aumenta massivamente a **densidade** da infraestrutura, permitindo "empacotar" muito mais aplicações rodando simultaneamente dentro de um mesmo servidor físico. Ao aproveitar melhor o espaço computacional, o iFood precisou ligar menos servidores para atender a uma demanda ainda maior, o que reduziu seu custo de infraestrutura em 40%.