# Miscellaneous Experiments

> Auto-compiled from frAInk learning.db | First compile: 2026-04-16

## Overview

Not everything frAInk has learned fits into weather, orderbook, guards, or CPI. This file catches the architecture evals, Alpaca paper-stock work, screener development, and cross-cutting observations that don't belong in a single domain.

## Alpaca Paper Stocks

- **GOOGL + NVDA 2026-04-10 paper longs** — first non-crypto stock entries. GOOGL bracket entry with TP + SL; SL $301 failed to place due to cash-account constraints (can't queue sell for unfilled position). Real lesson: **cash accounts can't do OCO brackets**; a limit sell + separate stop is the maximum they support. Need to track stops manually or migrate to margin.
- **NVDA TP hit 2026-04-15 +$55.24 (+5.87% in 5 days)** — clean win. TP limit filled at $199.29 vs the $199.28 target. Auto-cancelled the stop on fill. Validated the bracket pattern where it DOES work (limit legs on both sides, no OCO).
- **RNDR → RENDER rebrand** — Alpaca renamed the symbol mid-experiment. Three different format attempts (RNDR-USD, RNDR/USD, RENDER-USD) all returned 422 before we figured out the rename. Lesson: symbol changes on exchanges are silent; defensive aliasing is worth the effort.
- **Crypto stop-losses are manual** — Alpaca paper doesn't auto-enforce stops on crypto. Positions need periodic price checks. Structural constraint, not bug.

## Screener Development

- **Confluence Screener V1 — live-readiness eval 2026-04-16**: not ready for auto-trading. `buy_signal` had N=4 at 50% hit rate, 5-day horizon; sample too small. `candidate_buy` explicitly a watchlist signal and underperformed baseline by 9pp at 5d horizon in current backtest (consistent with its design — it's a "watch" tag, not an entry).
- **Universe too narrow** — backtest ran on 13 curated symbols. Broadened 2026-04-16 to ~100 stocks + ~32 crypto for accumulating sample size.
- **The RS-ignorance gap** — frAInk's stress-test of the screener (2026-04-16 paper run) independently identified that `candidate_buy` fires on stocks with negative RS (CRWD -11 RS, SOL -9.29 RS). Score alone is insufficient; RS direction is a required cross-check.
- **Screener is useful as watchlist input, not as auto-trader** — the current role is correct for its evidence level.

## Architecture Evals (TYPE 4)

- **Karpathy LLM Knowledge Base (2026-04-16)** — three architectural gaps flagged: no compile step, weak session carryforward, no structured automation layer. This wiki is the first concrete response to gap #1.
- **@defileo Claude Code + Obsidian (2026-04-16)** — four patterns adopted: CLAUDE.md-as-contract (already partial), /init bootstrap (deferred), `.claude/skills/` directory (scaffolded 2026-04-16), repo-root `memory.md` carryforward (shipped 2026-04-16).
- **Garry Tan "Thin Harness, Fat Skills" (2026-04-16)** — the single highest-leverage gap: no resolver routing task types to context docs. Draft RESOLVER.md created; wiring into Planner deferred pending reference-file audit.
- **Two-source convergence** — Karpathy + Tan flagged the SAME three gaps on the same day. That kind of convergence is worth weighting; single-source architecture advice gets ignored.

## Cross-Cutting Observations

- **"Swinging big has lost. Compounding small has worked."** (Frank's framing, 2026-04-10) — the anchoring principle for all subsequent position sizing. Applies across weather, CPI, and equities.
- **Pre-flight state reconciliation catches drift** — built 2026-04-10 in response to silent state divergence (Alpaca order closed externally, frAInk didn't notice until next pipeline run). Now fires at pipeline start, every run.
- **Narrative drift is real in long threads** — F007 was misclassified mid-session because the research run conflated two open threads. Cross-check by experiment_id against receipts, not by narrative match.

## Cross-References

- See also: [Weather Trading](./weather_trading.md) — most of the sizing rules originated there
- See also: [Guard Framework](./guard_framework.md) — the cross-venue guard matrix covers all the above
- See also: [CPI & Macro Markets](./cpi_markets.md) — the macro arm of the trading practice
