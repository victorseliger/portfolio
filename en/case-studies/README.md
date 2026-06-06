# Case studies

**Anonymized** case studies of real projects. Due to confidentiality (NDA), no client/company name,
proprietary code or production data is exposed — each case uses only an **industry hint** and focuses
on the **transferable engineering pattern**.

Each study follows the same structure: **manual pain → specification/approach → architecture &
stack → role of Claude Code → result/impact**.

## Reconciliation & data engineering

| # | Case | Industry (anon.) | Technical theme |
|---|------|------------------|-----------------|
| 01 | [Derivatives reconciliation](01-derivatives-reconciliation.md) | Energy / refining | Layered SQL pipeline + rule engine |
| 02 | [Reverse-logistics reconciliation](02-reverse-logistics-reconciliation.md) | Batteries (industry) | Cascading-tolerance matching + carry-forward |
| 03 | [Payment reconciliation forensics](03-payment-reconciliation-forensics.md) | Cosmetics / retail | Forensic matching + root-cause diagnosis |
| 04 | [Multi-acquirer reconciliation](04-multi-acquirer-reconciliation.md) | Mobility / transport | Bank-file parsing + multi-source matching |
| 07 | [Bank-file extraction](07-bank-file-extraction.md) | Reconciliation fintech | JSON parsing + regex + aggregation |

## Platforms & automation

| # | Case | Industry (anon.) | Technical theme |
|---|------|------------------|-----------------|
| 05 | [Multi-tenant pension platform](05-multitenant-pension-platform.md) | Pension SaaS (RPPS) | No-code → FastAPI/Postgres migration + multi-tenancy |
| 06 | [Operations dashboard ETL](06-operations-dashboard-etl.md) | Data company (internal) | Idempotent SQL ETL + webhook + decoupled storage |
| 08 | [From meeting to specification](08-spec-from-meetings.md) | Inventory reconciliation | Chunked transcription → structured spec |

## Personal products

| # | Case | Type | Technical theme |
|---|------|------|-----------------|
| 09 | [Screen monitor + AI](09-screen-monitor-ai.md) | Own product | Distributed system + WebSocket + LLM vision |
| — | [Maestro (reference architecture)](../projects/maestro/) | Own product | Multi-agent assistant (PydanticAI) |
