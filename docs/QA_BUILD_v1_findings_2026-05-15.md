# QA Build v1 — Findings

**Build:** Confluence Scanner QA Build v1 + v1.1 M1
**Shipped:** 2026-05-15 (one calendar day across 4 fresh-terminal sessions)
**Status:** v1 closed · v1.1 M1 closed · v1.1 M2 queued · 7d-22d harness re-run scheduled ~2026-05-22

---

## What this build is for

The Confluence Scanner produces BUY / WATCH / DON'T BUY verdicts on individual tickers. Before we deploy meaningful capital against those verdicts — or pay for Alpaca SIP feed at $99/mo — we want statistical evidence that the scanner's gates actually predict TP-hit. That's what the QA Build is: a validation loop with formal pass/fail gates on top of a trajectory schema that captures what each historical signal *did* across 7 horizons (2d / 3d / 5d / 7d / 10d / 15d / 22d).

It is not a buy-signal generator. The scanner itself is the buy-signal generator. The QA loop answers *"should you trust those signals"* and (via v1.1, in progress) *"is this candidate strong vs. weak, fast vs. multi-day."*

---

## What shipped in v1

| Step | Module | Output |
|---|---|---|
| 1 | Trajectory schema migration + QA-A decision harness | 23 new columns on `outcome_log.csv`; 3 Fisher-exact gates |
| 2 | Breakout-formation typology | 5-bucket classifier (sudden / playout_fast / playout_slow / failed / slow_grind) + 5 cross-tabs |
| 3 | Premarket integration via Finnhub free | New typed factor `factor_premarket_gap` in new `intraday_setup` family; Taxonomy v1.2 |
| 4 | UI extension on the local scanner page | Timeframe perspective dropdown (Swing 2-10d / Short-term 2-4w / View all) + new `/qa` dashboard with 5 panels + cross-link from each BUY card to its prior outcome trajectory |
| 5 | Close-out | This doc + memory + state files |

**v1.1 M1** (also 5/15): the `/qa` page got an intent banner explaining what it's for and isn't for, plus per-panel help text and a verdict-label decoder, after a walkthrough surfaced that the page was hard to interpret without referencing the spec.

---

## First real verdicts at the 3d horizon

The first harness run with mature data hit the 3-day window (the only horizon with enough N as of 5/15). Three formal gates, three actionable verdicts:

**1. Volume gate — PASS_KEEP** *(p=0.016, gap=+13.7pp)*
At 3d, BUYs with `factor_volume=very_strong` hit TP **26%** of the time (N=50). BUYs with `factor_volume=light` hit TP **12.3%** of the time (N=253). That's a 13.7-percentage-point gap at p<0.05 — the first statistically-significant signal the QA framework has produced.
**Action:** tighten the volume gate. Add `factor_volume=light` to confluence-floor demote consideration. (Tuning ship lands once 7d confirms or contradicts.)

**2. Confluence floor T — INCONCLUSIVE** *(p=0.107, gap=-20.4pp)*
At 3d, demoted BUYs (N=9) hit TP **33.3%** of the time. Non-demoted BUYs (N=355) hit TP **13.0%**. So demoted is *outperforming* non-demoted by 20 percentage points — counterintuitive, and underpowered (p=0.107). Watch as the demoted N grows. If the gap holds at p<0.05, the floor needs rethinking.

**3. Persistent BUYs — KILL** *(p=0.348, gap=-4.3pp)*
First-day-fire BUYs hit TP 12.5% (N=287). Second-day-and-after fires hit TP 16.9% (N=77). The hypothesis was that persistent fires would degrade — they don't, at this horizon. Annotate-only, no gate.

**Caveat:** these are 3d verdicts. The spec-default horizon is 7d, which is still all-AWAITING (the 5/8 cohort matures to 7 trading days around 2026-05-22). If 7d contradicts 3d, we revisit. If 7d confirms, the volume-gate tightening locks in.

---

## What we learned that wasn't in the spec

**Breakout typology at confluence=7 is not a guarantee.** Among matured trades with `confluence_factor_count=7`, the cross-tab shows 15 `sudden_breakout` vs 39 `failed_breakout` outcomes. The floor-demote work was real (fewer trades get to N=7+), but high confluence alone doesn't predict success. This is the kind of finding that calibrates the per-trade expectation.

**The dash needed a purpose statement.** Until v1.1 M1, the `/qa` page presented gate verdicts and cross-tabs without context. A fresh reader couldn't tell whether to look at it for buy candidates (no), strength validation (no — that's coming in M2), or *"should we trust the scanner"* (yes). The intent banner now resolves that on the page itself.

**The scanner needs a per-candidate snowflake, not just aggregate verdicts.** Originally the QA loop was framed as meta-only — *"are the gates predictive."* During acceptance review the framing shifted: the loop should also answer *"is this current BUY candidate strong vs. weak, fast vs. multi-day, relative to historical setups with the same factor profile."* That's v1.1 M2, queued next.

---

## What's next

- **v1.1 M2 — Snowflake candidate view** (~4-6h, next session). For each scanner BUY, lookup historical setups with matching factor profile (confluence + volume tier + RS state + sector) and surface a two-axis verdict: **Strength** (STRONG / MODERATE / WEAK / INSUFFICIENT) × **Duration** (FAST ≤3d / MULTI-DAY 4-22d / AWAITING). Inline chip on every BUY card on the live scanner page + detail panel on the QA page when filtered to a ticker.
- **v1.1 CG — Calendar gate** (passive, ~2026-05-22). Re-run the harness at 7d / 10d / 15d / 22d once those horizons have mature data. If verdicts contradict 3d, log the divergence here.
- **v1.2 — Quant model fit** (~5-8h, on the calendar ~2026-05-22+). Replaces v1.1 M2's deterministic lookup with a calibrated `P(TP by horizon)` model + per-candidate typology prediction.
- **True premarket signal validation.** The Finnhub adapter shipped 5/15 and live-smoked against the real API during RTH (which verified plumbing, not signal). First real premarket-window scan during 04:00–09:30 ET will start populating the `factor_premarket_gap_value` column and the premarket cross-tab.

---

## Honest framing

After 1-2 weeks of mature 7d-22d data plus the v1.1 M2 snowflake build, this loop should answer two questions concretely:

1. **Is the scanner's edge real enough to deploy meaningful capital?** If yes → SIP feed upgrade unlocks. If no → trading lane gets reduced priority, content production (Phase 18b) takes precedence.
2. **Which BUYs deserve to be on the radar today, and what's the likely move shape?** This is what v1.1 M2 delivers on the live scanner page.

If after that the dashboard still doesn't drive a decision, the honest output is *"the edge isn't there in this regime"* and we redirect time to other lanes. That's a valid outcome of a validation loop. We'll know in roughly two weeks.

---

*Build artifacts: `frAInk/docs/strategy/CONFLUENCE_QA_BUILD_v1.md` (spec + §14 v1.1 extension). Snapshots: `frAInk/exports/qa_decision_2026-05-15.json`, `frAInk/exports/breakout_typology_2026-05-15.json`, `frAInk/exports/outcome_log.csv` (599 rows / 64 cols + 3 more landing on next harvest).*
