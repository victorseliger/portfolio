# Conciliação de logística reversa em rede com dezenas de distribuidores

> **Setor:** **Líder nacional em baterias** (indústria com obrigação legal de logística reversa)
> **Tema técnico:** Motor de regras de matching com **tolerâncias em cascata** · arrasto histórico de pendências
> **Stack:** DuckDB · SQL · Python · pandas · openpyxl

---

## A dor manual

O fabricante tem obrigação legal de **reciclar 100%** do que vende: cada distribuidor precisa
devolver à fábrica o equivalente em sucata/baterias usadas. Mensalmente, a ficha de estoque da
fábrica (por distribuidor, em quilos devidos/creditados) precisa ser conciliada contra o **cardex
de movimentação do ERP de cada distribuidor**.

Na prática: **uma ficha central × dezenas de planilhas regionais** (~58 distribuidores), conferidas
à mão para achar pendências (compras em trânsito, devoluções pendentes, garantias pendentes). Um
trabalho mensal exaustivo, com regras de tolerância que ninguém conseguia aplicar de forma
consistente em escala.

## A especificação / abordagem

Modelei a conciliação como um **motor de regras com tolerâncias em cascata** sobre um modelo de
dados normalizado:

```
ficha central (XLSX)  ┐
                      ├─→ fact normalizado ─→ regras (cascata) ─→ pendências (4 tipos) ─→ output
movimentação ERP (XLSX) ┘                                   └─→ pendências não casadas ─→ arrasto p/ mês seguinte
```

- **Casamento em cascata**: primeiro match **estrito** (±0,5 kg), depois **tolerante**
  (±5 kg ou ±2%), reduzindo falsos positivos sem perder cobertura.
- **~18 regras** codificando a taxonomia de operações (entradas, saídas, devoluções, garantias).
- **Arrasto histórico**: itens não casados não são descartados — são **roladas para o mês
  seguinte**, refletindo a realidade de compras/devoluções em trânsito.
- **Saídas**: quatro tipos de pendência isolados e prontos para ação do time.

## Arquitetura & stack

- **DuckDB** in-process para o SQL analítico (joins, `EXCEPT`, window functions para dedup).
- Modelo dimensional (~32 tabelas): fato A = ficha central, fato B = movimentação, dimensões de
  operação e classificação, e um **ledger de pendências** que persiste entre meses.
- **Python/pandas/openpyxl** para ingestão de dezenas de XLSX heterogêneos e geração dos relatórios.

## O papel do Claude Code

- Estruturei a **cascata de regras** como SQL declarativo e testável, em vez de um emaranhado de
  `if`s.
- Usei o Claude Code para gerar e revisar as regras de tolerância e o backtest contra um mês real.
- Implementei um **backtest** para medir cobertura e precisão antes de confiar no resultado.

## Resultado / impacto

- Conciliação **mensal manual de ~58 planilhas** virou um pipeline reexecutável.
- No backtest de validação: **cobertura próxima de 100%** do "golden set" com **precisão ~82%**,
  expondo um punhado de falsos positivos para revisão humana focada.
- Pendências passaram a ser **rastreadas mês a mês** automaticamente, eliminando o retrabalho de
  "redescobrir" itens em trânsito.

---

> Case anonimizado. Códigos de região/distribuidor, NFs, pesos e a taxonomia interna de operações
> foram omitidos. O foco é o **motor de regras com tolerâncias em cascata e arrasto histórico**.
