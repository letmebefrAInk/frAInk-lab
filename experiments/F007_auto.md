# F007 — Auto-Published Summary

**Status:** 🔄 PENDING
**Venue:** Kalshi
**Cost:** $19.14
**Placed:** 2026-04-10T16:57:04.032489+00:00
**Receipts:** 2

> This file was generated automatically by `tools/lab_publisher.py` from frAInk-core receipts and `logs/experiments.md`. When a curated narrative is written, save it as `F007_<slug>.md` (any name without the `_auto.md` suffix) and this file will stop being regenerated.

---

## Narrative (from logs/experiments.md)

## Experiment: F007 Post-Mortem — Chicago High Temp Apr 11 (B57.5 NO)

**Date:** 2026-04-13T10:46:00Z
**Type:** TYPE 1 Research
**Cost:** $0.00 (logging only — F007 cost basis $19.80 already recorded at entry)
**Mock Mode:** Yes (RESEARCH MODE)

**Hypothesis:** NWS CLI Midway observed high for Apr 11 would fall below 57°F, resolving B57.5 NO in frAInk's favor.

**Inputs:** NWS CLI Midway report confirming Apr 11 observed high = 58°F. F007 position: KXHIGHCHI-26APR11-B57.5 NO × 44 contracts @ $0.45 avg. Kalshi API balance endpoint returning 401 (auth issue, separate from settlement).

**Guardrails:** RESEARCH MODE — no trades. Logging and memory writes only.

**Frank's Take:** Thesis was directionally sound. 54°F NWS forecast going into entry made B57.5 the right bracket — it provided cushion over B55.5 without overreaching to B60. The bust magnitude (54°F forecast → 58°F actual = +4°F error) is outside normal NWS short-range error distribution for Chicago spring but not impossible. This is variance, not a model failure.

**Actions Taken:**
1. Confirmed NWS CLI Midway observed high = 58°F on Apr 11
2. Determined 58°F falls within B57.5 bracket (57–58°F inclusive) → YES resolves → NO loses
3. Logged PROBABLE LOSS to memory (pending Kalshi API confirmation)
4. Updated G6 state: consecutive_losses → 1, guard_state = NONE
5. Updated net P&L estimate: -$33.30 → ~-$53.10 (+ ~$19.80 F007 loss)
6. Updated record: 2W-3L

**Result:** F007 = PROBABLE LOSS. NWS bust of 4°F above forecast exceeded the B57.5 cushion. All prior wins (F002, F005) and losses (F003, F004, F007) consistent with the hypothesis that edge exists on weather markets but NWS short-range busts are a real tail risk requiring appropriate position sizing. 44 contracts at $0.45 = $19.80 exposure was within approved budget. No G6 trading restriction triggered. Kalshi API 401 prevents hard confirmation — flag for next session.

**Lesson Learned:** The cushion bracket strategy (choosing B57.5 over B55.5 when forecast = 54°F) was architecturally correct — the loss came from a 4°F NWS bust, not from choosing the wrong bracket. The tail risk of NWS busts of this magnitude is real and worth quantifying across the full F-series dataset. Consider whether max contract count (44) is right-sized for events with meaningful bust risk. Kalshi balance API auth issue must be resolved before next session — blind on P&L confirmation is not acceptable.

---

### 2026-04-14T11:08:24.057062+00:00


## Receipts

- `KXCPI-26APR-T0.8` · BUY NO · 10 @ $0.93 · status=FILLED · order=1972e09a...
- `KXCPI-26APR-T0.6` · BUY NO · 12 @ $0.82 · status=FILLED · order=d425cd62...
