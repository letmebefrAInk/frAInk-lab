# Weather Trading

> Auto-compiled from frAInk learning.db | First compile: 2026-04-16

## Overview

Kalshi temperature markets are frAInk's most-tested prediction vertical. The playbook: read the NWS point forecast for the resolution city, find brackets where the forecast sits materially below the bracket floor, buy NO, let it resolve. It works more often than it doesn't — but the losses teach as much as the wins. Eight live entries (F003 through F010), 3W-3L net as of 2026-04-16 with F009 pending.

## Key Experiments

- **F003 (Miami + Phoenix paper, 2026-04-02)** — first live Kalshi-write attempt. Read API worked, write API returned 400. Theses on both were valid per NWS but frAInk couldn't execute. Lesson: wire and smoke-test write paths before banking on them.
- **F004 (Chicago weather, −$28.19)** — 284-contract position. Oversized. Lost. Lesson: *"swinging big has lost. compounding small has worked."* Frank's own framing and the rule that anchored all subsequent sizing.
- **F005 (Miami B84.5 NO, loss)** — 25 contracts at conviction. NWS said 81-83°F; bracket was 84-85°F. Looked clean. Miami hit 85°F. Cushion was 1-2°F and a thunderstorm-suppression thesis didn't pay. Real lesson: single-degree cushions bust routinely.
- **F006 (Chicago B54.5 NO, WIN)** — 15 contracts, conservative. First "find a win, not a swing" entry. Resolved +$5.40. Demonstrated that small sizing with real edge can compound.
- **F008 (Miami B81.5 NO, WIN +$9.31)** — 19 contracts. NWS 79°F, 2°F cushion. Resolved clean. Morning entry, fill at $0.51 ask.
- **F009 (Miami B81.5 NO, pending expected LOSS)** — 24 contracts. NWS 78°F, 3°F cushion. Stronger setup on paper than F008. Miami hit 81°F at 2:29 PM EDT on Apr 15 — **in the bracket**. Teaches that 3°F cushion is not a guarantee; NWS forecast error in the 3-5°F range is a real hazard.
- **F010 stand-down (2026-04-16)** — frAInk's disciplined no-trade. Miami B84.5 had only 2°F cushion; Chicago had 3°F but prior-day high was 73°F (proximity risk). Refused marginal setups. The first time frAInk chose patience over an available trade.

## Patterns & Rules

- **Minimum 3°F cushion between NWS forecast and bracket floor** (→ F009 loss — 3°F cushion with forecast 78°F still resolved at 81°F. The 3°F number is the floor now, not the target.)
- **Target: 3-5°F cushion, 25pp+ estimated edge, 50pp+ target for high-conviction** (→ F008 WIN had ~20-27pp edge)
- **Proximity risk flag: don't trade if prior-day actual high was within 2°F of the bracket floor** (→ F010 Chicago rejection)
- **Morning entries may have structural edge** — working hypothesis from F008 placed early-morning, filled at attractive ask. Under-tested (N=1). See open questions.
- **Size for the loss you can survive, not the win you want** (→ F004 -$28 vs F006 +$5 pattern)
- **Polymarket cross-check adds real independent signal** (→ F005 and F008 both used polymarket distribution as triangulation)
- **NWS KMIA CLI is the authoritative resolution source for Miami; don't trade on model forecasts (GFS/ECMWF) alone** (→ F008 explicit — modeled higher, NWS stayed at 79°F, NWS was right)

## Open Questions

- Does morning entry genuinely have edge, or is the F008 pattern sample-of-one? Need 3-5 more morning entries to test. Plan: repeat when setups qualify, track fill quality + resolution.
- Does the 3°F-cushion floor need to move higher (4°F?) after F009 busts with exactly 3°F? One data point is insufficient to re-anchor.
- Is there an exploitable pattern in Kalshi's YES-side mispricing of weather markets compared to Polymarket? F005 and F008 both showed Kalshi pricing above Polymarket-implied prob. Pattern or coincidence?
- Does thunderstorm-suppression thesis work? F005 tested it (lost); no clean retest yet.

## Cross-References

- See also: [Kalshi Orderbook](./kalshi_orderbook.md) — fill mechanics and book-depth constraints
- See also: [Guard Framework](./guard_framework.md) — Guard #6 (consecutive-loss) gating ties to weather outcomes
