# Migração de plataforma multi-tenant: de workflows no-code para backend em produção

> **Setor:** **SaaS multi-tenant de previdência (RPPS)** — fundos de previdência próprios
> **Tema técnico:** Migração no-code → **FastAPI + PostgreSQL** · multi-tenancy · ETL de fontes externas
> **Stack:** Python 3.12 · FastAPI · SQLAlchemy 2 (async) · Pydantic v2 · Celery · Alembic · PostgreSQL 16 · Next.js 14 · TypeScript · Docker · Caddy

---

## A dor manual / técnica

A plataforma original era construída em **workflows no-code (n8n)**. Funcionava para começar, mas
batia no teto: difícil de escalar, frágil para regras complexas, e o piloto já estava **excedendo
os limites de armazenamento** da ferramenta. A meta era suportar **mais de 10 fundos** (tenants)
até o fim do ano — o no-code não chegaria lá.

## A especificação / abordagem

Reescrevi a plataforma como um **backend de verdade**, mantendo o domínio (gestão de carteira,
posições, movimentos, fechamento mensal, estudo de taxa de juros e relatórios) e ganhando escala,
testabilidade e multi-tenancy:

- **Multi-tenancy** com isolamento por tenant em todo o modelo de dados.
- **Domínios**: catálogo de contratos, snapshots diários de preço, fechamento mensal (liquidação do
  fundo), estudo de taxa (múltiplas metodologias), orquestração de workflow e relatórios.
- **Relatórios** em múltiplos formatos (XLSX multi-aba + PDF + ZIP).
- **Integração de dados externos** (fontes de mercado e indicadores macro) com arquitetura
  preparada para conectores reais e stubs durante a evolução.
- **RBAC / auth** e observabilidade desde o início.

## Arquitetura & stack

```
Next.js 14 (dashboard multi-tenant)
        │  HTTPS (Caddy TLS)
        ▼
FastAPI (async)  ──  SQLAlchemy 2 async  ──  PostgreSQL 16
        │
        ├── Celery (tarefas/fechamento/ETL)  ── Redis
        └── Alembic (migrations versionadas)
```

- **Backend**: FastAPI + Pydantic v2 + SQLAlchemy 2 async; injeção de dependência; Celery para
  tarefas pesadas; Alembic para migrations.
- **Frontend**: Next.js 14 (App Router) + TypeScript + Tailwind, dashboard por tenant
  (carteira, posição, movimento, relatórios, estudo, fontes, downloads).
- **Infra**: Docker Compose, Caddy como reverse proxy com TLS.

## O papel do Claude Code

- Acelerou a **reescrita por módulos** (contratos, fechamento, taxas, relatórios), entregue de forma
  incremental em uma sequência de PRs.
- Padrões repetitivos (routers, schemas Pydantic, repositórios async, migrations) gerados e
  revisados com IA, mantendo consistência arquitetural.
- Apoio na modelagem multi-tenant e nas pipelines de ETL das fontes externas.

## Resultado / impacto

- Plataforma saiu de **no-code no limite** para um **backend escalável e testável**, pronto para
  múltiplos tenants.
- Entrega **incremental por PRs**, com os módulos principais (contratos, fechamento, taxas,
  relatórios) no ar.
- Base de código **versionada, com migrations e observabilidade**, eliminando a fragilidade do
  no-code para regras de negócio complexas.

---

> Case anonimizado. Nome do produto, do tenant piloto, IPs de produção e a lógica de cálculo
> previdenciária proprietária foram omitidos. O foco é o **padrão de migração no-code → backend
> multi-tenant**.
