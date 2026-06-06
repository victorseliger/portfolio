# Personal product: distributed screen-capture + AI analysis system

> **Type:** Personal product (own authorship)
> **Technical theme:** Distributed system · real time (WebSocket) · computer vision via LLM · desktop app
> **Stack:** Python 3.11 · FastAPI · WebSocket · PyQt6 · Anthropic Claude (vision) · SQLite/SQLAlchemy · mss · imagehash

---

## The problem

Keeping track of what happens on a remote machine usually means **manual, periodic checks** + an
email report. I wanted to turn that into a **real-time, intelligent** flow: capture the screen, let
an AI **visually analyze** what changed, and deliver the insight via messaging — without anyone
watching all the time.

## The approach

A distributed architecture in three pieces:

```
capture agent (remote machine)
   │  screenshots + dedup (dHash)
   │  HTTPS (tunnel)
   ▼
FastAPI server (local)  ──  analysis via Claude (vision)  ──  SQLite
   │  WebSocket (real time)
   ▼
PyQt6 desktop app (review + distribution via messaging)
```

- **Capture agent**: `mss` for screenshots, **perceptual-hash deduplication (dHash)** to only send
  when the screen actually changes, system-tray icon (`pystray`).
- **FastAPI server**: receives captures, calls **Claude with vision** for analysis, persists in
  **SQLite (SQLAlchemy 2)** and **broadcasts over WebSocket**.
- **PyQt6 desktop app**: dark UI to review and distribute the insights.
- **Tunneling** to connect the remote machine to the local server.

## The role of Claude Code

- Structured the **async** communication (httpx/asyncio) between agent, server and UI.
- Sped up the integration of **Claude vision** and the hash-based deduplication algorithm.
- Generated the PyQt6 app scaffolding (theme, screens, event queue).

## What it demonstrates

- A **real distributed system** (3 components, tunnel, real time) — not an isolated script.
- **AI applied in production** (LLM computer vision in the loop), with efficiency care (dedup to
  avoid wasting calls).
- Command of **async Python**, **WebSocket** and **desktop apps**.

---

> Personal authorship. Personal configuration details (keys, tokens, hostnames, sessions) are never
> part of the public material. The focus is the **architecture**.
