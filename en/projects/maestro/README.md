# Maestro — reference architecture of a multi-agent personal assistant

> **Type:** Personal product (own authorship)
> **Code status:** **private by design** — see the note below.
> **Stack:** Python 3.12 · FastAPI · PydanticAI · Pydantic v2 · SQLAlchemy 2 (async) · PostgreSQL 16 + pgvector · Redis 7 · Next.js 14 · TypeScript · Docker · Caddy

---

## Why this page has no code

Maestro is a personal assistant that processes **real personal data of third parties** (messages,
finances, calendar, contacts). Open-sourcing the code would leak information about people who **did
not consent** to being exposed — so the real repository is **private**.

This is a deliberate engineering decision: **portfolio maturity also means knowing what NOT to
publish.** What follows is the **architecture** — the part with demonstrable value — described
generically, with no personal data.

---

## What it is

A conversational assistant whose primary channel is messaging, designed to reduce **cognitive load**:
short responses, at most a few items at a time, and delegated decisions instead of endless lists. The
design focus is **cognitive accessibility** — a UX that doesn't punish the user and always ends in a
clear action.

## Multi-agent architecture

The system is a **council of specialized agents** orchestrated by a meta-agent, built on
**PydanticAI** (typed outputs with Pydantic, structured tool-calling):

```
                ┌─────────────── meta-agent (orchestrator) ───────────────┐
   message   →  │  route intent → pick specialist → compose response       │  →  short reply
                └──────────────────────────┬───────────────────────────────┘
                                            │
        ┌───────────────┬──────────────────┼──────────────────┬───────────────┐
   specialist A     specialist B      specialist C       invisible           memory
   (domain 1)       (domain 2)        (domain 3)         sub-agents          layer
                                                        (extraction/focus)
```

- **Specialized personas**: each agent has its own domain scope and a dedicated prompt.
- **Invisible sub-agents**: background tasks (fact extraction, prioritization) that don't talk to the
  user directly.
- **Typed outputs**: PydanticAI forces the model to return validated structures, not loose text —
  which makes behavior testable.

## 4-layer memory

```
L1  Redis            → hot cache / session context
L2  PostgreSQL       → structured, transactional history
L3  pgvector         → semantic memory (similarity search)
L4  Profile (JSON)   → stable user preferences and context
```

The combination gives the assistant **short- and long-term memory** without inflating the LLM
context on every call: only what's relevant is retrieved semantically (RAG over L3).

## Model routing (cost-aware)

Different tasks use different models, balancing quality and cost:

- **Fast/cheap model** for high-volume extraction and classification.
- **Mid-tier model** as the conversational default.
- **Deep-reasoning model** only for critical decisions.

## Integrations (generic patterns)

The backend integrates external services through reusable patterns (idempotent webhooks, OAuth,
queues):

- **Email** (real-time watch + parsing) and **calendar** (multi-account OAuth).
- **Messaging** as the primary UX channel.
- **Payments** and **document signing** via third-party APIs.
- A capture → normalization → categorization pipeline for the user's own financial data.

> Details of any specific business integration are **intentionally omitted**.

## Infrastructure

```
Next.js 14 (configuration/review dashboard)
        │  HTTPS (Caddy)
        ▼
FastAPI (async)  ──  SQLAlchemy 2 async  ──  PostgreSQL 16 + pgvector
        │
        ├── Redis (cache / queues)
        └── document storage (encrypted)
```

- **Docker Compose** for local orchestration and deploy.
- **Caddy** as a reverse proxy with automatic TLS.
- Sensitive documents **encrypted** at rest.

## The role of Claude Code

- Scaffolding of the multi-agent framework (personas, tools, Pydantic contracts).
- Design of the 4-layer memory and the semantic RAG over pgvector.
- Async FastAPI API patterns, OAuth and webhook idempotency.
- Model routing by task type.

## What it demonstrates

- **A real agent architecture** (orchestration + personas + typed outputs), not a chat wrapper.
- **Hybrid memory** (cache + relational + vector + profile) with semantic retrieval.
- **Production engineering**: async, multi-account OAuth, idempotent webhooks, encryption,
  Docker/Caddy.
- **Cost-awareness** with model routing.
- **Data ethics**: the ability to recognize third-party personal data and keep the code private.

---

> Architecture page written from scratch and anonymized. No prompt, name, business, financial or
> health data from the real product is here — and never will be.
