# Observações - Estilo em Camadas


Foi modificada a regra de transição de estado no método `cancelar()` da entidade `Consulta`, localizada no arquivo `dominio.py`. 
* **Regra original:** Proibia estritamente o cancelamento de qualquer consulta que não estivesse com o status `"agendada"`, levantando um `ValueError`.
* **Nova regra:** Se a consulta já estiver com o status `"cancelada"`, o método apenas retorna silenciosamente (sucesso), mantendo a restrição de erro apenas para consultas com status `"realizada"`.

**Trecho alterado em `dominio.py`:**
```python
    def cancelar(self) -> None:
        if self.status == "cancelada":
            return 
            
        if self.status != "agendada":
            raise ValueError(
                f"Impossível cancelar consulta com status '{self.status}'."
            )
        self.status = "cancelada"
```

Ao comparar os arquivos de execução, vimos uma diferença clara na hora de cancelar uma consulta que já estava cancelada. Antes (`saida-antes.txt`), o sistema barrava a ação e exibia um erro `HTTP 400 BAD REQUEST`. Com a nova regra (`saida-depois.txt`), o sistema passou a responder com `HTTP 200 OK`, concluindo o pedido com sucesso e devolvendo os dados da consulta sem interromper o programa.

**Mudança em `saida-depois.txt`:**
```text
    ────────────────────────────────────────────────────────────
  6. Tentando cancelar consulta já cancelada (esperado: HTTP 400)
    ────────────────────────────────────────────────────────────
    HTTP 200 OK → {'id': 2, 'status': 'cancelada'}
```

Esse resultado prova na prática como a Camada de Domínio é bem isolada e protegida. Foi mudado uma regra que fica apenas no coração do sistema (a entidade Consulta). Graças a essa separação em camadas, a parte que organiza as ações (Serviços) e a parte que exibe o resultado (Apresentação) se adaptaram sozinhas.