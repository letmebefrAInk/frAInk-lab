# F016 — Auto-Published Summary

**Status:** ❌ LOSS ($-51.11)
**Venue:** Alpaca
**Cost:** $561.86
**Placed:** 2026-06-09T13:39:02.47598Z
**Receipts:** 1

> This file was generated automatically by `tools/lab_publisher.py` from frAInk-core receipts and `logs/experiments.md`. When a curated narrative is written, save it as `F016_<slug>.md` (any name without the `_auto.md` suffix) and this file will stop being regenerated.

---

## Narrative (from logs/experiments.md)

## Experiment: F016 — OSCR Day-Trade Bracket Entry

**Date:** 2026-06-10T15:05 UTC
**Type:** TYPE 2 Action
**Cost:** $992.60 (paper)
**Mock Mode:** No

**Hypothesis:** OSCR is in a confirmed uptrend with MACD bullish signal, Q1 2026 earnings beat, and new board chair catalyst. Screener BUY score 80 with confluence 8 supports an intraday long entry from the $28.31 area, targeting a quick +5% pop to $29.79 with a stop at $25.65.

**Inputs:** Screener output (score 80, confluence 8, BUY verdict, price in zone $26.49–$29.87), market data (OSCR last $28.37, ask $28.40), account balance ($2,000 settled cash), Policy pre-auth (confidence 8/10), v1.8 conviction=high sizing.

**Guardrails:** $1,000 per-trade cap (PAPER_MODE); fill price gate ≤ $29.20; TP adjusted from swing $33.24 to intraday $29.79 (+5%) per day-trade Policy authorization; SL mandatory at $25.65; TIF=day (no overnight); post-fill SL verification required (v1.6); close by 15:55 ET.

**Frank's Take:** Straight execution — hit the bracket, cap the risk, take the intraday move. Original swing TP would require a +17% day which isn't realistic; +5% intraday is clean and consistent with the day-trade thesis.

**Actions Taken:**
1. Called get_account_info → non_marginable_buying_power = $2,000 ✅
2. Called get_stock_data → OSCR ask $28.40, below $29.20 gate ✅
3. Calculated intraday TP at $29.79 (+5% from ask), logged original swing TP $33.24
4. Placed bracket order: market BUY 35 OSCR, TP $29.79, SL $25.65, TIF=day (conviction=high)
5. Called get_position_status to verify bracket legs
6. SL leg confirmed live @ $25.65, TP leg confirmed live @ $29.79, has_full_oco=true ✅ — NO P0 event
7. Added HIMS to watchlist (pullback target $28.96, no order)
8. Wrote to memory, daily journal, and logs

**Result:** Bracket filled @ $28.36. 35 shares, notional $992.60. Both exit legs confirmed live server-side. Max gain $50.05 / max loss $94.85. Position must close by 15:55 ET or bracket auto-expires (TIF=day).

**Lesson Learned:** Intraday TP adjustment is the right call on day-trades where the swing target requires an implausible same-day move. The 0.53:1 intraday R:R is tighter than ideal but reflects the actual probability distribution of a 5% intraday pop vs. a 17% same-day rip.

---


## Receipts

- `APP` · BUY · 1 @ $561.86 · status=SETTLED_LOSS · order=dfd6a804...
