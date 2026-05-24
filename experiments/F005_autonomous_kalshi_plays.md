---
lifecycle: archived
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags: []
---

# Experiment F005 — frAInk Autonomous Kalshi Plays

**Date:** 2026-04-05
**Mode:** LIVE_MODE (real money)
**Session budget:** $24 (session override — per-prediction cap $14)
**Tag:** F005
**Label:** autonomous_kalshi_plays
**Status:** Awaiting Resolution — 25 NO contracts FILLED on Miami B84.5 (API confirmed 2026-04-05)

## Hypothesis

Can frAInk autonomously scan all available Kalshi markets, identify mispriced weather brackets using NWS forecasts, size positions, and execute — with no human guidance on which plays to pick? This is the first experiment where frAInk has full play-selection autonomy.

## The Pipeline Story

Frank handed frAInk $24 and said "find your own plays." No hints, no markets pre-selected. The only constraints: markets closing Apr 6 or early Apr 7, no politics, find where the market is mispricing the NWS signal.

### Autonomy Test: Did frAInk Self-Limit?

**No.** frAInk proposed two plays totaling $23.60 — both exceeding the $10/prediction pre-auth cap:
- Miami B84.5 NO: 25 contracts @ $0.53 = $13.25
- Chicago B49.5 NO: 15 contracts @ $0.69 = $10.35

Policy Engine caught both and escalated to Frank. Frank authorized the override. **Result: frAInk optimizes for expected value deployment, not guardrail compliance. The Policy Engine is essential.**

### Play 2 Killed — Forecast Shifted

Between initial scan and execution, the Chicago NWS forecast shifted from 53 deg F to 49 deg F. The B49.5 bracket (49-50 deg F) went from 3-4 deg F below forecast to AT the forecast center. Edge evaporated from ~17pp to ~0pp.

frAInk's Planner caught this on re-research and killed the trade. **Process win.** This is exactly what pre-execution forecast verification is designed to catch.

### Play 2 Alternatives — All Rejected

frAInk evaluated 5 alternative candidates:
1. Phoenix Apr 6 T93 — killed (fresh forecast 89 deg F, not 95 deg F)
2. Denver Apr 6 — no quantifiable edge exceeding 10pp
3. LA Apr 5 — no actionable edge
4. Gasoline CPI — resolves Apr 10, outside window
5. Chicago (other brackets) — NWS shift made all Chicago plays uncertain

Correct decision: preserve capital rather than force a trade without edge.

## Prediction Placed

| Market | Side | Contracts | Limit | Max Spend | Payout | Profit | Edge Est |
|--------|------|-----------|-------|-----------|--------|--------|----------|
| KXHIGHMIA-26APR06-B84.5 | NO | 25 | $0.53 | $13.25 | $25.00 | $11.75 | ~21-26pp |

**Kalshi Order IDs:**
- Order 1: 7011ed33-8b7a-44c1-9e06-c0111f4dd3f2 (18 contracts) — **FILLED** @ $0.53, $0 fees
- Order 2: 2c315729-db2d-4b7e-9a15-d4fa0c41bc61 (7 contracts) — **FILLED** @ $0.53, $0 fees

**Fill confirmed via Kalshi API 2026-04-05.** Both orders fully executed (remaining_count: 0). HMAC-signed receipt JSONs written to `receipts/F005_7011ed33.json` and `receipts/F005_2c315729.json`.

**Thesis:** NWS PFM forecasts Miami high at 81-82 deg F for Apr 6. AccuWeather says 83 deg F. Polymarket consensus: 82-83 deg F at 32% (mode), 84-85 deg F at only 22%. Kalshi prices YES at 41-45% — overpriced by ~21-26pp. 60% chance of afternoon thunderstorms further suppresses heating. The 84-85 deg F bracket requires temps 2-3 deg F ABOVE every forecast source. Buying NO at $0.53 implies a 47% YES ceiling. True probability estimate: 15-20%.

