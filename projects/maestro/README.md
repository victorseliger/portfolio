# Maestro — arquitetura de referência de um assistente pessoal multi-agente

> **Tipo:** Produto pessoal (autoria própria)
> **Status do código:** **privado por design** — veja a nota abaixo.
> **Stack:** Python 3.12 · FastAPI · PydanticAI · Pydantic v2 · SQLAlchemy 2 (async) · PostgreSQL 16 + pgvector · Redis 7 · Next.js 14 · TypeScript · Docker · Caddy

---

## Por que esta página não tem código

O Maestro é um assistente pessoal que processa **dados pessoais reais de terceiros** (mensagens,
finanças, agenda, contatos). Open-sourcear o código vazaria informação de pessoas que **não
consentiram** em ser expostas — então o repositório real é **privado**.

Isso é uma decisão de engenharia deliberada: **maturidade em portfólio também é saber o que NÃO
publicar.** O que segue é a **arquitetura** — o que tem valor demonstrável — descrita de forma
genérica, sem qualquer dado pessoal.

---

## O que é

Um assistente conversacional cujo canal primário é mensageria, projetado para reduzir **carga
cognitiva**: respostas curtas, no máximo poucos itens por vez, e decisões delegadas em vez de
listas infinitas. O foco de design é **acessibilidade cognitiva** — uma UX que não pune o usuário e
que termina sempre em uma ação clara.

## Arquitetura multi-agente

O sistema é um **conselho de agentes especializados** orquestrados por um meta-agente, construído
sobre **PydanticAI** (saídas tipadas com Pydantic, tool-calling estruturado):

```
                ┌─────────────── meta-agente (orquestrador) ───────────────┐
   mensagem  →  │  roteia intenção → escolhe especialista → compõe resposta │  →  resposta curta
                └──────────────────────────┬───────────────────────────────┘
                                            │
        ┌───────────────┬──────────────────┼──────────────────┬───────────────┐
   especialista A   especialista B    especialista C     sub-agentes        camada de
   (domínio 1)      (domínio 2)       (domínio 3)        invisíveis         memória
                                                        (extração/foco)
```

- **Personas especializadas**: cada agente tem um escopo de domínio próprio e um prompt dedicado.
- **Sub-agentes invisíveis**: tarefas de bastidor (extração de fatos, priorização) que não falam
  com o usuário diretamente.
- **Saídas tipadas**: PydanticAI força o modelo a retornar estruturas validadas, não texto solto —
  o que torna o comportamento testável.

## Memória em 4 camadas

```
L1  Redis            → cache quente / contexto de sessão
L2  PostgreSQL       → histórico estruturado e transacional
L3  pgvector         → memória semântica (busca por similaridade)
L4  Perfil (JSON)    → preferências e contexto estável do usuário
```

A combinação dá ao assistente **memória de curto e longo prazo** sem inflar o contexto do LLM em
toda chamada: recupera-se semanticamente só o que é relevante (RAG sobre a L3).

## Roteamento de modelos (custo-consciência)

Diferentes tarefas usam diferentes modelos, equilibrando qualidade e custo:

- **Modelo rápido/barato** para extração e classificação de alto volume.
- **Modelo intermediário** como padrão conversacional.
- **Modelo de raciocínio profundo** apenas para decisões críticas.

## Integrações (padrões genéricos)

O backend integra serviços externos por padrões reutilizáveis (webhooks idempotentes, OAuth,
filas):

- **E-mail** (watch em tempo real + parsing) e **calendário** (OAuth multi-conta).
- **Mensageria** como canal primário de UX.
- **Pagamentos** e **assinatura de documentos** via APIs de terceiros.
- Pipeline de captura → normalização → categorização para dados financeiros do próprio usuário.

> Detalhes de qualquer integração de negócio específica são **propositalmente omitidos**.

## Infraestrutura

```
Next.js 14 (dashboard de configuração/revisão)
        │  HTTPS (Caddy)
        ▼
FastAPI (async)  ──  SQLAlchemy 2 async  ──  PostgreSQL 16 + pgvector
        │
        ├── Redis (cache / filas)
        └── armazenamento de documentos (criptografado)
```

- **Docker Compose** para orquestração local e deploy.
- **Caddy** como reverse proxy com TLS automático.
- Documentos sensíveis **criptografados** em repouso.

## O papel do Claude Code

- Scaffolding do framework multi-agente (personas, tools, contratos Pydantic).
- Desenho da camada de memória de 4 níveis e do RAG semântico sobre pgvector.
- Padrões de API FastAPI assíncrona, OAuth e idempotência de webhook.
- Roteamento de modelos por tipo de tarefa.

## O que demonstra

- **Arquitetura de agentes de verdade** (orquestração + personas + saídas tipadas), não um wrapper
  de chat.
- **Memória híbrida** (cache + relacional + vetorial + perfil) com recuperação semântica.
- **Engenharia de produção**: async, OAuth multi-conta, webhooks idempotentes, criptografia,
  Docker/Caddy.
- **Custo-consciência** com roteamento de modelos.
- **Ética de dados**: capacidade de reconhecer dado pessoal de terceiros e manter o código privado.

---

> Página de arquitetura escrita do zero e anonimizada. Nenhum prompt, nome, dado de negócio,
> financeiro ou de saúde do produto real está aqui — e nunca estará.
