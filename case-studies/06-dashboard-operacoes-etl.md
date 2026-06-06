# Dashboard operacional em tempo real: gestão de trabalho → Postgres → webhook → HTML

> **Setor:** **Dashboard operacional interno** de uma empresa de dados
> **Tema técnico:** ETL em SQL idempotente · renderização via **webhook** · desacoplamento de storage
> **Stack:** n8n · Supabase (PostgreSQL + PostgREST) · API de gestão de trabalho · JavaScript (Node) · HTML/CSS/JS vanilla

---

## A dor manual / técnica

O time de operações acompanhava capacidade, worklogs e trabalho-em-progresso **manualmente**,
puxando dados da ferramenta de gestão de trabalho. A primeira versão automatizada guardava tudo nas
**tabelas internas do n8n** — que **bateram no limite de armazenamento** e não davam vazão para um
dashboard em tempo real.

## A especificação / abordagem

A virada foi **desacoplar o storage** da ferramenta de automação e tornar a transformação
**determinística**:

```
gestão de trabalho (API)
        │  n8n (sync + polling + transform)
        ▼
Supabase / PostgreSQL (raw)  ──  ETL via funções SQL idempotentes (rebuild_*)
        │
        ▼  n8n webhook (renderiza HTML)
dashboard (HTML/CSS/JS vanilla)
```

- **Storage dedicado**: troca das DataTables do n8n por **Postgres (Supabase)**, removendo o teto
  de armazenamento.
- **ETL idempotente**: funções SQL `rebuild_*` que reconstroem as visões agregadas de forma
  determinística (reexecutáveis sem efeito colateral).
- **Soft-delete com trilha de auditoria**, preservando histórico.
- **Renderização por webhook**: o n8n só renderiza o HTML final, minimizando overhead de execução.
- **Frontend zero-framework** (vanilla JS), reduzindo dependências e superfície de manutenção.

## Arquitetura & stack

- **n8n** para sync/polling da API de gestão de trabalho e como camada de renderização (webhook).
- **Supabase / PostgreSQL** como fonte de verdade analítica; **PostgREST** para acesso.
- **SQL** para todo o ETL (funções idempotentes `rebuild_*`).
- **HTML/CSS/JS vanilla** no frontend.

## O papel do Claude Code

- Desenhou as **funções SQL idempotentes** de ETL e o modelo de tabelas no Supabase.
- Acelerou a migração de DataTables → Postgres e a lógica de soft-delete/auditoria.
- Ajudou no padrão de **renderização via webhook** e no frontend sem framework.

## Resultado / impacto

- Dashboard saiu de **storage no limite** para um modelo **escalável e em tempo real**.
- ETL **determinístico e reexecutável** (sem estados sujos), com **trilha de auditoria** por
  soft-delete.
- Frontend **sem framework** = menos dependências, deploy e manutenção mais simples.

---

> Case anonimizado. Nome da empresa, da ferramenta de gestão, dados de equipe/projetos e quaisquer
> credenciais foram omitidos. O foco é o **padrão ETL idempotente + storage desacoplado + webhook**.
