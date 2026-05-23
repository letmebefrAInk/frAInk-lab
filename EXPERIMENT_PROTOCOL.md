---
lifecycle: mature
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags:
- spec
- experiment-protocol
- lab
---

# frAInk — EXPERIMENT_PROTOCOL.md
### How experiments are designed, executed, and evaluated.
#### Version v1.2 · Current as of 2026-04-17

---

## Purpose

frAInk is an experimental AI system designed to explore how autonomous agents reason, act, and learn in real-world environments. Experiments are the core mechanism through which frAInk evolves.

Every experiment must be **bounded, logged, and interpretable**.

The goal is not to prove the AI right. The goal is to **learn something true.**

---

## F-Series naming convention

Every live or paper trading experiment gets a sequential F-series label. The label is set at proposal time and threaded through Policy approval → Executor tool calls → SAMcypher receipt → memory row → lab publication.

**Active as of 2026-04-17:** F001 through F010+.

Examples:
- **F003** — Phoenix NO 10-contract weather prediction (+$2.74 WIN). First small-size weather play.
- **F004** — Chicago NO 284-contract (−$28.19 LOSS). The "swung too big" lesson that anchored the small-size compounding thesis.
- **F007** — April CPI portfolio, 22 contracts across two brackets (OPEN, resolves May 12).
- **F008** — Miami NO B81.5 weather, supersede after initial F007-Miami decision (+$9.31 WIN).
- **F009** — Miami NO B81.5 weather 24-contract (LOSS — busted cushion at 81°F actual).

**Label discipline is non-negotiable.** Unlabeled trades auto-inherit a ticker-derived label, which breaks F-series grouping in the ledger. Every `place_*` tool call must pass `experiment_label="F0XX"` explicitly when the trade is part of a named experiment.

Non-trading experiments (research protocols, article evaluations, tool builds) use their own naming — no F-series label required, but same structural discipline.

---

## Experiment Structure

Every experiment follows the same seven-stage arc.

### 1. Hypothesis

What are we trying to learn? One clear question.

Examples:
- "Does early-morning weather entry beat afternoon entry on Kalshi cushion plays?" (F008 working theory)
- "Does the broader 100-stock universe produce real screener edge that the 13-symbol universe missed?" (Scanner readiness v2)
- "Does swinging big on Kalshi weather predictions produce more edge than compounding small?" (F003–F007 batch; answer: no)

Every experiment starts with a clear question. No fishing expeditions.

### 2. Inputs

What information is the agent allowed to see?

Typical inputs:
- Market data (Alpaca, Kalshi, yfinance)
- Research context (Tavily web_search, fetch_article)
- Historical patterns via `search_trade_history` FTS5
- Screener output (when relevant)
- Memory carry-forward (prior experiment outcomes, behavioral rules in learning.db)

**No hidden information.** Every input is declared up front.

### 3. Guardrails

Every experiment runs inside defined limits. Authoritative caps in `AGENT_SPEC.md` + `PRE_AUTH_TRADING.md`. Summary:

