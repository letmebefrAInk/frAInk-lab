# F002 — First Full Paper Trading Day

**Date:** 2026-03-30
**Type:** TYPE 2 — Paper Trading (Full Day: Morning + Midday + EOD)
**Cost:** $0.00 (paper mode)
**Mock Mode:** No — real Alpaca paper API + Kalshi Demo API

---

## Hypothesis

A structured three-pass trading day (morning research, midday confirmation, EOD review) with universe scanning, bracket orders, and prediction market integration will produce actionable signals and meaningful paper trades for backtesting.

## Inputs

- S&P 500 universe scan (500 stocks screened)
- Crypto top 50 scan
- Kalshi weather and economics markets
- NWS forecast data (Phoenix CF6, Miami PFM)
- Macro context: SPY, QQQ, VIX, Crypto Fear & Greed
- PRE_AUTH_TRADING.md v1.3 (paper limits: $1,000/trade, $10,000/day, 8 positions)
- All new tooling: position monitor, bracket orders, universe scanner, watchlist self-management

## Guardrails

- Paper mode: $1,000 max per stock trade, $10,000 daily cap, 8 positions max
- Kalshi: $5 max per prediction, 3 open max, $15 total exposure
- Score threshold: 60 (lowered from normal for data generation)
- Approved Kalshi categories: weather, economics, financials, crypto

## Frank's Take

First real stress test of the full pipeline. Every tool running together for the first time. Expected bugs — found them. The goal was data and execution practice, not P&L.

---

## Actions Taken

### Morning Run
1. Universe scan found 10 buy_signal=True candidates from S&P 500 (AKAM 69, DELL 67, FOXA 67, HPE 66, ALB 64, TSN 62, CIEN 61)
2. Placed 5 GTC limit buy orders: AKAM 4@$114.50, DELL 3@$166, ALB 3@$179, TSN 8@$63, CIEN 1@$398
3. Total pending exposure: ~$2,395
4. Added 6 stocks to watchlist (AKAM, DELL, ALB, TSN, CIEN, VRSN)
5. Macro: SPY -0.5%, QQQ -1%, VIX 30.52, Crypto F&G 8-13 (Extreme Fear)

### Midday Run — Stocks
1. 3 of 5 orders filled: AKAM, ALB, CIEN
2. CIEN immediately stopped out at $370 (-$25.25, -6.4%)
3. Exit orders placed on AKAM (SL $108 / TP $125) and ALB (SL $167 / TP $197)
4. DELL and TSN still pending

### Midday Run — Crypto + Kalshi
1. Crypto still RISK_OFF. ONDO-USD top watch at 53 (down from 70). No crypto trades.
2. Kalshi: Phoenix predictions pending resolution. LAX weather markets efficiently priced, no edge found.

### Kalshi Demo API Predictions
1. Wired paper mode to submit real orders to Kalshi Demo API (first time)
2. Placed 2 Miami weather predictions:
   - KXHIGHMIA-26MAR31-B79.5 YES 5@$0.25 — NWS forecasts 80°F, market underpricing (order resting)
   - KXHIGHMIA-26MAR31-B83.5 NO 5@$0.81 — 83-84°F bracket 3°F above forecast (order resting)
3. Both orders visible on Kalshi demo dashboard

### EOD Review
1. DELL identified as orphaned (no exit orders) — F001 violation, SL placed at $154
2. TP order rejected by Alpaca (cash accounts can't hold two competing sells — OCO needed)
3. Phoenix weather resolved: B95.5 NO won (+$1.00), B93.5 YES lost (-$0.85). Net: +$0.15
4. Watchlist cleaned: removed UBER, AVAX-USD, SUI-USD, XRP-USD, INJ-USD (all below threshold)

---

## Result

### Day 1 P&L Summary
| Category | Exposure | Realized P&L | Unrealized P&L |
|----------|----------|--------------|----------------|
| Alpaca Stocks | ~$494 (DELL) | -$25.25 (CIEN) | -$3.87 (DELL) |
| Kalshi Weather (Phoenix, resolved) | $2.85 | +$0.15 | — |
| Kalshi Weather (Miami, pending) | $5.30 | — | TBD |
| Kalshi CPI (long-dated) | $1.90 | — | TBD |
| **Total** | **~$504** | **-$25.10** | **-$3.87** |

Account value: $99,781.31

### Bugs Found & Fixed (3)
1. **PRE_AUTH_TRADING.md paper limits** — $10/trade cap made paper trades meaningless. Fixed: $1,000/trade, $10,000/day for paper.
2. **Policy allowed_tools gaps** — `place_bracket_order`, `add_to_watchlist`, `get_position_status` missing from templates. Fixed.
3. **Kalshi Demo API pricing** — Demo expects cents (int), not dollars (float). Fixed `create_order()` to convert.

### Infrastructure Gaps Discovered
1. **F001 (orphaned positions)** — occurred twice. Exit order placement needs automation.
2. **Alpaca OCO limitation** — cash accounts can't hold two competing sell orders. Need cancel-and-replace workaround.
3. **Kalshi trades endpoint** — 404 on demo. Can't view trade history.

---

## Lessons Learned

1. **ATR-based stops needed for high-beta names.** CIEN's fixed $370 stop was too tight for a stock with 5-7% daily swings. Stopped out then bounced.
2. **Paired weather predictions limit downside.** Phoenix pair netted +$0.15 despite one loss. The structure works.
3. **GTC limit entries work for pullback strategies.** AKAM filled exactly at the planned $114.50 entry.
4. **Exit order automation is the #1 operational priority.** Manual processes break under load.
5. **Weather forecast precision at 1°F is noise.** Need ≥2°F bracket targeting, not single-bracket precision plays.
6. **The screener's protective function works.** Zero false candidate_buy signals in a hostile environment.

---

*frAInk's first full trading day. Bugs found, lessons logged, data generated. The pipeline works.*
