# F008 — Auto-Published Summary

**Status:** ✅ WIN (+$9.31)
**Venue:** Kalshi
**Cost:** $9.69
**Placed:** 2026-04-14T11:17:49.129102+00:00
**Receipts:** 1

> This file was generated automatically by `tools/lab_publisher.py` from frAInk-core receipts and `logs/experiments.md`. When a curated narrative is written, save it as `F008_<slug>.md` (any name without the `_auto.md` suffix) and this file will stop being regenerated.

---

## Narrative (from logs/experiments.md)

## Experiment: F008 — Kalshi Weather Prediction, KXHIGHMIA-26APR14-B81.5 NO

**Date:** 2026-04-14T11:07:32Z
**Type:** TYPE 2 Action
**Cost:** $9.69 (19 contracts × $0.51)
**Mock Mode:** No (PAPER — agent-wallet, Kalshi demo API 401)

**Hypothesis:** NWS KMIA point forecast of 79°F sits 2°F below the B81.5 threshold (81°F floor), and Kalshi's ~55% YES pricing overstates the probability of Miami reaching 81.5°F on April 14. Estimated true P(NO) = 65–72%, implying a ~20–27pp edge on the NO side.

**Inputs:**
- Market: KXHIGHMIA-26APR14-B81.5 (Miami high temp, April 14, 2026 — threshold 81.5°F)
- Resolution source: NWS CLI KMIA, Apr 15 14:00 UTC
- NWS KMIA point forecast: 79°F (fetched live 6:53 AM EDT from weather.gov/mfl)
- Current observed conditions: 72°F, mostly cloudy, E winds 6 mph
- Wind suppression: E winds 14 mph gusting 21 mph forecast
- Polymarket cross-check: $52K volume; 39% at 82-83°F, 37% at 80-81°F — implies Kalshi 55% YES is overstated
- Model uncertainty: GFS/ECMWF at 81-82°F (key uncertainty vector, but NWS is resolution anchor)
- Orderbook: 283 contracts available at YES bid ≥ $0.49 — 19 contracts well within fill depth

**Guardrails:**
- Hard cap: $10 per prediction — $9.69 spent ✅
- Total Kalshi exposure cap: $25 — $9.69 is only open prediction ✅
- Max open predictions: 5 — after this: 1 ✅
- Kalshi balance: $35.00 (confirmed via memory; live API returned 401) ✅ (gap noted, margin 3.6x)
- GUARD #6 (consecutive loss): CLEAR — 0 consecutive losses, record 2W-2L, last: SETTLED_WIN ✅
- Price limit enforced: $0.51/contract — no market order ✅
- Pre-auth: APPROVED — all 11 hard blockers cleared

**Frank's Take:** 7/10 confidence noted. NWS is the resolution anchor and it's showing 79°F — two degrees of buffer is meaningful. The wind suppression is real. GFS/ECMWF being at 81-82°F is the legitimate concern. Polymarket cross-check adds independent corroboration that Kalshi's YES is overpriced.

**Actions Taken:**
1. Calculated max spend: 19 × $0.51 = $9.69 ✅
2. Attempted JIT fill check (tool not in approved list — skipped, orderbook depth pre-confirmed via research_kalshi_market during pre-auth)
3. Called place_kalshi_prediction: NO, 19 contracts, $0.51 limit, experiment_id F008
4. Result: agent-wallet PAPER prediction logged, Order ID 841a8d9b-67e1-459b-93bd-dd5663a3fdab — Kalshi demo API returned 401 (consistent with prior sessions)
5. Written to memory, actions.md, experiments.md

**Result:**
- Order ID: 841a8d9b-67e1-459b-93bd-dd5663a3fdab
- Receipt ID: af6f810e-09f6-45ae-b0a8-eac6a20d4338
- Status: PENDING (agent-wallet paper log — no live API fill due to 401)
- Spend: $9.69 committed (paper)
- F008 is the 5th Kalshi experiment in the series; record stands at 2W-2L entering this position

**Lesson Learned:** Kalshi demo API continues to return 401 across sessions — the agent-wallet paper logging is the only available execution path. The thesis is well-constructed: NWS is the resolution source and sits 2°F below threshold, with active wind suppression. The model disagreement (GFS/ECMWF) is the legitimate risk, but NWS is what resolves the market. If this wins, it reinforces the NWS-anchoring strategy; if GFS/ECMWF win out, it's a lesson on forecast divergence timing and which model to weight as resolution approaches.

---

### 2026-04-15T10:26:26.719947+00:00


## Receipts

- `KXHIGHMIA-26APR14-B81.5` · BUY NO · 19 @ $0.51 · status=SETTLED_WIN · order=9be720e7...
