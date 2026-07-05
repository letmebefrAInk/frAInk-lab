---
lifecycle: active
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags: []
---

# Kalshi Orderbook

> Auto-compiled from frAInk learning.db | First compile: 2026-04-16

## Overview

Kalshi's orderbook mechanics are subtler than they look. The book depth you see isn't always the book depth you get; JIT fill checks can false-negative; API auth can lapse mid-session. The quirks that surfaced from the F-series paid for themselves in lessons even when the positions lost.

## Key Experiments

- **F003 write-API 400 errors (2026-04-02)** — first attempt to call `place_kalshi_prediction` returned 400 on both legs. Research API worked; write API didn't. Root cause later traced to signing + endpoint quirks. Lesson: wire-AND-smoke-test each external adapter, don't assume symmetric read/write.
- **F006 (Chicago B54.5, 2026-04-10)** — `check_kalshi_fill` returned `fillable=false` with depth=0 but the order rested and filled anyway. JIT fill check false-negatives are real. Don't abort on fill-check alone when the orderbook structure confirms a valid resting price. F009 applied this lesson and placed the order despite the false-negative — filled clean.
- **F008 demo-API 401 pattern (2026-04-14)** — persistent 401 on Kalshi demo/paper API across sessions forced paper logging through agent-wallet only. The deeper issue: **Kalshi has no paper market**. `project_kalshi_no_paper_invariant.md` captures the fix — paper mode now routes to prod via `KALSHI_DEMO_MODE` env flag.
- **F005 5-position hard cap (2026-04-05)** — discovered that Kalshi limits open positions to 5. Became a pre-flight check in the Planner before any new proposal. Hard blockers need pre-flight, not post-hoc discovery.

## Patterns & Rules

- **Pre-flight position count before any new entry** (→ F005 — proposing while at-cap wastes research cycles)
- **`check_kalshi_fill` is advisory, not authoritative** — trust orderbook depth + limit price alignment over the JIT tool (→ F006, F009)
- **Pre-flight fill poller catches drift** — order settled states that drift between sessions get reconciled at pipeline start (runner.py pre-flight hook, 2026-04-05)
- **Receipts are the source of truth for open positions, not memory.db** — signed HMAC JSON files in `receipts/` carry status, fill price, settlement payout. State reconciliation pulls from receipts, not narrative.
- **Settlement is NWS CLI / BLS CPI / official-source only** — never from a model forecast or a cross-exchange indicator. The resolution source is non-negotiable (→ F008 explicit — model forecasts disagreed with NWS; NWS won because NWS resolves the market)

## Open Questions

- Is there a programmatic way to detect Kalshi demo-API 401 and surface it as a pipeline warning before the trade proposal gets built? Current path is reactive.
- Does Kalshi publish book-depth history anywhere we can pull it? Useful for post-hoc fill-quality analysis.
- Is the 5-position cap a soft or hard limit? Unknown whether it scales with account tier.

## Cross-References

- See also: [Weather Trading](./weather_trading.md) — most orderbook lessons came from weather-market fills
- See also: [Guard Framework](./guard_framework.md) — Guard #6 gates how many orderbook entries can be active at once
