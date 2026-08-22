
**Estado:** aceita
**Data:** 2026-08-19

## Contexto
A plataforma hospitalar precisa integrar três capacidades principais que operam sob exigências opostas: a Agenda, a Triagem Administrativa e o Faturamento. O problema central é definir uma estrutura arquitetural base que acomode essas naturezas distintas de forma segura, garantindo o isolamento do desenvolvimento e a simplicidade da operação, sem introduzir a complexidade de sistemas distribuídos prematuramente. O escopo deste registro foca na implantação inicial da plataforma e na organização macro, não se aprofundando nos esquemas exatos de banco de dados.

## Forças
* **Consistência (Agenda):** necessidade de transações atômicas locais para evitar reservas duplas concorrentes. (Cenário: 50 solicitações simultâneas para o mesmo horário resultam em 0 reservas duplicadas).
* **Extensibilidade (Triagem):** necessidade de acoplar etapas opcionais e regras de negócio específicas de cada unidade hospitalar sem alterar o fluxo central. 
* **Vazão / Throughput (Faturamento):** necessidade de dar conta do processamento em massa de milhares de registros administrativos sem derrubar os recursos do sistema para os usuários interativos.

## Alternativas
* **Separação imediata em microsserviços (implantação independente por capacidade):** atenderia perfeitamente à força da escala independente (especialmente para os picos do faturamento), mas traria o alto risco operacional e de engenharia de coordenar transações distribuídas (essenciais para a consistência da Agenda); foi descartada porque não possuímos evidência ou volume atual que justifique o custo da complexidade de rede.

## Decisão
Adotar o **Monólito Modular** como estrutura de implantação inicial. A Agenda, a Triagem e o Faturamento conviverão em uma única unidade de implantação (processo compartilhado e banco de dados único), porém organizados internamente em fatias lógicas com rigoroso controle de fronteiras. 

Essa estrutura permite que cada módulo implemente o padrão interno adequado à sua força (Camadas para Agenda, Microkernel para Triagem e Pipes and Filters para Faturamento) e se comunique com os demais apenas por meio de interfaces públicas contratadas, justificando a flexibilidade aliada à simplicidade operacional.

## Consequências
* **Consequência favorável:** garantir que "nunca ocorram duas reservas no mesmo horário" torna-se tecnicamente simples. O efeito é observável na utilização do banco de dados relacional como garantidor atômico (transação local), eliminando a necessidade de coordenar serviços distribuídos.
* **Consequência desfavorável:** aceitamos o custo e a restrição de que agenda e faturamento dividem o mesmo processo físico. Falhas críticas de memória ou gargalos de CPU gerados por lotes de faturamento podem impactar a escala e a resiliência da agenda enquanto a implantação unificada permanecer verdadeira.

## Evidências
* Avaliação teórica do diagrama de sequência da capacidade de Agenda confirmando o funcionamento atômico.
* Testes de fronteiras lógicas simulados por ferramenta de análise estática e protótipos de integração local (arquivos `test_estilos.py`).
* Prova de conceito demonstrando o fluxo sintético de faturamento dentro da mesma aplicação.

## Revisão
Este registro deverá ser reaberto e a extração de serviços reavaliada se a latência da funcionalidade de Agenda (p95) ultrapassar de forma sustentada 500 ms durante a janela de execução do lote diário de Faturamento.