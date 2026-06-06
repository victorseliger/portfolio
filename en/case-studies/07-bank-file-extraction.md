# Bank-file metadata extraction and summarization

> **Industry:** **Bank reconciliation fintech**
> **Technical theme:** JSON parsing · regex extraction · pandas aggregation
> **Stack:** Python · pandas · regex · Excel

---

## The manual pain

The operation received, by email, **hundreds of bank files** from multiple acquirers and processors
(card, instant payments, collections, disbursements), with the metadata dumped into a **large JSON**
(API response). The recurring task: **filter the files of a specific merchant**, extract the
relevant fields and generate a summary — done by hand, record by record.

## The specification / approach

A small, straightforward utility that eliminates repetitive manual work:

1. **JSON parsing** of the metadata (hundreds of file records).
2. **Filter by merchant** (filename prefix).
3. **Regex extraction**: issuer identifier, date, time and sequence embedded in the name/field.
4. **Aggregation (pandas groupby/agg)**: summary by date and grand total.
5. **3-sheet Excel**: detail, by date, and overall summary.

## Architecture & stack

- **Python + pandas** for parsing, filtering and aggregation.
- **regex** to extract structured identifiers embedded in strings.
- **Multi-sheet Excel** output as an audit deliverable.

## The role of Claude Code

- Wrote the JSON parser and the extraction regular expressions quickly.
- Structured the `groupby/agg` and the multi-sheet Excel generation.

## Result / impact

- A **manual, recurring** filter/summary became a re-runnable script that takes seconds.
- A **reusable** pattern for any bank-file metadata extraction from JSON dumps.

---

> Anonymized case study. Real identifiers (tax IDs, merchant IDs), issuer names and files were
> omitted. The focus is the **extraction + aggregation pattern**.
