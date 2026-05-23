---
lifecycle: active
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags:
- needs_audit
---

# Guard Framework

> Auto-compiled from frAInk learning.db | First compile: 2026-04-16

## Overview

frAInk's guard system is the layer between "this is a reasonable idea" and "execute it with real money." Seven guards, each a programmatic pre-check against a known failure mode. Most of them were built in response to specific losses — the framework grew from incidents, not from up-front design. Health checks are logged regularly so that state transitions (FLAG → CLEAR, or the reverse) are traceable.

## The Seven Guards

1. **G1 — Position cap per venue.** Max open positions per venue (Alpaca 8, Kalshi 5). Prevents uncapped exposure stacking.
2. **G2 — Universe cooldown.** 24hr between sp500/russell2000/crypto_top50 scans to prevent pattern re-detection on same candidates. Watchlist has no cooldown.
3. **G3 — Pre-auth conditions.** Every trade must declare which pre-auth conditions it claims to meet. Missing conditions → hard block.
4. **G4 — Balance reconciliation.** Live balance must reconcile with memory.db's last-known snapshot within an expected delta. Drift triggers a FLAG.
5. **G5 — Score floor.** Screener-sourced trades must clear minimum `total_score` thresholds. Prevents noise entries.
6. **G6 — Consecutive loss guard.** After 2 consecutive losses: FLAG (require higher edge + confidence). After 3: BLOCK (no new entries without Frank override). The most-tested guard in the framework.
7. **G7 — Price sanity.** Limit price must align with current bid/ask structure. Prevents fat-finger errors.

## Key Experiments

- **G6 origination (F004/F005 losses, 2026-04-05)** — frAInk lost F004 -$28 and F005 -$13 in succession. Without a consecutive-loss guard, a third swing-sized loss could have blown the remaining budget. Guard #6 was built the same week as mandatory infrastructure.
- **G6 first-FLAG (F004 aftermath, 2026-04-10)** — consecutive_losses counter hit 2, FLAG state entered. Gated all new Kalshi predictions pending a win or a Frank override. Screener signals kept firing (widest candidate field ever recorded — 9 simultaneous candidate_buys) but the guard held.
- **G6 first-CLEAR (F006 WIN, 2026-04-11)** — F006 settled WIN, counter reset 2→0, FLAG → NONE. Proved the reset path works. Auto-reset triggered by fill poller picking up SETTLED_WIN status.
- **G4 Kalshi-scoped FLAG (2026-04-10)** — balance didn't reconcile with open-order state due to CPI orders not appearing in position API. FLAG scoped to Kalshi only, not Alpaca. Taught that guard state can be venue-scoped.

## Patterns & Rules

- **Guards are built from losses, not from imagination** — every guard in the framework traces to a specific incident. New guard proposals should cite the specific failure mode they prevent (→ guard_framework memory; G6 traces directly to F004+F005)
- **Guard state transitions are logged** — FLAG → CLEAR, CLEAR → FLAG, each change emits a journal entry (→ 2026-04-10 through 2026-04-11 daily guard health checks)
- **Guard state is venue-scoped when appropriate** — don't freeze all trading because Kalshi balance drifted; Alpaca is independent (→ G4 2026-04-10)
- **Health check ritual** — a dedicated "Guard Health Check" experiment type runs periodically, producing a full guard-matrix snapshot. Provides a reliability baseline and early-warning for drift.
- **Auto-reset is safer than manual reset** — G6's reset on F006 WIN was triggered by fill-poller detection of SETTLED_WIN, not by a human clicking "reset." Reduces operator error.

## Open Questions

- Should G6 be per-category (weather-only, CPI-only) rather than global? A 2-loss streak in weather shouldn't freeze unrelated Kalshi categories if they trade differently.
- What's the threshold for building G8, G9, etc? The framework grew from 5 to 7 in a month. When does it stop growing?
- How do we handle guard conflicts? If G6 says FLAG but G5 says pass on a rare high-score setup, who wins? Current behavior is "strictest guard wins" — is that right?

## Cross-References

- See also: [Weather Trading](./weather_trading.md) — most G6 transitions traced to weather outcomes
- See also: [Kalshi Orderbook](./kalshi_orderbook.md) — G1 (position cap) and G4 (balance) both interact with Kalshi mechanics
