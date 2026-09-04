**1. Segundo o anúncio oficial, o que aconteceu em 2008 e qual operação da empresa ficou parada por três dias?**

Um banco de dados relacional e vertical, hospedado no datacenter da própria Netflix, corrompeu-se totalmente. Deixando a operação paralisada por três dias.

**2. A Netflix declara ter reconstruído a tecnologia em vez de transportá-la. Explique a diferença entre as duas coisas e o que cada uma muda na arquitetura resultante.**

**Transportar** significa pegar a arquitetura antiga (o monólito e o banco de dados central) e rodá-la em máquinas virtuais alugadas na nuvem; os mesmos problemas continuariam existindo, apenas mudando de endereço. **Reconstruir** significa reescrever o código do zero, dividindo o sistema em microsserviços independentes e substituindo o banco relacional por diversos bancos de dados NoSQL distribuídos. A arquitetura resultante ganha alta disponibilidade, tolerância a falhas e capacidade de escalar.

**3. O Open Connect inverte a lógica de cache: o conteúdo chega antes do pedido. Que propriedade do negócio da Netflix torna isso possível, e que tipo de serviço jamais conseguiria fazer o mesmo?**

A estratégia é possível porque a Netflix trabalha com um catálogo fechado, conhecido e altamente previsível. Aliado a potentes algoritmos de recomendação, a empresa prevê o que será assistido e envia os arquivos antecipadamente, funcionando como um cache invertido. Serviços de transmissões ao vivo jamais conseguiriam aplicar isso, precisando depender de abordagens estritamente reativas.

**4. Compare Isthmus, arquitetura ativa-ativa e Chaos Kong quanto ao modo de falha que cada um endereça.**

* **Isthmus:** Aborda falhas de resiliência parcial. Resolve a queda específica do balanceador de carga de uma região, permitindo novo roteamento de entrada.
* **Arquitetura Ativa-Ativa:** Resolve falhas de isolamento geográfico. Garante que mais de uma região opere e sincronize dados simultaneamente para assumir a carga caso a outra fique instável.
* **Chaos Kong:** Endereça a falha catastrófica total de uma região. Força o esvaziamento completo do tráfego para verificar se a região sobrevivente suporta o pico de acessos.

**5. O Chaos Monkey exige a plataforma Spinnaker e o Hystrix está em modo de manutenção desde 2018. Explique o que cada um desses fatos diz sobre reaproveitar a plataforma de ferramentas de outra empresa.**

Esses fatos demonstram que as ferramentas tecnológicas são retratos de necessidades datadas. O Chaos Monkey depender do Spinnaker prova que copiar uma ferramenta de falhas sem possuir a infraestrutura de entrega contínua da empresa original é inútil e perigoso. O *Hystrix* estar defasado mostra que adotar o ecossistema antigo de uma gigante pode significar implementar padrões que a própria criadora já abandonou.