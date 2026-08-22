flowchart TB
    entrada["Interfaces autorizadas"] --> app["Aplicação"]
    
    subgraph estrutura["Monólito Modular"]
        app --> moduloAgenda["Agenda — Camadas"]
        app --> moduloTriagem["Triagem — Microkernel"]
        app --> moduloFaturamento["Faturamento — Pipes and Filters"]
        
        moduloAgenda --> registro["Módulo Auditoria"]
        moduloTriagem --> registro
        moduloFaturamento --> registro
    end
    
    moduloTriagem --> externo["Adaptador da Operadora"]
    moduloFaturamento --> externo






**Texto alternativo:**
Uma aplicação de plataforma recebe requisições de interfaces autorizadas e as encaminha para os módulos Agenda, Triagem e Faturamento, todos contidos dentro de um Monólito Modular. Esses três módulos enviam registros para o Módulo Auditoria (mantendo registro das operações), que também é interno. Fora do monólito, os módulos de Triagem e Faturamento se comunicam com o Adaptador da Operadora.

**Legenda:**
*Figura 1 — Estrutura inicial da plataforma em Monólito Modular com estilos internos específicos por capacidade. Fonte: elaboração própria.*

**Leitura textual da figura:**
As "Interfaces autorizadas" enviam a requisição para a "Aplicação". Dentro do quadro "Monólito Modular", a aplicação encaminha o fluxo para "Agenda — Camadas", "Triagem — Microkernel" ou "Faturamento — Pipes and Filters". Estes três módulos enviam os fatos ocorridos para o "Módulo Auditoria", que está dentro da mesma estrutura. Fora do quadro do monólito, a Triagem e o Faturamento conversam com o componente externo "Adaptador da Operadora".