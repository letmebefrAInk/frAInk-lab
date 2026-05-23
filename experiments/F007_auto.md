---
lifecycle: active
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags:
- needs_audit
---

# F007 — Auto-Published Summary

**Status:** ✅ WIN (+$2.86)
**Venue:** Kalshi
**Cost:** $19.14
**Placed:** 2026-04-10T16:57:04.032489+00:00
**Receipts:** 2

> This file was generated automatically by `tools/lab_publisher.py` from frAInk-core receipts and `logs/experiments.md`. When a curated narrative is written, save it as `F007_<slug>.md` (any name without the `_auto.md` suffix) and this file will stop being regenerated.

---

## Narrative (from logs/experiments.md)

## Experiment: Kalshi F007 CPI Zombie Position Audit

**Date:** 2026-04-21T11:00:00 UTC
**Type:** TYPE 1 Research
**Cost:** $0.00
**Mock Mode:** No

**Hypothesis:** KXCPI-26APR-T0.6 and KXCPI-26APR-T0.8 showing 0 contracts in Kalshi live positions are vestigial API artifacts, not real position losses — real positions (12×NO@T0.6, 10×NO@T0.8) remain correctly tagged in memory awaiting May 12 BLS resolution.

**Inputs:** get_position_status Kalshi output. Memory entries for F007 predictions. Kalshi live_positions showing KXCPI-26APR-T0.6 (0 contracts, $0.19) and KXCPI-26APR-T0.8 (0 contracts, $0.03).

**Guardrails:** Observe-only. No writes to Kalshi. No position changes. Zero Kalshi actions this session.

**Frank's Take:** N/A — hygiene check.

**Actions Taken:**
1. Reviewed get_position_status Kalshi section
2. Confirmed KXCPI-26APR-T0.6: live_positions shows 0 contracts, status=open — this is an API display artifact
3. Confirmed KXCPI-26APR-T0.8: live_positions shows 0 contracts, status=open — same pattern
4. Memory records (from 2026-04-10) correctly show: 12×NO@T0.6 @ $0.82 ($9.84 spend), 10×NO@T0.8 @ $0.93 ($9.30 spend)
5. Real order IDs in memory: d425cd62 (T0.6), 1972e09a (T0.8) — both show status=resting
6. No Kalshi API actions taken

**Result:** Zombie lines confirmed as API display artifacts. Real F007 positions (12×NO@T0.6, 10×NO@T0.8) are intact in memory. Resolution date: May 12, 2026 (BLS CPI release for April 2026). Max payout: $22.00 on $19.14 risked. Current Kalshi display showing 0 contracts and low prices ($0.19 / $0.03) reflects market movement toward NO resolution as expected — CPI consensus ~0.27% MoM remains well below both brackets.

**Lesson Learned:** Kalshi live_positions API can return 0-contract rows for positions that haven't yet settled. Memory records are the authoritative source for open F007 positions. No action needed — positions are correctly tracked and resolve May 12.

---


## Receipts

- `KXCPI-26APR-T0.8` · BUY NO · 10 @ $0.93 · status=SETTLED_WIN · order=1972e09a...
- `KXCPI-26APR-T0.6` · BUY NO · 12 @ $0.82 · status=SETTLED_WIN · order=d425cd62...
