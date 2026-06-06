# Produto pessoal: sistema distribuído de captura de tela + análise por IA

> **Tipo:** Produto pessoal (autoria própria)
> **Tema técnico:** Sistema distribuído · tempo real (WebSocket) · visão computacional via LLM · app desktop
> **Stack:** Python 3.11 · FastAPI · WebSocket · PyQt6 · Anthropic Claude (vision) · SQLite/SQLAlchemy · mss · imagehash

---

## O problema

Acompanhar o que acontece em uma máquina remota normalmente significa **checagem manual e
periódica** + relatório por e-mail. Eu quis transformar isso em um fluxo **em tempo real e
inteligente**: capturar a tela, deixar uma IA **analisar visualmente** o que mudou e entregar o
insight por mensageria — sem ninguém olhando o tempo todo.

## A abordagem

Arquitetura distribuída em três peças:

```
agente de captura (máquina remota)
   │  screenshots + deduplicação (dHash)
   │  HTTPS (túnel)
   ▼
servidor FastAPI (local)  ──  análise por Claude (vision)  ──  SQLite
   │  WebSocket (tempo real)
   ▼
app desktop PyQt6 (revisão + distribuição via mensageria)
```

- **Agente de captura**: `mss` para screenshot, **deduplicação por perceptual hash (dHash)** para
  só enviar quando a tela realmente muda, ícone na bandeja do sistema (`pystray`).
- **Servidor FastAPI**: recebe as capturas, chama o **Claude com visão** para análise, persiste em
  **SQLite (SQLAlchemy 2)** e faz **broadcast por WebSocket**.
- **App desktop PyQt6**: UI dark para revisar e distribuir os insights.
- **Tunelamento** para conectar a máquina remota ao servidor local.

## O papel do Claude Code

- Estruturou a comunicação **assíncrona** (httpx/asyncio) entre agente, servidor e UI.
- Acelerou a integração de **visão do Claude** e o algoritmo de deduplicação por hash.
- Gerou o scaffolding do app PyQt6 (tema, telas, fila de eventos).

## O que demonstra

- **Sistema distribuído real** (3 componentes, túnel, tempo real) — não um script isolado.
- **IA aplicada em produção** (visão computacional via LLM no loop), com cuidado de eficiência
  (dedup para não desperdiçar chamadas).
- Domínio de **async Python**, **WebSocket** e **app desktop**.

---

> Projeto de autoria própria. Detalhes de configuração pessoal (chaves, tokens, hostnames, sessões)
> nunca fazem parte do material público. O foco é a **arquitetura**.