| Guardrail | Value |
|-----------|-------|
| Per-action budget cap (LIVE) | $10 |
| Per-cycle total budget | $100 |
| Max open Kalshi positions | 5 (LIVE), 8 (PAPER/MOCK) |
| Max Kalshi exposure | $25 |
| Consecutive-loss guard (#6) | 3 = BLOCK |
| Price-sanity guard (#7) | slippage/depth thresholds |
| Mode required for real money | `LIVE_MODE=true` + `LIVE_MODE_AUTHORIZED=true` |

Experiments that require raising a cap go through Frank — not through Policy reinterpretation.

### 4. Decision Phase

Two decisions are recorded independently **before outcomes are known**:

**frAInk's decision** — captured in Policy approval row (memory.db `decisions` table) with full reasoning.

**Frank's decision** (when applicable) — captured in `docs/public/frankvfraink_scoreboard.md`. This is the running comparison board. Not every experiment has a Frank-side decision; most do when it's a trading experiment.

Recording both creates the natural control comparison. No retrofitting after the outcome lands.

### 5. Execution

If the experiment requires action, Executor runs it under the Policy-approved constraints. Execution emits:
- An HMAC-signed **SAMcypher receipt** → `receipts/F###_<slug>.json` (tracked in git, auto-published to frAInk-lab)
- A memory row in `memory/memory.db` → `experiments` table
- A structured log entry in `logs/experiments.md`
- A learning.db `tool_log` row per underlying tool dispatch

Nothing gets executed without all four artifacts landing. If receipt emission fails, the action is rolled back where reversible.

### 6. Outcome

What actually happened? Recorded without modification.

- Prediction accuracy / resolution
- Profit / loss (with timestamps)
- Research quality / time-to-conclusion
- Any anomaly or guardrail trip

Outcome lands in the same memory row + a settlement receipt updates the F-series ledger.

### 7. Lessons

Every experiment ends with a short structured reflection. Usually captured in `logs/experiments.md` or auto-generated via `write_daily_journal`. Questions:

- What did frAInk do well?
- Where did it fail?
- What surprised us?
- What behavioral rule, tool, or metric should change next time?

Lessons that recur 3+ times become **proposals** — written to `learning.db` `proposals` table, reviewed monthly, promoted to active behavioral rules if validated.

---

## Capture mechanisms

Experiment data lives in five places, each with a specific purpose:

| Artifact | Location | Purpose | Lifetime |
|----------|----------|---------|----------|
| **Receipts** | `receipts/F###_<slug>.json` | HMAC-signed audit trail of the action | Permanent (git) |
| **Memory row** | `memory/memory.db` `experiments` table | Structured queryable experiment record | Permanent (nightly backed up) |
| **Behavioral rules** | `memory/learning.db` `behavioral_rules` + `rule_fires` | Rules extracted from experiment patterns | Living — updated by proposer |
| **Experiment log** | `logs/experiments.md` | Human-readable narrative | Permanent |
| **Public experiment record** | `frAInk-lab/experiments/F###_auto.md` | Auto-published via `tools/lab_publisher.py` post-pipeline | Permanent (public) |

Daily journals (`write_daily_journal`) auto-fire at EOD for PAPER and RESEARCH runs. They're not experiments themselves — they're the connective tissue between experiments.

---

## Scoreboard

The running Frank-vs-frAInk comparison lives at `docs/public/frankvfraink_scoreboard.md`. It's one tracked question over time, not a global leaderboard. Currently focused on "who picks better short-term trades."

For the F-series financial experiments specifically, the scoreboard is compiled from `receipts/*.json` + settlement data into `frAInk-lab/SCOREBOARD.md` (public) — updated automatically via lab_publisher on every pipeline run.

---

## Budget & sizing discipline

- **Default per-action cap: $10 in LIVE.** Proposals to exceed require Frank approval.
- **F-series thesis (locked 2026-04-10):** compound small, don't swing big. F003 (+$2.74) beat F004 (-$28.19) on thesis quality; what killed F004 was sizing, not research.
- **Small positions with thin edge compound.** 50%+ win rate on $10 stakes is a real path. One-trade-home-runs aren't.

---

## When an experiment should NOT run

- Active BLOCK guard (e.g., Guard #6 at 3+ consecutive losses)
- Cushion < threshold on weather markets (F009 lesson: 3°F cushion + 78°F forecast still busted to 81°F)
- Zero resting orderbook depth (F-series structural lesson)
- Pre-auth class not covered in `PRE_AUTH_TRADING.md`
- Mode mismatch (LIVE without `LIVE_MODE_AUTHORIZED=true` → forced to PAPER)

Stand-downs are experiments too. Log the rejected thesis + what would have to change to approve.

---

## Guiding Principles

- **Bounded, logged, interpretable.** Three words, no exceptions.
- **Learn something true.** Winning is a proxy; truth is the target.
- **Record decisions before outcomes.** No retroactive narrative.
- **Small and compounding beats big and heroic.** F-series thesis, earned the hard way.
- **Stand-downs count.** Skipping a bad bet is a real result.

---

## Version History

| Version | Date | Notes |
|---------|------|-------|
| v1.0 | 2026-03-17 | Initial 7-stage structure: hypothesis, inputs, guardrails, decision, execution, outcome, lessons. Dangling Scoreboard section. |
| v1.1 | (undated) | Minor edits to stage descriptions. |
| v1.2 | 2026-04-17 | F-series naming convention codified. Capture mechanisms table added (receipts + memory + learning + logs + lab publication). Budget & sizing discipline section from F003–F007 lessons. "When NOT to run" section. Completed Scoreboard section with link to frankvfraink + frAInk-lab SCOREBOARD. |

---

*Part of the frAInk 3-Agent Model. See `AGENT_SPEC.md` for system-wide rules.*
