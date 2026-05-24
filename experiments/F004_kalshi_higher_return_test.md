---
lifecycle: archived
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags: []
---

# Experiment F004 — Kalshi Higher-Return Test

**Date:** 2026-04-04
**Mode:** LIVE_MODE (real money)
**Session budget:** $30 (one-time override — higher-return lens)
**Tag:** F004
**Label:** kalshi_higher_return_test
**Status:** ❌ RESOLVED — LOSS. Chicago high exceeded 54°F. All 284 contracts expired worthless. -$28.19.

## Hypothesis

Can frAInk identify asymmetric, mispriced prediction markets by combining NWS same-day forecasts with real-time weather observations? The higher-return lens prioritizes 5x+ payout opportunities where the market underprices a realistic outcome. This is the first experiment testing frAInk's ability to find genuine edge beyond simple forecast-vs-bracket plays.

## The Pipeline Story

F003 was a conservative bracket play (NO on a temperature range 2-3 degrees above forecast). F004 asks: what happens when we deliberately hunt for mispriced tails?

**Market scan:** Pulled 49 weather, 66 economics, 4 financials, 0 crypto markets from Kalshi. Economics markets (CPI, inflation surge) were interesting but definition uncertainty made them inactionable. Financials were all long-dated IPO plays. Weather was the hunting ground.

**The discovery:** NWS forecast for Chicago O'Hare on April 4: high of 55 degrees F. The Kalshi market KXHIGHCHI-26APR04-T55 asks "Will the high be 54 degrees or below?" — priced at YES $0.06-0.07. The market was giving this only 6-7% probability.

**Current conditions confirmed the thesis:** At 8:20 AM CDT, O'Hare reported 50 degrees F, mist, overcast skies, 96% humidity, south wind 6 mph. These conditions suppress afternoon heating. With the NWS forecasting 55 and a standard forecast error of plus or minus 2-3 degrees, the real probability of staying at 54 or below is roughly 25-35% — four to five times what the market was pricing.

**Market dynamics:** Between the initial scan and order placement, the market shifted toward our thesis. The B55.5 band (55-56 degrees) jumped from $0.25 to $0.43, and the B59.5 band (59-60 degrees) collapsed from $0.27 to $0.03. The market agreed heating was suppressed — but T55 (the below-threshold play) stayed cheap at $0.07-0.10.

**Execution:** Three order rounds, F003 liquidity playbook:
1. 285 contracts at $0.07 — 14 filled, 271 resting (thin sell-side depth)
2. Cancelled, resubmitted 230 at $0.08 — 0 filled
3. Cancelled, resubmitted 270 at $0.10 — 21 filled immediately, remaining 249 filled within 60 seconds

Total: 284 contracts filled (14 at $0.07, 270 at $0.10).

## Markets Researched

### Candidates Evaluated (119 markets scanned across 4 categories)

| Market | Assessment | Decision |
|--------|-----------|----------|
| KXHIGHCHI-26APR04-T55 (Chicago less than 55) | NWS 55, mist/overcast, market 6-7% vs 25-35% est. | **PLACED** — 4-5x mispricing |
| KXHIGHCHI-26APR04-B55.5 (Chicago 55-56) | NWS forecast center. Initially $0.25. | Considered — edge eroded to $0.43 before execution |
| KXHIGHMIA-26APR04-B82.5 (Miami 82-83) | NWS 82. Market 54% YES vs 35% est. | **SKIPPED** — YES overpriced, NO side had edge but low return |
| KXHIGHLAX-26APR04-B80.5 (LA 80-81) | NWS 80. Santa Ana winds add uncertainty | **SKIPPED** — thin edge, upside uncertainty from wind event |
| KXCPI-26MAR-T0.9 (CPI more than 0.9%) | Resolves Apr 10. | **SKIPPED** — can't confirm SA vs NSA metric definition |
| KXLCPIMAXYOY-27-P5 (Inflation 5% in 2026) | Liberation Day tariffs make 5% plausible | **SKIPPED** — long-dated, ties up capital |

## Prediction Placed

| Market | Side | Contracts | Avg Price | Cost | Fees | Total | Thesis |
|--------|------|-----------|-----------|------|------|-------|--------|
| KXHIGHCHI-26APR04-T55 | YES | 284 | ~$0.099 | ~$27.66 | ~$0.53 | ~$28.19 | NWS forecast 55 for O'Hare. Current 50 at 8:20 AM, mist, overcast, 96% humidity. Conditions suppress heating. Market priced 6-7%, real probability 25-35%. 10:1 asymmetric payout. |

## Outcomes

| Scenario | Payout | Profit/Loss | ROI |
|----------|--------|-------------|-----|
| YES wins (high 54 or below) | $284.00 | +$255.81 | +907% |
| NO wins (high 55 or above) | $0.00 | -$28.19 | -100% |

**Resolution:** April 5, 2026 05:59 UTC — based on NWS Climate Report (CLI) for Chicago O'Hare.

## What This Tests

1. **Edge detection beyond simple bracket plays** — Can frAInk identify threshold markets where the probability is mispriced, not just bracket markets near the forecast mode?
2. **Real-time data integration** — Current weather observations (temp, conditions, humidity) materially improved the probability estimate vs forecast alone.
3. **Execution under thin liquidity** — Three order rounds with price improvement, same playbook as F003. Market ultimately provided depth at $0.10.
4. **Asymmetric position sizing** — $28.19 risk for $284 potential payout. The higher-return lens found a genuine asymmetric setup.

## Budget Accounting

| Item | Amount |
|------|--------|
| Starting Kalshi balance | $52.74 (including F003 +$2.74 win) |
| F004 cost (incl fees) | ~$28.19 |
| Remaining balance | $24.55 |
| F004 session budget cap | $30.00 |
| Under budget by | ~$1.81 |

## F003 Resolution (recorded during this session)

Phoenix B88.5 NO resolved in frAInk's favor. 10 contracts at $0.72 = $7.26 cost. Payout $10.00. **Profit: +$2.74 (37.7% ROI).** NWS confirmed Phoenix high stayed below 88 degrees. The forecast-based edge held.

## Resolution — 2026-04-05

**Result:** ❌ LOSS. Chicago high exceeded 54°F. The ≤54°F threshold was not met. All 284 contracts expired worthless.

| Metric | Value |
|--------|-------|
| Loss | -$28.19 (100% of position) |
| Kalshi record after F004 | 1W-1L |
| Net Kalshi P&L | -$25.45 |
| Remaining balance | ~$24.55 |

**Post-mortem:** The 25-35% probability estimate was too aggressive. NWS forecast of 55°F was the central estimate — needing ≤54°F required the forecast to be wrong by ≥1°F in a specific direction. Early-morning suppression (mist, overcast) doesn't cap afternoon heating. Position size was ~53% of bankroll — roughly 3x Kelly even at the estimated probability. Threshold markets near the forecast center are fundamentally different from bracket markets away from the mode. Edge estimation needs recalibration.

---

*Experiment F004 — frAInk's second real-money prediction. First deliberate hunt for asymmetric mispricing. The thesis was directionally interesting but the probability estimate and position sizing were both too aggressive. Lesson: don't confuse an interesting thesis with a calibrated one.*
