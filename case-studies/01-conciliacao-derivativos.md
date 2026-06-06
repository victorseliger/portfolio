# Conciliação automatizada de derivativos no fechamento mensal

> **Setor:** Multinacional do segmento de **energia / refino**
> **Tema técnico:** Pipeline SQL em camadas · motor de regras de classificação · matching multifonte
> **Stack:** DuckDB · SQL · Python · pandas · Excel

---

## A dor manual

A empresa faz hedge mensal de exposição cambial e de commodity (petróleo). Toda virada de mês, o
time de finanças precisava **conciliar o resultado dos derivativos** cruzando três fontes que nunca
batiam sozinhas:

1. Relatórios de marcação a mercado (MtM) — câmbio e commodity;
2. O razão contábil (ERP);
3. O balancete.

O processo vivia em um **papel de trabalho Excel de ~19 MB**, com classificação linha a linha e
roteamento manual para o plano de contas. Era lento, dependente de uma pessoa específica e
propenso a erro humano — exatamente o tipo de trabalho que não escala e trava o fechamento.

## A especificação / abordagem

Em vez de "melhorar a planilha", reescrevi o processo como um **pipeline de dados determinístico
em camadas**, com cada etapa auditável:

```
origens (XLSX)  →  raw  →  staging  →  fact  →  aggregate  →  matching  →  output (7 entregáveis)
```

- **Ingestão**: leitura das fontes (MtM câmbio, MtM commodity, razão, balancete) para tabelas raw.
- **Normalização (staging/fact)**: padronização de datas, sinais, moeda e granularidade.
- **Motor de regras de classificação**: ~7 regras que roteiam cada lançamento por estratégia
  (ex.: carga comercial, estoque, crack spread), separam ganho/perda e tratam prêmio pago/recebido.
- **Matching multifonte**: cruzamento contra razão e balancete com tolerância controlada.
- **Saída**: 7 planilhas Excel prontas para o fechamento, com as divergências já evidenciadas.

A lógica de classificação ficou **declarativa em SQL** (não escondida em fórmulas de célula), o que
torna cada decisão rastreável e revisável.

## Arquitetura & stack

- **DuckDB** como engine OLAP in-process (SQL analítico sobre arquivos, sem servidor).
- **SQL** para toda a lógica de negócio (camadas raw→stg→fact→agg→match→out).
- **Python + pandas** para orquestração da ingestão e geração dos Excel finais.
- Separação clara entre **dado bruto**, **regra** e **entregável** — cada camada é materializável e
  inspecionável isoladamente.

## O papel do Claude Code

- Traduzi o papel de trabalho manual em **especificação executável**: mapeei cada regra implícita
  da planilha para uma regra SQL explícita.
- Usei o Claude Code para acelerar a escrita das queries em camadas e dos testes de sanidade
  (conferência de totais por estratégia, amarração com o balancete).
- Iteração rápida sobre casos de borda (sinais invertidos, prêmios, estratégias mistas).

## Resultado / impacto

- Fechamento de derivativos que era **manual e dependente de uma pessoa** virou um **pipeline
  reexecutável** em minutos.
- **Amarração com o razão/balancete dentro da tolerância**, com divergências isoladas
  automaticamente para revisão humana.
- Conhecimento deixou de ser tácito (preso na planilha) e passou a ser **versionável e auditável**.

---

> Case anonimizado. Nomes de empresa, contas, estratégias e valores reais foram omitidos
> deliberadamente. O foco é o **padrão de engenharia transferível**, não os dados do cliente.
