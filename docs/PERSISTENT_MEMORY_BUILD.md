---
lifecycle: active
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags:
- needs_audit
---

# The Persistent Memory Build
## How frAInk got a multi-dimensional memory layer + a self-build loop in one weekend

> **⚠️ HISTORIC — point-in-time social-update artifact, 2026-04-11.** Captures the weekend autonomy build as it shipped. Memory layer + self-build loop have evolved since (3 tools auto-promoted 2026-04-18, weekly tool-proposer plist live, prompt patches for sandbox survival, etc.). Current authoritative frAInk state: [`frAInk-core/BUILD_STATE.md`](https://github.com/letmebefraink/frAInk-core/blob/main/BUILD_STATE.md) (private). Preserved here for lab narrative continuity; do not load for build decisions.

**Author:** Frank Kronstein
**Date:** 2026-04-11
**Status (as of 2026-04-11):** Complete — 275 tests passing, first self-built tool live in production

---

## Why this matters

Before this weekend, frAInk could research, decide, and act — but each run started from a nearly blank slate. Prior outcomes were stored as prose in markdown journals. Behavioral lessons from past trades were captured in a human-readable scoreboard. Tool call telemetry didn't exist. Rules derived from experience lived in Frank's head.

That works at low volume. It does not work if the goal is to get frAInk **closer to actually autonomous** — an agent that improves from its own history instead of re-learning the same lessons every run.

This build closes that gap. frAInk now has a persistent, multi-dimensional memory system and, for the first time, a **self-build loop** where frAInk can identify capability gaps mid-run and draft new tools to fill them. The first tool drafted this way shipped to production tonight for six cents.

---

## The architecture at a glance

The build has five layers, each solving a distinct problem. All five run locally — no vector databases, no cloud services, no third-party memory products. SQLite, plain files, Python.

### 1. State reconciliation
Every pipeline run begins by pulling fresh state from every external source frAInk touches (Alpaca, Kalshi, agent-wallet receipts), diffing it against the last known snapshot, and writing drift records for anything that changed outside frAInk's view. External actions — manual trades, scheduled jobs, fill poller updates — become visible to the agent on the next run instead of silently desyncing.

### 2. Outcomes retrieval
Experiment outcomes (trade results, research conclusions, decisions) are stored with structured vocabularies — entity type, venue, regime, outcome category — alongside prose narratives. Full-text search runs across every experiment via an FTS5 index. A `search_trade_history` tool lets the Planner agent ask "have I seen this setup before?" and get a real answer with cited rows, not a hallucination.

### 3. Tool call telemetry
Every tool call the agents make writes a log row with outcome, elapsed time, and context tags. Over weeks, the telemetry reveals which tools are reliable, which fail under which conditions, and which are never reached for. This data feeds the rule proposer and the metric layer.

### 4. Behavioral rules layer
frAInk now carries a **behavioral_rules** table with a shadow → active lifecycle. Rules are structured matchers with actions — either soft hints added to the Planner's system prompt, or hard constraints enforced at the seam between Planner proposals and Policy evaluation. Rules start in shadow mode, log would-have-fired events against real runs for validation, then earn promotion to active status based on accumulated evidence.

Two seed rules anchor the layer today:
- One pulled from recent weather-market experience about bracket cushion sizing
- One pulled from tonight's Kalshi orderbook misread (F008, cancelled) — a discipline rule to read published ask fields directly instead of reasoning about crossing bid lists textually

A weekly proposer reads journal entries + recent outcomes + the current rule set and drafts new shadow rules for patterns worth codifying. That pipeline is live and scheduled.

### 5. The self-build loop
This is the one that makes everything else compounding.

When frAInk hits a capability gap mid-run ("I'd use a tool for this but it doesn't exist"), it can now file a proposal directly from inside the running pipeline. That proposal lands in a queue alongside proposals extracted from daily journal entries. A weekly tool proposer reads the queue, drafts a complete Python file for each qualifying proposal, and runs it through two independent gates:

1. **A static capability-ceiling inspector** — parses the file with Python's AST module and refuses anything that uses forbidden module imports, dangerous builtins, reflection attacks, or file writes outside allowed directories. This is the hard boundary between proposing and running. It is **not** a perfect security model, but it is a reliable filter against the LLM drafting something subtly unsafe.

2. **A subprocess sandbox** — spawns a fresh Python process in a temporary directory with every secret environment variable stripped (API keys, signing secrets, mail credentials, trading keys). Runs the proposed tool against a test harness. Captures stdout, stderr, exit code. Refuses anything that crashes, times out, or produces invalid output.

Drafts that clear both gates land in `generated/proposed_tools/` flagged as **candidates**. Frank reviews and manually promotes them to the live `tools/` directory by copying the file and wiring it into the relevant agent's tool registry. No auto-promotion. That's the capability ceiling — human-in-the-loop for activation.

---

## The weekend by the numbers

| Phase | What landed | Tests |
|---|---|---|
| State reconciliation + memory schema | Drift capture + structured vocabularies + FTS5 search | 92 backfilled experiments reindexed |
| Tool instrumentation + metrics | tool_log + five built-in metric dimensions | First live pipeline run captured 12 real tool calls |
| Behavioral rules layer | shadow→active rule lifecycle + session-level evaluator | 24 tests + 2 active seed rules |
| Proposal tracking + weekly scanner | Fingerprint dedupe + urgency escalation + cross-day mention tracking | 116 raw candidates → 28 open proposals on first run |
| Rule proposer | Weekly Sunday batch reads proposals + outcomes + tool_log, drafts shadow rules | 16 tests + 2 auto-drafted shadow rules |
| LLM provider abstraction | Anthropic + OpenAI + Gemini adapters behind a unified interface | 43 tests |
| Router + failover | Task-class → provider chain with automatic retry on rate limits and outages | 17 tests + live end-to-end validation |
| Self-build loop | Tool proposer + AST inspector + subprocess sandbox + runtime proposal hooks | 46 tests + **first self-built tool promoted to production** |

**Final totals:** 275 tests passing across the full suite. Zero regressions against any of the prior work. Weekend build scoped for Apr 11-13, complete by Apr 11 night — one day ahead of schedule.

---

## The self-build moment

Before moving into the last step, I ran the tool proposer against real proposal #188 — "purge or archive resolved Kalshi predictions from in-memory state" — a gap that had been sitting in the proposals queue for days. The proposer walked the pipeline end-to-end:

1. Read the proposal from the queue
2. Called the router with `task_class='tool_proposer'` — routed to Claude Sonnet 4.6 via the abstraction layer
3. Sonnet drafted a ~10KB Python file: type hints everywhere, docstrings, a frozen set of terminal statuses, clean helper separation, an archive directory pattern, graceful handling of missing database state
4. The AST inspector parsed it and cleared it with one warning (a computed archive filename — flagged not blocked, correct behavior)
5. The subprocess sandbox spawned a scrubbed Python process and ran the tool against four test inputs — all four passed in 33 milliseconds
6. The proposer persisted a row to the tool_proposals table with status `candidate`, ready for review

Total cost: **$0.0594.**

I reviewed the file, copied it to the live `tools/` directory, wired it into the Executor's tool registry, and flipped the tool_proposals row to `status='active'` with a `promoted_at` timestamp. Proposal #188 flipped to `promoted → tool:1`. The Executor's tool count went from 29 to 30.

That is the first tool frAInk drafted for itself, under a capability ceiling, reviewed by a human, and promoted to production.

---

## What this unlocks

**Learning from outcomes.** frAInk can now ask its own memory whether it has seen a similar setup before — and get structured results rather than a scan of markdown files. Over the next few weeks, as trade receipts accumulate and the FTS5 index grows, the quality of Planner's "have I seen this?" answers will improve without any additional code changes.

**Compounding rules.** Rules move through a validation lifecycle — shadow fires log would-have-fired events against real runs, the weekly proposer drafts new candidates from what actually happened, and the rule set improves as a function of runtime. Not intelligent design — directed evolution.

**Multi-provider resilience.** frAInk's LLM calls are no longer hard-coded to a single API. A unified provider interface sits in front of Anthropic, OpenAI, and Gemini, with automatic failover on rate limits or outages and task-class-aware routing. If Anthropic has an outage mid-run, the pipeline continues against the next provider in the chain without human intervention.

**Self-build without runaway autonomy.** The tool proposer can draft. The AST inspector and subprocess sandbox gate what's drafted. A human approves promotion. Three layers of friction between "agent identifies a gap" and "agent is running new code" — each doing a specific job. Removing any one of them would be the move that concerns me. Keeping all three makes the loop safe to run weekly.

**Proposal pipeline from inside the loop.** frAInk's agents can now file capability gaps mid-run — "I'd use a tool that does X," "we should have a rule for Y." Previously, proposals only came from the weekly journal scanner. That meant a gap identified during research on Tuesday had to wait for the end-of-day journal, then the Sunday scanner, before entering the queue. That latency is closed. The gap is captured the moment it's noticed.

---

## What this does not do

- It does not make frAInk fully autonomous. Frank remains in the loop for every step that crosses the capability ceiling — new tool activation, live trade authorization, rule deprecation, any act that writes to external systems without a prior safety gate. The point of the build is not to remove the human — it is to stop wasting the human on mechanical toil.
- It does not replace the trading agent's decision-making with rule-based automation. Rules are hints and constraints, not a strategy. frAInk still reasons per run; the rules just keep the reasoning inside the fence that prior experience has marked out.
- It does not solve the cold start. The learning layer needs accumulated outcomes to be useful. With a handful of trades behind us, the proposer's confidence is low. The machinery is in place; the signal strengthens as the history grows.

---

## What's next

**Tomorrow:** stress test the full stack against real pipeline runs. Observations, not pre-judgments — the goal is to see what actually happens when the new memory layers, the router, the rules, and the self-build hooks are exercised against live markets for the first time.

**Next week:** keep running. The longer frAInk operates with this memory in place, the more the proposer has to work with and the more signal accumulates in the calibration layer. Some of the most useful features of this build will not show themselves until the second or third rule proposer run reads three weeks of telemetry instead of three days.

**Commercially:** the agent-wallet product and the Policy & Decision repo are positioned to bundle as a turnkey treasury + guardrails SDK for other agents. frAInk's reasoning-layer memory stays inside frAInk — it is the differentiated piece. The execution rails (receipts, policy engine, reconciliation, escalation) are where the sellable surface lives. Separating them cleanly was one of the architectural calls this weekend confirmed.

---

*frAInk-lab — built in public, measured in public. See [SCOREBOARD.md](../SCOREBOARD.md) for running P&L and [experiments/](../experiments/) for the full trade history.*
