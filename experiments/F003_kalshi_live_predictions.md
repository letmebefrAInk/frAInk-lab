# Experiment F003 — First Live Kalshi Predictions

**Date:** 2026-04-02
**Mode:** LIVE_MODE
**Session budget:** $20 (one-time override)
**Status:** 🔄 In Progress — order resting, awaiting fill + resolution

## Hypothesis
First real-money prediction market experiment. Testing whether frAInk can identify
weather markets with a genuine directional edge using live orderbook data + Tavily
research, place disciplined limit orders, and document his reasoning transparently.

## Markets Researched

### Candidates Evaluated (25 weather markets scanned across Miami, Chicago, Phoenix)

| Market | City | Bracket | Initial Assessment | Final Decision |
|--------|------|---------|-------------------|----------------|
| KXHIGHMIA-26APR02-B81.5 | Miami | 81-82°F | YES $0.53 / NO $0.43 | **BLOCKED** — thesis error caught |
| KXHIGHTPHX-26APR02-B88.5 | Phoenix | 88-89°F | YES $0.28 / NO $0.66 | **PLACED** — edge confirmed |
| Chicago B70.5 | Chicago | 70-71°F | ~28-31% YES | Rejected — storm makes distribution too wide |
| Chicago T73 | Chicago | >73°F | ~4% YES | Rejected — market efficient |
| Miami B83.5 | Miami | 83-84°F | NO ~$0.86 | Rejected — terrible capital efficiency |
| Phoenix B86.5 | Phoenix | 86-87°F | ~51% YES | Rejected — no edge, matches NWS forecast |

### Miami B81.5 — Why It Was Blocked

Initial thesis claimed NWS forecast mode of 80-81°F falls in the B79.5 bracket, not B81.5. Planner's re-research on the retry run caught the error: NWS forecast is "high 81°F" which falls **inside** the B81.5 (81-82°F) bracket. Betting NO on the forecast mode bracket is -EV. Policy blocked it at confidence 2/10.

**This is the system working.** The three-agent pipeline (Planner re-researches → Policy evaluates independently → blocks bad trades) caught a thesis error before real money was at risk.

## Predictions Placed
| Market | Side | Contracts | Price | Cost | Thesis |
|--------|------|-----------|-------|------|--------|
| KXHIGHTPHX-26APR02-B88.5 | NO | 10 | $0.66 | $6.60 (max) | NWS says 86°F for Phoenix Apr 2. The 88-89°F bracket is 2-3°F above forecast. True probability ~15-25% vs market 28%. Marginal but positive edge. |

**Order ID:** `4fccbe4e-a7eb-4cd3-8aa0-2aad2679124c`
**Order Status:** Resting (limit order, not yet filled)
**Resolution:** NWS CLI report at Phoenix Sky Harbor, Apr 3 2026 ~19:00 UTC

## Total Spent
$6.60 / $20.00 session budget (if order fills)

## Technical Milestone
**First successful live Kalshi order placement.** The initial run failed with 400 Bad Request — the `create_order` method in `integrations/kalshi.py` was sending prices as dollar floats (e.g., `0.66`) on the production API but Kalshi expects integer cents (`66`) on both demo and prod. Bug fixed in-session, retry succeeded.

## Results
<!-- Fill in after resolution — check NWS CLI report for Phoenix Apr 3 after 19:00 UTC -->

## What frAInk Learned

**The pipeline's self-correction works.** The most important outcome of F003 isn't the Phoenix prediction — it's that frAInk caught his own thesis error on Miami before spending money. Run 1 proposed both trades. Run 2's Planner independently re-researched and flagged the bracket mismatch. Policy evaluated the updated research and blocked Miami while approving Phoenix. That's exactly what the three-agent architecture is designed for.

**Limit orders require patience.** The Phoenix order is resting at $0.66 — it may or may not fill before resolution. This is correct behavior (no market orders per pre-auth rules), but it means frAInk needs to accept that some edge opportunities will expire unfilled.

**Edge in weather markets is real but thin.** Of 25 markets scanned, only 1 had confirmed positive edge after full research. The hit rate for actionable predictions is low — which is probably correct. If edge were everywhere, it wouldn't be edge.

**The API fix was critical infrastructure.** The cents-vs-dollars bug blocked all live order placement. Now that it's fixed, the full pipeline works end-to-end for the first time.
