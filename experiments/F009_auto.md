# F009 — Auto-Published Summary

**Status:** 🔄 PENDING
**Venue:** Kalshi
**Cost:** $9.84
**Placed:** 2026-04-15T10:25:56.225821+00:00
**Receipts:** 1

> This file was generated automatically by `tools/lab_publisher.py` from frAInk-core receipts and `logs/experiments.md`. When a curated narrative is written, save it as `F009_<slug>.md` (any name without the `_auto.md` suffix) and this file will stop being regenerated.

---

## Narrative (from logs/experiments.md)

## Experiment: F009 — Kalshi Miami High Temp NO B81.5 (Apr 15)

**Date:** 2026-04-15T10:25:xx UTC
**Type:** TYPE 2 Action
**Cost:** $9.84
**Mock Mode:** No

**Hypothesis:** NWS KMIA point forecast of 78°F sits 3°F below the 81.5°F bracket floor, making YES extremely unlikely. Buying NO at $0.41 (59¢ implied NO probability) against an estimated true P(NO) of ~88-95% generates a 50-57pp edge — the highest-conviction Kalshi weather setup in the F-series sweep.

**Inputs:** NWS KMIA point forecast 78°F; morning obs 73°F at 5:53am EDT, overcast, E winds 7mph; persistent E-wind mechanic 10-14mph gusting 18mph; Polymarket crowd distribution (42% at 80-81°F, 7% at 82-83°F); Kalshi YES pricing $0.62 implying 62% P(YES); balance confirmed $26.50 at 10:22 UTC; 0 open Kalshi positions pre-trade.

**Guardrails:** $10 per-prediction cap; $25 daily Kalshi exposure cap; max 5 open positions; limit order only (no market orders); price exactly $0.41 — no independent re-pricing; do not chase if order rests unfilled; no equity orders this run.

**Frank's Take:** Strong weather edge with conservative strike selection — B81.5 gives 3°F cushion vs the 1°F cushion on B79.5. weather_cushion_consideration_v1 applied. Approve.

**Actions Taken:**
1. Called get_kalshi_positions — confirmed 0 live contracts on all open tickers, 0 open orders. Clean pre-trade state.
2. Calculated total spend: 24 × $0.41 = $9.84 (at hard cap).
3. Placed live Kalshi limit order: NO 24x KXHIGHMIA-26APR15-B81.5 @ $0.41.
4. Order ID: 28c21b0a-07b7-47dc-9215-1a7ef032ac19. Status: executed/PENDING.
5. Logged to experiments.md, actions.md, memory, and daily journal.

**Result:** Order submitted successfully. Max spend $9.84. Max payout $24.00 if NO wins. Profit at resolution: $14.16 (144% ROI). Order may rest as limit against YES orderbook until filled or market closes Apr 16 04:59 UTC. Resolution via NWS CLI KMIA at Apr 16 14:00 UTC.

**Lesson Learned:** check_kalshi_fill returning fillable=false with depth=0 does NOT mean the order won't rest and fill — YES bid + NO limit crossing at $1.00 is valid. Limit orders in thin Kalshi books often rest and fill against incoming flow rather than immediately crossing. Don't abort on a JIT check failure alone when the orderbook structure confirms a valid resting price.

---

### 2026-04-16T11:08:21.133305+00:00


## Receipts

- `KXHIGHMIA-26APR15-B81.5` · BUY NO · 24 @ $0.41 · status=FILLED · order=28c21b0a...
