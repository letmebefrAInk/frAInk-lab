# QA Build v1 — post Scanner Verdict Stability re-run

**Date:** 2026-05-21 PM
**Companion to:** `QA_BUILD_v1_findings_2026-05-15.md` (5/15 PM C-refresh snapshot)
**Spec ship:** [`SCANNER_VERDICT_STABILITY_v1.md`](../../Codex%20Builds/Confluence%20Screener%20V1/docs/strategy/SCANNER_VERDICT_STABILITY_v1.md)
**Status:** Verification per spec §3.7 + §4.5 — math change re-run, diff documented, **no auto-action** until 7d cohort matures 5/22+.

---

## Short version

**Gate verdicts did NOT shift from the Scanner Verdict Stability ship.**

#1 horizon-aware bar weighting was implemented as **contract annotation only** (Frank-acked option A in mid-build re-gate): each verdict now carries `bars_used` + `stability_layer_enabled` fields, but the synthesis math (composite computation, similar-setups lookup, hard-fail detection, weight renormalization) is unchanged. The actual volatility reduction this ship delivers lands at the hysteresis / stability-band / UI-grouping layers, NOT at the synthesis layer that powers Gate 1 / Gate 2 / Gate 3.

Therefore the 5/15 PM gate findings remain valid:
- Gate 1 (Confluence floor T): INCONCLUSIVE — keep tightening threshold under review
- Gate 2 (Volume): PASS_KEEP — keep the very_strong floor
- Gate 3 (Persistent BUYs): TBD pending N≥30

The next legitimate gate re-run is the **C-refresh at N≥100 due 2026-05-25** (already at N=81 on 5/14 per memory). That refresh will reflect newer outcome data, NOT the contract-annotation ship.

---

## Why the math didn't change

Spec §3.1 phrased the #1 horizon-aware bar weighting fix as "STANDARD SWING / EXTENDED SWING / INVESTMENT verdicts consume prior-EOD bars + premarket only ... Implemented at the verdict-synthesis layer."

Two readings were possible:

- **(A) Contract annotation** — `HORIZON_BAR_PROFILE` constant + `bars_used` stamp; volatility reduction comes from layers #2 (hysteresis) + #3 (stability band) + #5 (UI grouping). Cheap (~30 min implementation). Frank-acked at mid-build re-gate 2026-05-21 AM.

- **(B) Synthesis-layer factor filtering** — drop intraday-volatile factor slots from positional weight sets + renormalize. Would actually change composite math; would have flipped Gate 1 and Gate 2 verdicts unpredictably. **Rejected** because it strips STANDARD's weight set down to ~2 factors (similar_setups + fundamentals), losing too much signal.

Option (A) was Stan's conviction recommendation and Frank's ack — see mid-build HITL prompt + answer in this terminal's transcript. The spec is now read as: contract annotation is the v1 implementation; deeper pipeline-level fix (skip current-day partial bar for positional factor compute) is a follow-up if intraday flutter still bothers Frank after live-running this stack.

---

## What did change (relevant to future QA gate cohorts)

The hysteresis layer (`confluence_screener/verdict_hysteresis.py`) holds smoothed verdicts in-memory per-(ticker, horizon). The persisted `verdict_transitions` table records every flip with `prev_verdict / new_verdict / ticks_since_prev`. This is data that future QA gates COULD consume — e.g., a new gate "did the smoothed verdict outperform the raw verdict at 7d horizon?" — but no such gate exists in v1 and Stan didn't manufacture one (drift fence §7 — no new gates without spec).

Future v1.1+ gate candidate worth surfacing in next QA conversation:
- **Smoothed-vs-raw lift gate** — compare 7d TP rate of (smoothed BUY NOW) vs (raw BUY NOW that flickered through WATCH-then-BUY) under the new N=3 hysteresis. If smoothing meaningfully under- or over-performs, the threshold N is tunable.

That's a real-cohort proposal, not a v1 ship.

---

## Local harness run (this terminal, dev env)

```
==============================================================================
  Confluence Scanner QA — C-refresh Decision Harness (horizon=7d)
==============================================================================

Gate: confluence_floor_t      → INSUFFICIENT_DATA (N=0)
Gate: volume_gate              → INSUFFICIENT_DATA (N=0)
Gate: persistent_buys          → INSUFFICIENT_DATA (N=0)
```

INSUFFICIENT_DATA across all three gates reflects this dev env's empty `outcome_log` + `scan_history` — not a regression. Production env (where the 599-row outcome_log lives) will reproduce 5/15 PM verdicts because synthesis math is unchanged.

---

## Carry-forwards

1. **C-refresh at N≥100** — proceed on calendar 2026-05-25 (per CS V1 BUILD_STATE "What's next" #3). Re-run the harness against the matured cohort; that's the legitimate verdict-shift moment.
2. **Smoothed-vs-raw lift candidate** — capture in next scanner-tuning conversation; not a v1 ship.
3. **Deeper pipeline-level bar-source fix** — only if intraday flutter persists after Frank live-runs this stack for ~1 week. Stan recommendation per close-out: revisit if `verdict_transitions` shows >2 flips/day on positional horizons.