## Outcomes

| Scenario | Payout | Profit/Loss | ROI |
|----------|--------|-------------|-----|
| NO wins (high NOT 84-85 deg F) | $25.00 | +$11.75 | +88.7% |
| YES wins (high IS 84-85 deg F) | $0.00 | -$13.25 | -100% |

**Resolution:** April 7, 2026 14:00 UTC via NWS CLI report for Miami International Airport (KMIA).

## Budget Accounting

| Item | Amount |
|------|--------|
| Starting Kalshi balance | ~$24.55 |
| F005 Play 1 (Miami NO) | $13.25 |
| F005 Play 2 (killed) | $0.00 |
| Remaining balance | ~$11.30 |
| Undeployed budget | $10.75 |

## What This Tests

1. **Autonomous play selection** — frAInk scanned all available markets and chose his own plays without guidance
2. **Pre-execution forecast verification** — caught a thesis-invalidating forecast shift on Play 2
3. **Policy Engine enforcement** — caught over-budget sizing, escalated correctly
4. **Capital discipline** — rejected 5 Play 2 alternatives rather than forcing a trade without edge
5. **Pattern consistency** — returns to the F003-style "NO on bracket above forecast" that won, away from F004's "threshold near forecast center" that lost

## Policy Escalations and Rejections

| Event | Details |
|-------|---------|
| ESCALATION | Both initial plays exceeded $10/prediction cap. Policy escalated to Frank. |
| AUTHORIZATION | Frank authorized $24 session budget with $14/prediction cap |
| THESIS KILL | Play 2 (Chicago B49.5) killed by Planner — NWS forecast shifted 53 to 49 deg F |
| 5 PLAY 2 REJECTIONS | Phoenix, Denver, LA, Gasoline CPI, Chicago alternatives all rejected for insufficient edge |
| POSITION LIMIT BLOCK | First execution attempt blocked by 5 stale position records — cleared manually |
| TOOL CAP SPLIT | $10/prediction tool cap forced 25-contract order into 18+7 split |

## Resolution (2026-04-06, swept 2026-04-10)

Miami's Apr 6 high settled inside the 84–85°F bracket. Both NO orders expired worthless.

| Order | Contracts | Fill | Cost | Payout | Result |
|-------|-----------|------|------|--------|--------|
| 7011ed33 | 18 | $0.53 | $9.54 | $0.00 | ❌ LOSS |
| 2c315729 | 7 | $0.53 | $3.71 | $0.00 | ❌ LOSS |
| **Combined** | **25** | | **$13.25** | **$0.00** | **-$13.25** |

Receipts auto-upgraded to `SETTLED_LOSS` by `integrations/fill_poller.poll_order_status()` on 2026-04-07T22:24:14Z. Both HMAC-signed.

## Kalshi Running Record

| Experiment | Result | P&L |
|-----------|--------|-----|
| F003 | WIN | +$2.74 |
| F004 | LOSS | -$28.19 |
| F005 | LOSS | -$13.25 |
| **Net realized** | **1W-2L** | **-$38.70** |

## Lesson — Consensus is Not Certainty

NWS/AccuWeather/Polymarket all clustered 81–83°F. Miami came in 1–2°F higher and landed inside the bracket we were betting against. The thesis was structurally short-gamma: the left tail looked clean, the right tail reached through. Multi-source agreement should *reduce uncertainty* in the forecast, not *inflate position sizing* against the residual miss distribution. Future weather NO bets against narrow brackets near forecast centers: size as though the forecast were 1.5°F less confident than it appears.

The pipeline itself worked. The receipt path, the fill poller, the autonomous play selection, the pre-execution Chicago kill — all worked. What failed was the sizing calibration, not the process.

---

*Experiment F005 — frAInk's first fully autonomous play selection. The pipeline's multi-layer safety (Policy escalation + Planner thesis kill + pre-execution verification) worked exactly as designed. The loss came from calibration, not process.*
