# Automated derivatives reconciliation for monthly close

> **Industry:** Multinational in the **energy / refining** sector
> **Technical theme:** Layered SQL pipeline · rule-based classification engine · multi-source matching
> **Stack:** DuckDB · SQL · Python · pandas · Excel

---

## The manual pain

The company hedges FX and commodity (oil) exposure every month. At each month-end, the finance team
had to **reconcile the derivatives result** by cross-checking three sources that never agreed on
their own:

1. Mark-to-market (MtM) reports — FX and commodity;
2. The accounting ledger (ERP);
3. The trial balance.

The process lived in a **~19 MB Excel working paper**, with line-by-line classification and manual
routing to the chart of accounts. It was slow, dependent on one specific person, and prone to human
error — exactly the kind of work that doesn't scale and stalls the close.

## The specification / approach

Instead of "improving the spreadsheet", I rewrote the process as a **deterministic, layered data
pipeline**, where every step is auditable:

```
sources (XLSX)  →  raw  →  staging  →  fact  →  aggregate  →  matching  →  output (7 deliverables)
```

- **Ingestion**: read the sources (FX MtM, commodity MtM, ledger, trial balance) into raw tables.
- **Normalization (staging/fact)**: standardize dates, signs, currency and granularity.
- **Classification rule engine**: ~7 rules routing each entry by strategy (e.g. commercial cargo,
  inventory, crack spread), separating gain/loss and handling premium paid/received.
- **Multi-source matching**: cross-checking against ledger and trial balance with controlled tolerance.
- **Output**: 7 ready-to-use Excel files for the close, with discrepancies already surfaced.

The classification logic became **declarative SQL** (not hidden in cell formulas), making every
decision traceable and reviewable.

## Architecture & stack

- **DuckDB** as an in-process OLAP engine (analytical SQL over files, no server).
- **SQL** for all business logic (raw→stg→fact→agg→match→out layers).
- **Python + pandas** to orchestrate ingestion and generate the final Excel files.
- Clear separation between **raw data**, **rule** and **deliverable** — each layer is materializable
  and independently inspectable.

## The role of Claude Code

- I translated the manual working paper into an **executable specification**: mapping each implicit
  rule of the spreadsheet to an explicit SQL rule.
- I used Claude Code to speed up writing the layered queries and the sanity checks (totals per
  strategy, tie-out against the trial balance).
- Fast iteration over edge cases (inverted signs, premiums, mixed strategies).

## Result / impact

- A derivatives close that was **manual and dependent on one person** became a **re-runnable
  pipeline** in minutes.
- **Tie-out with the ledger/trial balance within tolerance**, with discrepancies automatically
  isolated for human review.
- Knowledge stopped being tacit (locked in the spreadsheet) and became **versionable and auditable**.

---

> Anonymized case study. Company name, accounts, strategies and real figures were deliberately
> omitted. The focus is the **transferable engineering pattern**, not the client's data.
