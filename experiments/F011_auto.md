# F011 — Auto-Published Summary

**Status:** ✅ WIN (+$0.02)
**Venue:** Alpaca
**Cost:** $11.94
**Placed:** 2026-05-12T14:36:59.215283Z
**Receipts:** 1

> This file was generated automatically by `tools/lab_publisher.py` from frAInk-core receipts and `logs/experiments.md`. When a curated narrative is written, save it as `F011_<slug>.md` (any name without the `_auto.md` suffix) and this file will stop being regenerated.

---

## Narrative (from logs/experiments.md)

## Experiment: F011 — 7:27 Leaders Scanner Paper Entry #5 (META) + Paper Track #6 (ANET)

**Date:** 2026-04-17T12:31:40Z
**Type:** TYPE 2 Action (log-only paper entries — no Alpaca orders placed)
**Cost:** $0.00
**Mock Mode:** No

**Hypothesis:** META and ANET both surfaced as 7:27 leaders scanner candidates on Apr 15-16 with sufficient technical confluence to warrant logging as paper dataset entries. META shows a confirmed Fib 0.618 breakout with EMA-20/50 recapture and MACD bullish cross; ANET shows rs_leader signal at 86 confluence score. Logging both advances the scanner validation dataset (N=5→6 for META, N=6→7 track for ANET) toward the N=20 threshold needed for statistical validity.

**Inputs:**
- Screener output: META confluence score (Fib breakout, EMA-20/50 recaptured, MACD bullish cross, Apr 15 signal)
- Screener output: ANET rs_leader signal, confluence score 86, Apr 15-16 signal
- Hold window: Apr 17–28 (hard pre-earnings exit before META Apr 30 earnings)
- META price range at signal: $669–677; ANET price at signal: ~$160
- G6 state: CLEAR, consecutive_losses=1, record 3W-3L

**Guardrails:**
- NO Alpaca orders placed — these are pure log/dataset entries per Policy approval
- No financial commitment of any kind
- Pre-earnings exit hard-coded at Apr 28 (META earnings Apr 30 — never hold through earnings)
- ANET tagged research_depth=thin per Policy directive (scanner signal only, no fundamental catalyst identified)

**Frank's Take:** N/A — this is a building-block experiment for scanner validation. The methodology is the point, not any individual outcome.

**Actions Taken:**

*META Paper Entry — Dataset Entry #5:*
- Symbol: META
- Direction: LONG (paper only)
- Entry price: ~$669–677 (scanner signal range Apr 15)
- Stop: $639.00 (hard stop, ~5.5% below mid-entry)
- Target: $697.00 (~3% above mid-entry)
- Risk/Reward: ~1:0.55 (asymmetric toward caution given thin dataset)
- Hold window: Apr 17–28 (exit before Apr 30 earnings, no exceptions)
- Source: screener_assisted (7:27 leaders scan)
- Dataset_N: 5 (this is the 5th qualifying entry in scanner validation dataset)
- Paper_only: true
- Pre_earnings_exit: Apr 28
- Technical basis: Fib 0.618 breakout confirmed Apr 15, EMA-20/50 recaptured, MACD bullish cross on daily

*ANET Optional Paper Track — Dataset Entry #6:*
- Symbol: ANET
- Direction: LONG (paper only)
- Entry price: ~$160.00 (scanner signal range Apr 15-16)
- Stop: ~$152.00 (~5% below entry)
- Target: ~$164.80 (~3% above entry)
- Hold window: Apr 17–28
- Source: screener_assisted (7:27 leaders scan)
- Research_depth: THIN — scanner signal only, no fundamental catalyst identified this session
- Paper_only: true
- Pre_earnings_exit: Apr 28
- Technical basis: rs_leader flag, confluence score 86 — no additional corroboration found
- Note: Research quality is tagged thin. This matters for dataset scoring later — ANET's provenance is weaker than META. Weight accordingly when scoring N=20 results.

*Kalshi Weather — Stand-Down:*
- All Miami and Chicago temperature bracket candidates evaluated
- Best candidates failed carry-forward thresholds: 3°F cushion minimum (0°F for top picks) AND 25pp edge minimum (15-20pp actual vs. 25pp required)
- G6 state: CLEAR at consecutive_losses=1 — FLAG threshold at 2, one loss away
- Decision: Stand down. No predictions placed. Correct posture given G6 proximity to FLAG level.

**Result:**
- META paper entry logged as dataset entry #5. Scanner validation dataset advances: N=4→5.
- ANET paper track logged as dataset entry #6 (thin). N=5→6 (with provenance caveat).
- Kalshi stand-down enforced. No financial exposure.
- MU short flag escalated to Frank via email (separate action this session).
- G6 state: CLEAR, consecutive_losses=1, record 3W-3L — no G6 action taken.

**Lesson Learned:** The scanner dataset is being built the right way — log every qualifying signal with honest provenance tagging (thin vs. solid), enforce pre-earnings exits as a hard rule, and accumulate toward N=20 before drawing conclusions. ANET's thin tag is the correct call: adding a signal to the dataset doesn't mean treating it as a trade recommendation — it means being honest about what the signal quality actually was when it fires. That honesty is what makes N=20 worth something.

---

### 2026-04-17T13:11:41.596983+00:00


## Receipts

- `F` · BUY · 1 @ $11.94 · status=SETTLED_WIN · order=3107e2b5...
