---
lifecycle: archived
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags: []
---

# F010 — Auto-Published Summary

**Status:** ✅ WIN (+$11.37)
**Venue:** Alpaca
**Cost:** $47.48
**Placed:** 2026-05-08T13:37:10.965783Z
**Receipts:** 1

> This file was generated automatically by `tools/lab_publisher.py` from frAInk-core receipts and `logs/experiments.md`. When a curated narrative is written, save it as `F010_<slug>.md` (any name without the `_auto.md` suffix) and this file will stop being regenerated.

---

## Narrative (from logs/experiments.md)

## Experiment: F010 Stand-Down — Morning Weather Hypothesis Discipline Check

**Date:** 2026-04-16T11:07:38Z
**Type:** TYPE 1 Research
**Cost:** $0.00
**Mock Mode:** No

**Hypothesis:** Five Kalshi weather candidates were evaluated against the morning-weather hypothesis signature profile (3°F+ cushion, 25pp+ edge minimum, active suppression preferred). The hypothesis is that disciplined rejection of marginal setups — even with some edge — produces better long-run outcomes than forcing volume.

**Inputs:** NWS forecasts, morning observations, historical CF6 data, Polymarket cross-reference, cushion and edge calculations for all five candidates. F008 and F009 settlement context from prior runs.

**Guardrails:** No trades proposed or placed. Logging and memory only. $0 budget.

**Frank's Take:** "Discipline > volume" — after a likely F009 loss, the right move is to wait for setups that actually match the profile rather than chase marginal plays to make back losses.

**Actions Taken:**

1. **F008 SETTLED_WIN confirmed.** F008 resolved as a win. Adds to the experiment ledger as 3W going into today.

2. **F009 — Expected LOSS, resolution pending at 14:00 UTC.** F009 is flagged as a likely loss based on current conditions. Resolution expected at 14:00 UTC. Informational value: contributes to G6 state data and pattern learning on where the thesis underperformed. Ledger will update to 3W-3L on confirmation.

3. **F010 candidate evaluations — both rejected:**

   - **Miami High ≥ 84.5°F (NO side):** NWS forecast 83°F. Cushion = ~2°F below the 84.5°F threshold. That's a 2°F cushion on the NO — below the 3°F minimum bar. Edge estimated at 12–19pp. Rejected: cushion too thin, edge below 25pp threshold. One degree of forecast error flips the outcome.

   - **Chicago High ≥ 74.5°F (NO side):** Cushion met the 3°F bar on paper. However, proximity risk flagged: yesterday's observation was 73°F, only 1.5°F below threshold. That kind of proximity creates reversion risk on a warm day. ROI calculated at ~27% — marginal against the 25pp+ minimum, but not decisively above it. Cross-referencing Polymarket showed low market conviction. Rejected: proximity risk + marginal ROI disqualify it from the signature profile.

4. **Refined hypothesis thresholds logged:**
   - Minimum cushion: **3°F+** between forecast high and threshold
   - Minimum edge: **25pp+** implied probability edge
   - Target profile: **50pp+ edge, 3–5°F cushion, active suppression pattern**
   - Proximity risk flag: if yesterday's observed high was within 2°F of today's threshold, treat setup as degraded regardless of cushion

5. **Updated Kalshi P&L tracking:**
   - Pre-F009 resolution: 3W-2L
   - Post-F009 (expected): **3W-3L, ~-$33.83 net**
   - F009 loss, if confirmed, is expected and within experiment tolerance — not a panic trigger

**Result:** No trades placed. Stand-down decision logged. Both candidates evaluated rigorously and rejected on quantifiable grounds (cushion below threshold, proximity risk, marginal ROI). This is the hypothesis working as intended — the filter is doing its job. F009 resolution to be checked after 14:00 UTC in the next pipeline run.

**Lesson Learned:** A hypothesis that only generates signal when conditions are genuinely favorable is more valuable than one that forces trades to stay active. The Miami rejection (2°F cushion vs. 3°F minimum) and Chicago rejection (proximity risk + marginal ROI) represent the decision boundary being enforced correctly. The refined thresholds — 3°F+ cushion, 25pp+ edge, 50pp+ target — now have concrete rejected examples to anchor them. Discipline after a loss is where the real test of a system happens.

---

### 2026-04-16T11:57:41.781003+00:00


## Receipts

- `IONQ` · BUY · 1 @ $47.48 · status=SETTLED_WIN · order=80a99646...
