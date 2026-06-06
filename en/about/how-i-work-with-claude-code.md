# How I work with Claude Code

My way of building software with AI is not "ask for code and paste it". It's an **engineering
process** in which Claude Code is a multiplier — and the specification is what makes the difference.

## The philosophy: the spec is half the work

Most of the business pains I solve aren't "how to code" problems, but **"what exactly needs to be
done"** problems. That's why I invest heavily in turning scattered knowledge (giant spreadsheets,
meetings, tacit rules) into an **executable, versionable specification** before writing the first
line of implementation.

## The flow

```
1. Research & reuse   → is there something proven/battle-tested? (libs, patterns, projects)
2. Specification      → manual pain → explicit rules → data model → edge cases
3. Implementation     → layered, deterministic, testable (TDD when it makes sense)
4. Review             → code review (quality + security) before trusting the result
5. Verification       → backtest / tie-out against the source of truth
```

### 1. Research & reuse first
Before writing new code, I look for proven implementations and patterns. Rewriting what already
exists is waste.

### 2. Specification before code
I map every implicit rule (from a spreadsheet, from a call) to an **explicit** one. In several
projects, that meant taking logic out of cell formulas and putting it into **declarative SQL** —
traceable, reviewable, auditable.

### 3. Layered implementation
The data pipelines I build follow isolated layers (`raw → staging → fact → aggregate → matching →
output`). Each step is materializable and inspectable on its own — the opposite of the "magic
spreadsheet" nobody understands.

### 4. Quality and security review
AI-generated code **goes through review** like any code. Security in particular: secrets never enter
the repository, production data stays out, and inputs are validated at the boundaries.

### 5. Verification against reality
I don't trust the output just because "it ran". I **backtest** against a real period and **tie out
against the source of truth** (ledger, trial balance, golden set) before considering it done.

## Where I use AI in production

- **Data reconciliation**: translating manual processes into deterministic SQL/DuckDB pipelines.
- **Automation**: orchestration with n8n + Python backends for flows that scale.
- **Applied AI**: LLM computer vision, multi-agent systems (PydanticAI), and Claude Code itself as a
  central development tool.

## Principles I carry

- **Immutability and determinism** > hidden side effects.
- **Many small, cohesive files** > a few giant ones.
- **Errors handled explicitly** > silent failures.
- **Secrets out of the code, always** — no exceptions.

---

> This portfolio is itself an example of the process: each case was written from a real
> understanding of the projects, **anonymized by design**, without leaking anything confidential.
