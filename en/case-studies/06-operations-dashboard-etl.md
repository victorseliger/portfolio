# Real-time operations dashboard: work management → Postgres → webhook → HTML

> **Industry:** **Internal operations dashboard** at a data company
> **Technical theme:** Idempotent SQL ETL · **webhook** rendering · storage decoupling
> **Stack:** n8n · Supabase (PostgreSQL + PostgREST) · work-management API · JavaScript (Node) · vanilla HTML/CSS/JS

---

## The manual / technical pain

The operations team tracked capacity, worklogs and work-in-progress **manually**, pulling data from
the work-management tool. The first automated version stored everything in **n8n's internal tables**
— which **hit storage limits** and couldn't sustain a real-time dashboard.

## The specification / approach

The turning point was to **decouple storage** from the automation tool and make the transformation
**deterministic**:

```
work management (API)
        │  n8n (sync + polling + transform)
        ▼
Supabase / PostgreSQL (raw)  ──  ETL via idempotent SQL functions (rebuild_*)
        │
        ▼  n8n webhook (renders HTML)
dashboard (vanilla HTML/CSS/JS)
```

- **Dedicated storage**: swapped n8n DataTables for **Postgres (Supabase)**, removing the storage
  ceiling.
- **Idempotent ETL**: `rebuild_*` SQL functions that rebuild the aggregated views deterministically
  (re-runnable with no side effects).
- **Soft-delete with audit trail**, preserving history.
- **Webhook rendering**: n8n only renders the final HTML, minimizing execution overhead.
- **Zero-framework frontend** (vanilla JS), reducing dependencies and maintenance surface.

## Architecture & stack

- **n8n** for sync/polling of the work-management API and as the rendering layer (webhook).
- **Supabase / PostgreSQL** as the analytical source of truth; **PostgREST** for access.
- **SQL** for all ETL (idempotent `rebuild_*` functions).
- **Vanilla HTML/CSS/JS** on the frontend.

## The role of Claude Code

- Designed the **idempotent SQL** ETL functions and the Supabase table model.
- Sped up the DataTables → Postgres migration and the soft-delete/audit logic.
- Helped with the **webhook rendering** pattern and the framework-less frontend.

## Result / impact

- The dashboard went from **storage at its limit** to a **scalable, real-time** model.
- A **deterministic, re-runnable ETL** (no dirty state), with a **soft-delete audit trail**.
- A **framework-less frontend** = fewer dependencies, simpler deploy and maintenance.

---

> Anonymized case study. Company name, the management tool, team/project data and any credentials
> were omitted. The focus is the **idempotent ETL + decoupled storage + webhook pattern**.
