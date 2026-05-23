---
lifecycle: active
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags:
- needs_audit
---

# F006 — Chicago Midway B54.5 NO (Weather Prediction)

**Date placed:** 2026-04-10 13:42 UTC
**Date resolved:** 2026-04-11 14:00 UTC
**Venue:** Kalshi (LIVE)
**Status:** ✅ **WIN — +$5.40 (56% ROI)**
**Type:** TYPE 2 Live Action — Weather prediction market

---

## Context

F006 was the first Kalshi trade after Guard #6 (consecutive-loss counter) went live. Guard #6 had reached FLAG state at `consecutive_losses=2` after the F004 + F005 losses, and a win here was the reset mechanism designed into the guard. Frank explicitly instructed frAInk to find "a conservative win, not a swing for the fences" — direct reaction to the oversized F004 and F005 positions that preceded it.

This was also the first Kalshi trade placed under the new `$25 session budget` cap structure and the first where frAInk called `get_kalshi_balance` autonomously in the Planner phase to size against real capital instead of guessing from env overrides.

---

## The Play

**Market:** `KXHIGHCHI-26APR10-B54.5` — Will the high at Chicago Midway on 2026-04-10 be in the 54–55°F bracket?
**Side:** NO (bet it won't be 54–55°F)
**Size:** 15 contracts @ $0.64
**Cost:** $9.60
**Max payout:** $15.00
**Max profit:** $5.40

**Thesis:** NWS forecast for Chicago Midway was 49°F for Apr 10, with METAR showing 45°F at the time of entry under OVC006 (600ft ceiling), mist, N wind 15kt. Five independent sources converged on a high well below the 54°F bracket floor:

1. **NWS forecast:** 49°F
2. **METAR observation:** 45°F current (cooler than forecast baseline)
3. **WeatherBug:** ~48°F forecast
4. **world-weather.info:** ~47°F forecast
5. **NWS CLI historical:** no warming catalysts visible

The bracket required a **5-6°F NWS bust with zero warming catalysts** to land in the 54–55°F range. Estimated 24 percentage-point edge. Planner confidence 8/10.

---

## Guard #6 Test

F006 was placed while Guard #6 was in **FLAG** state (`consecutive_losses=2` after F004 and F005 losses). The guard's design:

- **FLAG** (N=2): confidence ≥6 required AND explicit Planner research support required; still allowed
- **BLOCK** (N=3): hard block, no new Kalshi positions
- **Reset**: any WIN → counter back to 0

F006 met both FLAG requirements (confidence 8/10 with 5-source research), so Policy approved. A loss would have tripped Guard #6 to BLOCK on the next attempt. A win resets it to CLEAR — which is exactly what happened.

---

## Resolution

Chicago Midway's Apr 10 high settled at approximately 48°F, well below the 54°F bracket floor. All 15 NO contracts resolved in favor.

- **Fill poller auto-upgraded** the receipt from `FILLED` → `SETTLED_WIN` overnight 2026-04-10 → 2026-04-11
- **Guard #6** auto-reset to `CLEAR` state on the next `get_trade_history_stats()` evaluation
- **Receipt:** `receipts/F006_c1150f26.json` — HMAC signed, profit +$5.40

---

## Kalshi Record After F006

| Experiment | Result | P&L |
|-----------|--------|-----|
| F003 | WIN | +$2.74 |
| F004 | LOSS | -$28.19 |
| F005 | LOSS | -$13.25 |
| **F006** | **WIN** | **+$5.40** |
| **Net realized** | **2W-2L** | **-$33.30** |

Down from -$38.70. Still negative net, but the drawdown is recovering and the direction is right.

---

## The Real Lesson — Cushion Size Matters

F005 vs F006 is the cleanest A/B test in the Kalshi ledger so far:

| | F005 (Miami) | F006 (Chicago) |
|---|---|---|
| Forecast | 81–83°F | 49°F |
| Bracket | 84–85°F | 54–55°F |
| Cushion | 1–2°F | 5–6°F |
| Sources | 3 (NWS, AccuWeather, Polymarket) | 5 (NWS, METAR, WeatherBug, world-weather.info, NWS CLI) |
| Confidence | 7/10 | 8/10 |
| Size | 25 contracts | 15 contracts |
| Result | LOSS -$13.25 | WIN +$5.40 |

**The pattern:** Multi-source forecast consensus works when the divergence between forecast and bracket is large enough to survive normal weather variance. F005's 1–2°F cushion was inside the noise floor; F006's 5–6°F cushion was outside it. This is also why F006 was smaller — not because the thesis was weaker, but because the cushion made the sizing discipline easier (smaller position, higher probability).

**Rule candidate for Step 5's proposer:** *"Weather NO bracket plays require at least 4°F cushion between consensus forecast and bracket floor/ceiling."* Sample size is still small (n=2) but the signal is clear and directionally confirmed.

---

## What This Validates

- **Guard #6 counter-reset works in production** — first time the reset path executed on real data. Counter went 2→0, state went FLAG → CLEAR, without any manual intervention.
- **Fill poller + receipt writer pipeline is reliable** — second consecutive automatic overnight settlement capture. F005 and F006 both resolved without manual sweep of the receipt state.
- **`get_kalshi_balance` tool is load-bearing** — Planner called it autonomously to confirm sizing against the real $48.80 balance. This is the first experiment that was sized against an API-confirmed balance instead of env overrides.
- **Frank's "no swing-for-fences" directive was the right call** — conservative 15 contracts at 56% max ROI vs F004's 284 contracts at theoretical 1000% ROI. Small position compounds; big position catastrophes don't.

---

## Process Gap Still Open

The downstream docs (experiments.md, this scoreboard file, this lab entry) are still manually updated after each F-series resolution. The fill poller handles the receipt, Guard #6 handles the state, but **the publishing step is manual**. This is exactly the gap the Step 11 self-build `publish_experiment` tool from `FRAINK_AUTONOMY_ARCHITECTURE.md` is designed to close. Once built, frAInk will write this lab file himself on settlement, not me.

For now: F005 manually swept 2026-04-10, F006 manually swept 2026-04-11. When auto-publish lands, the manual sweeps end.

---

*F006 — the breaking win. Proved Guard #6's counter-reset path, validated the fill poller for back-to-back experiments, and gave us the first clean A/B test on weather-market cushion sizing. Not the biggest trade, but structurally one of the most important.*
