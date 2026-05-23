---
lifecycle: active
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags:
- needs_audit
---

# Experiment F003 — First Live Kalshi Predictions

**Date:** 2026-04-02
**Mode:** LIVE_MODE (real money)
**Session budget:** $20 (one-time override)
**Status:** ✅ Complete — WIN +$2.74 (37.7% ROI)

## Hypothesis
First real-money prediction market experiment. Testing whether frAInk can identify
weather markets with a genuine directional edge using live orderbook data + Tavily
research, place disciplined limit orders, and document his reasoning transparently.

## The Pipeline Story

This experiment tested the full autonomous pipeline end-to-end for the first time with real money. Three runs, three lessons.

**Run 1 — API bug discovered.** frAInk researched 25 weather markets across Miami, Chicago, and Phoenix. Identified two candidates. Attempted to place both orders. Both returned 400 Bad Request. Root cause: `integrations/kalshi.py` was sending prices as dollar floats (`0.66`) on the production API — Kalshi expects integer cents (`66`). The demo branch had the conversion; the prod branch didn't. Bug had been invisible because all prior experiments used demo mode.

**Run 2 — Self-correction in action.** After the API fix, frAInk re-researched both markets. Planner caught a critical thesis error on Miami: the original thesis claimed NWS forecast mode of 80-81°F falls in the B79.5 bracket, but 81°F actually falls *inside* the B81.5 bracket (81-82°F). Betting NO on the forecast mode bracket would have been -EV. Policy blocked Miami (confidence 2/10) and approved only Phoenix (confidence 5/10). The three-agent architecture caught a bad trade before real money was at risk.

**Run 3 — Getting in the game.** The Phoenix order was placed at $0.66 but sat resting — wide spread, no counterparty. Frank authorized improving the price to $0.72 to cross the spread. Cancelled the $0.66 order, placed at $0.72. Partial fill of 4 contracts immediately, remaining 6 filled within 3 minutes. All 10 contracts executed.

## Markets Researched

### Candidates Evaluated (25 weather markets scanned)

| Market | City | Bracket | Assessment | Decision |
|--------|------|---------|-----------|----------|
| KXHIGHMIA-26APR02-B81.5 | Miami | 81-82°F | Forecast 81°F = INSIDE bracket | **BLOCKED** — thesis error |
| KXHIGHTPHX-26APR02-B88.5 | Phoenix | 88-89°F | NWS says 86°F, 2-3°F above | **PLACED** — edge confirmed |
| Chicago B70.5 | Chicago | 70-71°F | 90% precip, wide distribution | Rejected — no clear edge |
| Chicago T73 | Chicago | >73°F | ~4% market, ~3-5% true | Rejected — efficient |
| Miami B83.5 | Miami | 83-84°F | Strong edge but $0.86 risk/$0.14 reward | Rejected — terrible capital efficiency |
| Phoenix B86.5 | Phoenix | 86-87°F | ~51% matches NWS "near 86" | Rejected — no edge |

## Prediction Placed

| Market | Side | Contracts | Price | Cost | Fees | Total | Thesis |
|--------|------|-----------|-------|------|------|-------|--------|
| KXHIGHTPHX-26APR02-B88.5 | NO | 10 | $0.72 | $7.20 | $0.06 | $7.26 | NWS forecast 86°F for Phoenix Apr 2. The 88-89°F bracket is 2-3°F above same-day forecast. True probability ~15-25% vs market pricing 28%. Phoenix has been cooling: 105°F Mar 19 → 86°F Apr 1. |

**Order ID:** `26d1f784-556d-414e-8e86-b4690750f6c4`
**Status:** Executed (all 10 contracts filled)
**Fill breakdown:** 4 taker fills @ $0.72, 6 maker fills @ $0.72

## Total Spent
$7.26 / $20.00 session budget (including $0.06 fees)

## Expected Outcome
- **If NO wins** (Phoenix high ≠ 88-89°F): Payout $10.00, **profit $2.74** (37.7% ROI)
- **If YES wins** (Phoenix high = 88-89°F): Lose $7.26
- **Resolution:** NWS Climatological Report (CLI) for Phoenix Sky Harbor. Market closes Apr 3 07:00 UTC, expires Apr 9 14:00 UTC.

## Technical Milestones
1. **First successful live Kalshi order** — API bug (cents vs dollars) fixed in-session
2. **Three-agent self-correction** — Policy blocked a bad thesis (Miami) that would have lost money
3. **Full pipeline validated end-to-end** — research → thesis → order → fill → position tracking, all with real money

## Results

**Outcome: WIN**
- Phoenix actual high on Apr 2: below 88°F (market resolved as "no" — high was NOT 88-89°F)
- Payout: $10.00 (10 contracts x $1.00)
- Cost: $7.26
- **Profit: +$2.74 (37.7% ROI)**
- Market status: finalized
- Resolution confirmed: 2026-04-04 session

## What frAInk Learned

**The pipeline's self-correction is the real product.** The most important outcome isn't the $2.74 potential profit — it's that frAInk caught his own thesis error on Miami before spending money. The three-agent architecture (Planner re-researches → Policy evaluates independently → blocks bad trades) worked exactly as designed. If this had been a single-agent system, the Miami bet would have gone through.

**Limit orders require spread awareness.** The initial $0.66 order sat resting because the book had a $0.06 spread (NO bids at $0.66, YES bids at $0.28 — need sum ≥ $1.00 to cross). Understanding orderbook mechanics matters as much as having a thesis.

**Edge in weather markets is real but thin.** Of 25 markets scanned, only 1 survived full research with confirmed positive edge. Low hit rate is probably correct — if edge were everywhere, it wouldn't be edge.

**First-run bugs are infrastructure debt, not failures.** The cents-vs-dollars bug was invisible in demo mode. Only live money exposed it. This is exactly why you start small.

---

*F003 is frAInk's first real-money experiment. Win or lose, the pipeline works.*
