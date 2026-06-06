# Multi-acquirer settlement reconciliation for a marketplace

> **Industry:** **Mobility / road-transport marketplace**
> **Technical theme:** **Fixed-width bank file** parsing · normalized model · key-based matching
> **Stack:** Python · pandas · Bash

---

## The manual pain

The marketplace receives payments through **two different acquirers** (credit card and
disbursement/wallet). Each delivers data in its **own bank-file layout** (fixed-width format, e.g.
`.RET`). The team had to reconcile settlements and find out, every day, **which payments arrived
wrong, in duplicate, or simply didn't arrive** — comparing layouts that don't talk to each other.

Checking bank files by hand is tedious, slow, and exactly the kind of task where one mistake turns
into direct financial loss.

## The specification / approach

I standardized both worlds into a **single model** before comparing:

```
acquirer A (.RET fixed-width) ┐
                              ├─→ layout parser ─→ normalized model ─→ key-based matching ─→ discrepancies
acquirer B (CSV report)       ┘                    (reference, amount, date)
```

- **Layout-driven parsers**: each acquirer has its field map (position/length); the parser reads the
  fixed-width record field by field.
- **Normalization**: everything converges to a common schema (reference, amount, date, status).
- **Key-based matching** flagging: missing, amount-mismatch, and duplicate.
- **Discrepancy report** ready for the finance team to act on.

## Architecture & stack

- **Python + pandas** for parsing, normalization and matching.
- **Bash** for folder orchestration (received → processed → difference).
- A directory structure that separates **raw input**, **processed** and **discrepancies** — simple,
  auditable and easy to operate.

## The role of Claude Code

- Sped up writing the fixed-width parsers (the most tedious and off-by-one-prone part).
- Helped design the common normalized model shared by both acquirers.
- Fast iteration over discrepancy cases (cents in amounts, different date formats).

## Result / impact

- Daily reconciliation of **two acquirers** with incompatible layouts became an automated routine.
- Discrepancies (missing/duplicate/amount) **flagged automatically**, reducing financial risk and
  close time.
- **Reusable** fixed-width parsers for new acquirers.

---

> Anonymized case study. Acquirer names, merchant IDs, real amounts and dates were omitted. The
> focus is the **parsing + normalization + multi-source matching pattern**.
