# Multi-tenant platform migration: from no-code workflows to a production backend

> **Industry:** **Multi-tenant pension SaaS** (own pension funds — Brazilian "RPPS")
> **Technical theme:** No-code → **FastAPI + PostgreSQL** migration · multi-tenancy · external-data ETL
> **Stack:** Python 3.12 · FastAPI · SQLAlchemy 2 (async) · Pydantic v2 · Celery · Alembic · PostgreSQL 16 · Next.js 14 · TypeScript · Docker · Caddy

---

## The manual / technical pain

The original platform was built on **no-code workflows (n8n)**. It worked to get started, but hit a
ceiling: hard to scale, fragile for complex rules, and the pilot was already **exceeding the tool's
storage limits**. The goal was to support **10+ funds** (tenants) by year-end — no-code wouldn't get
there.

## The specification / approach

I rewrote the platform as a **real backend**, keeping the domain (portfolio management, positions,
movements, monthly close, interest-rate study and reporting) while gaining scale, testability and
multi-tenancy:

- **Multi-tenancy** with per-tenant isolation throughout the data model.
- **Domains**: contract catalog, daily price snapshots, monthly close (fund settlement), rate study
  (multiple methodologies), workflow orchestration and reporting.
- **Reports** in multiple formats (multi-sheet XLSX + PDF + ZIP).
- **External data integration** (market sources and macro indicators) with an architecture ready for
  real connectors and stubs during evolution.
- **RBAC / auth** and observability from the start.

## Architecture & stack

```
Next.js 14 (multi-tenant dashboard)
        │  HTTPS (Caddy TLS)
        ▼
FastAPI (async)  ──  SQLAlchemy 2 async  ──  PostgreSQL 16
        │
        ├── Celery (tasks/close/ETL)  ── Redis
        └── Alembic (versioned migrations)
```

- **Backend**: FastAPI + Pydantic v2 + SQLAlchemy 2 async; dependency injection; Celery for heavy
  tasks; Alembic for migrations.
- **Frontend**: Next.js 14 (App Router) + TypeScript + Tailwind, per-tenant dashboard (portfolio,
  position, movement, reports, study, sources, downloads).
- **Infra**: Docker Compose, Caddy as a TLS reverse proxy.

## The role of Claude Code

- Sped up the **module-by-module rewrite** (contracts, close, rates, reports), delivered
  incrementally across a sequence of PRs.
- Repetitive patterns (routers, Pydantic schemas, async repositories, migrations) generated and
  reviewed with AI, keeping architectural consistency.
- Support on multi-tenant modeling and the external-source ETL pipelines.

## Result / impact

- The platform went from **no-code at its limit** to a **scalable, testable backend**, ready for
  multiple tenants.
- **Incremental delivery via PRs**, with the core modules (contracts, close, rates, reports) live.
- A **versioned codebase with migrations and observability**, removing the no-code fragility for
  complex business rules.

---

> Anonymized case study. Product name, pilot tenant, production IPs and the proprietary pension
> calculation logic were omitted. The focus is the **no-code → multi-tenant backend migration
> pattern**.
