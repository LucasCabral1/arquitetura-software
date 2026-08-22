# Observações Microkernel

### 1. A condição alterada
Foi modificada a constante de regra de negócio `ALIQUOTA` dentro do plugin `ImpostoRJPlugin`, localizado no arquivo `impostos_rj.py`.
* **Regra original:** A alíquota única do ICMS para o Rio de Janeiro era de 20% (`0.20`).
* **Nova regra:** Simulando um aumento na carga tributária do estado, a alíquota foi reajustada para 30% (`0.30`).

**Trecho alterado em `impostos_rj.py`:**
```python
     """ICMS fluminense com alíquota única de 30%."""

    nome = "ICMS-RJ"
    ALIQUOTA = 0.30   # NOVA REGRA: Alíquota aumentada

```
Comparando os arquivos `saida-antes.txt` e `saida-depois.txt`, vimos que a mudança funcionou de forma bem isolada: afetou apenas a fatura do cliente do Rio de Janeiro. O sistema calculou o novo valor do `ICMS-RJ`, deixando a cobrança final mais cara. Por outro lado, as faturas de São Paulo e Minas Gerais continuaram exatamente como antes.

**Mudança em `saida-depois.txt`:**
```text
    Resultado da emissão:
    ICMS-RJ: R$2,520.00
    Frete: R$0.00
    ─────────────────────────────
    TOTAL: R$10,920.00
    Notificações: email:nf@distribrj.com
```

Esse resultado mostra na prática a grande vantagem do estilo Microkernel: o isolamento das regras. O cálculo do imposto mudou apenas dentro do plugin ("cartucho") do Rio de Janeiro. O Núcleo do sistema (o motor principal) não precisou de nenhuma alteração e nem precisou saber da nova taxa; ele apenas chamou o plugin na hora certa. Além disso, os plugins vizinhos  continuaram trabalhando normalmente.