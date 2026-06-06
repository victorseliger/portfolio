# Reconciliation forensics: hunting for missing settlement records

> **Industry:** Multinational in **cosmetics / retail** (international operation)
> **Technical theme:** **Forensic matching** with multi-variant normalization · exhaustive cross-check · root-cause diagnosis
> **Stack:** Python · pandas · fixed-width file parsing · Excel

---

## The manual pain

The daily card settlement sent by the **payment processor** didn't match the **internal order
ledger**. The team expected a handful of transactions on a specific day (some debit, some credit),
but the file received didn't include the expected credits — and the internal ledger had **tens of
thousands of rows**, making a manual check infeasible.

The business question was simple and expensive: **"where are the missing transactions, and why?"**

## The specification / approach

I treated it as a **forensic data problem**, not a routine reconciliation:

1. **Full field extraction**: a parser for the processor's fixed-width file, opening all ~36 fields
   of each record (instead of only the "obvious" ones).
2. **Multi-variant normalization**: since identifiers diverge across systems, each key was tested in
   **5 variants** — original, without leading zeros, with suffix, digits-only, and currency-formatted.
3. **Exhaustive cross-check** against the internal ledger (tens of thousands of rows), measuring the
   match in each variant.
4. **Variance report**: quantifying what matched, what didn't, and the signature of what was missing.

## Diagnosis (the real deliverable)

The exhaustive cross-check showed that **all received records were debits** — no credits.
Conclusion: it wasn't a reconciliation error, but an **integration gap** — the processor sends
debits and credits in **separate files**. The recommendation was to request the corresponding credit
file, rather than "hunting" for rows that never arrived.

That's the value of a good investigation: turning "the numbers don't match" into an **actionable
root cause**.

## Architecture & stack

- **Python + pandas** for parsing, normalization and cross-checking.
- A layout-driven **fixed-width file parser** (field by field).
- **Multi-sheet Excel** generation (detail, by date, summary) for evidence and audit.

## The role of Claude Code

- Sped up writing the fixed-width parser and the per-variant normalization functions.
- Helped structure the **exhaustive cross-check** and the variance report in a readable way.
- Allowed fast iteration over hypotheses ("what if the key has leading zeros? what about a suffix?")
  until the root cause was isolated.

## Result / impact

- A close-blocking impasse ("transactions are missing") was solved with a **root-cause diagnosis**
  instead of manual rework.
- A **reusable forensic matching pattern** (multi-variant normalization + exhaustive cross-check +
  variance) applicable to any payment reconciliation.

---

> Anonymized case study. Order/representative IDs, amounts, and processor/internal-system names were
> omitted. The focus is the **forensic reconciliation methodology**.
