# Matriz de estilos arquiteturais por capacidade

| Estilo | Agenda | Triagem administrativa | Faturamento |
|---------|---------|---------|---------|
| Camadas | Ideal. Por separar interface, dados e caso de uso, protegendo as regras de negócio |Ideal. Consegue separar as diferentes funcionalidades, mantendo a organização e isolamento| Não é ideal. Organiza validações e consolidação em etapas conhecidas, porém não atua bem em processamento massivo em lotes |
| Pipes and Filters | Não é ideal. Porque o fluxo é interativo e exige consistência imediata | Não é  ideal. Permite acrescentar etapas lineares ao fluxo, mas dificulta ramificações mais complexas. | Ideal. O faturamento já é naturalmente um pipeline de validação, ajuste, consolidação e envio em lote. |
| Microkernel | Não é ideal. Não é necessário extensabilidade | Ideal. Por necessitar de extensabilidade, o contrato de plugins permite isso.  | Não é ideal. As etapas são fixas, e com isso não aproveita do sistema de plugins que, é a principal vantagem desta arquitetura. |
| Monólito modular | Ideal. Visto que uma agenda não tem uma escalabilidade exigida | Não é ideial. Devido a filial precisar de regras específicas. | Falta evidência para concluir. |

