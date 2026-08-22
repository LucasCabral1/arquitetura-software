# Observações - Pipes and Filters

Foi modificada a regra de aprovação na classe `FiltroPorExperienciaMinima`, que atua como um *Tester* no pipeline de triagem.
* **Regra original:** O filtro reprovava apenas os candidatos cuja experiência fosse estritamente menor (`<`) que o exigido pela vaga[cite: 5].
* **Nova regra:** A política foi alterada para exigir que a experiência supere o mínimo estipulado. Trocamos o operador para menor ou igual (`<=`), reprovando também os candidatos que possuem a experiência no limite exato da vaga.

**Trecho alterado em `testers.py`:**
```python
            if c.anos_experiencia <= self._minimo:
                print(
                    f"  [REPROVADO] {c.candidato_nome}: "
                    f"{c.anos_experiencia} ano(s) não supera o rigor do mínimo {self._minimo}"
                )
                continue

```

Ao olhar o arquivo `saida-depois.txt`, notamos que mais candidatos apareceram com o status `[REPROVADO]` na fase que verifica a experiência. Como deixamos essa regra mais rígida, menos currículos conseguiram avançar pelo fluxo. Por causa disso, o relatório gerado na última etapa mostrou uma lista de aprovados menor do que na primeira vez.

**Mudança em `saida-depois.txt`:**
```text
    Pipeline: Pipeline(LeitorDeCurriculos → ValidadorDeCurriculo → NormalizadorDeCampos → FiltroPorExperienciaMinima → FiltroPorPretensaoSalarial → CalculadorDeScore → RelatorioDeTriagem)

  [DESCARTADO] Currículo id=3: nome ausente
  [REPROVADO] Bruno Rocha: 1 ano(s) < mínimo 3
  [REPROVADO] Elena Souza: 3 ano(s) < mínimo 3
  [REPROVADO] Clara Mendes: pretensão R$22,000 > máximo R$18,000
```

Esse resultado mostra muito bem a principal vantagem do estilo Pipes and Filters: as partes do sistema são independentes e não misturam informações.

Quando tornamos apenas um filtro mais exigente, diminuímos imediatamente a quantidade de dados que foi para a frente. As próximas etapas — como a que calcula a pontuação e a que imprime a tela final — continuaram funcionando perfeitamente com esse lote menor de currículos, sem precisarem de nenhuma alteração em seus códigos. Isso prova que cada etapa trabalha sozinha, focada apenas em fazer a sua parte, sem depender de como as outras funcionam internamente.