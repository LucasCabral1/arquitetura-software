
## 1. Comparação
* **Contrato Explícito:** É o documento de design (Design-First). Ele define as regras, intenções e a estrutura da API antes mesmo de haver código. Serve como um acordo estático entre quem provê e quem consome a API.
* **Contrato Gerado:** É a documentação viva gerada dinamicamente a partir do código-fonte da implementação (via modelos Pydantic). Ele mostra o que a API *realmente* está expondo e aceitando naquele momento. 
* **Execução:** Representa o comportamento real da aplicação em tempo de execução. Como testado no Bruno (recebendo erro `422` ao omitir o CPF) e provado nos testes automatizados, a execução é o que valida se o código implementado obedece às regras estipuladas no contrato na prática.

## 2. Evidência de Falha Deliberada (Experimento)
Durante a exploração do laboratório, foi criado um arquivo `openapi-experimento.yaml` onde o exemplo de mídia do CPF foi alterado de `"12345678901"` para `"123"`. 

