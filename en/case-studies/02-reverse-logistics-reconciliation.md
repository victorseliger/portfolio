# Reverse-logistics reconciliation across a network of dozens of distributors

> **Industry:** **National leader in batteries** (manufacturer with a legal reverse-logistics obligation)
> **Technical theme:** Matching engine with **cascading tolerances** · historical carry-forward of pending items
> **Stack:** DuckDB · SQL · Python · pandas · openpyxl

---

## The manual pain

The manufacturer has a legal obligation to **recycle 100%** of what it sells: each distributor must
return the equivalent in scrap/used batteries to the factory. Every month, the factory's stock
ledger (per distributor, in kilos owed/credited) must be reconciled against the **inventory
movement ledger from each distributor's ERP**.

In practice: **one central ledger × dozens of regional spreadsheets** (~58 distributors), checked by
hand to find pending items (in-transit purchases, pending returns, pending warranties). An
exhausting monthly task, with tolerance rules nobody could apply consistently at scale.

## The specification / approach

I modeled the reconciliation as a **rule engine with cascading tolerances** over a normalized data
model:

```
central ledger (XLSX)  ┐
                       ├─→ normalized fact ─→ rules (cascade) ─→ pending items (4 types) ─→ output
ERP movement (XLSX)    ┘                                   └─→ unmatched items ─→ carry-forward to next month
```

- **Cascading matching**: first a **strict** match (±0.5 kg), then a **tolerant** one
  (±5 kg or ±2%), reducing false positives without losing coverage.
- **~18 rules** encoding the operations taxonomy (inbound, outbound, returns, warranties).
- **Historical carry-forward**: unmatched items aren't discarded — they're **rolled into the next
  month**, reflecting the reality of in-transit purchases/returns.
- **Outputs**: four pending-item types isolated and ready for the team to act on.

## Architecture & stack

- **DuckDB** in-process for the analytical SQL (joins, `EXCEPT`, window functions for dedup).
- Dimensional model (~32 tables): fact A = central ledger, fact B = movement, operation and
  classification dimensions, and a **pending-items ledger** that persists across months.
- **Python/pandas/openpyxl** to ingest dozens of heterogeneous XLSX files and generate the reports.

## The role of Claude Code

- I structured the **rule cascade** as declarative, testable SQL, instead of a tangle of `if`s.
- I used Claude Code to generate and review the tolerance rules and the backtest against a real month.
- I implemented a **backtest** to measure coverage and precision before trusting the result.

## Result / impact

- A **manual monthly reconciliation of ~58 spreadsheets** became a re-runnable pipeline.
- In the validation backtest: **coverage close to 100%** of the golden set with **~82% precision**,
  exposing a handful of false positives for focused human review.
- Pending items started being **tracked month over month** automatically, eliminating the rework of
  "rediscovering" in-transit items.

---

> Anonymized case study. Region/distributor codes, invoices, weights and the internal operations
> taxonomy were omitted. The focus is the **rule engine with cascading tolerances and historical
> carry-forward**.
