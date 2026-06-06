# Case studies

Estudos de caso **anonimizados** de projetos reais. Por confidencialidade (NDA), nenhum nome de
cliente/empresa, código proprietário ou dado produtivo é exposto — cada case usa apenas **dica de
setor** e foca no **padrão de engenharia transferível**.

Cada estudo segue a mesma estrutura: **dor manual → especificação/abordagem → arquitetura & stack →
papel do Claude Code → resultado/impacto**.

## Engenharia de conciliação & dados

| # | Case | Setor (anônimo) | Tema técnico |
|---|------|-----------------|--------------|
| 01 | [Conciliação de derivativos](01-conciliacao-derivativos.md) | Energia / refino | Pipeline SQL em camadas + motor de regras |
| 02 | [Conciliação de logística reversa](02-conciliacao-logistica-reversa.md) | Baterias (indústria) | Matching com tolerâncias em cascata + arrasto histórico |
| 03 | [Forense de conciliação de pagamentos](03-forense-conciliacao-pagamentos.md) | Cosméticos / varejo | Matching forense + diagnóstico de causa raiz |
| 04 | [Conciliação multi-adquirente](04-conciliacao-multi-adquirente.md) | Mobilidade / transporte | Parsing de arquivo bancário + matching multi-fonte |
| 07 | [Extração de arquivos bancários](07-extracao-arquivos-bancarios.md) | Fintech de conciliação | Parsing JSON + regex + agregação |

## Plataformas & automação

| # | Case | Setor (anônimo) | Tema técnico |
|---|------|-----------------|--------------|
| 05 | [Plataforma multi-tenant de previdência](05-plataforma-multitenant-previdencia.md) | SaaS de previdência (RPPS) | Migração no-code → FastAPI/Postgres + multi-tenancy |
| 06 | [Dashboard operacional ETL](06-dashboard-operacoes-etl.md) | Empresa de dados (interno) | ETL SQL idempotente + webhook + storage desacoplado |
| 08 | [Da reunião à especificação](08-spec-a-partir-de-reunioes.md) | Conciliação de estoques | Transcrição em chunks → spec estruturada |

## Produtos pessoais

| # | Case | Tipo | Tema técnico |
|---|------|------|--------------|
| 09 | [Monitor de tela + IA](09-monitor-tela-ia.md) | Produto próprio | Sistema distribuído + WebSocket + visão por LLM |
| — | [Maestro (arquitetura de referência)](../projects/maestro/) | Produto próprio | Assistente multi-agente (PydanticAI) |
