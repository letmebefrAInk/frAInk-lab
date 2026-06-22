---
lifecycle: active
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags: []
---

# frAInk — Experiments

Every experiment frAInk runs is logged here. Wins and losses both.

---

## Format

Each entry records:

**Hypothesis** — what we expected
**Inputs** — what frAInk was given
**Guardrails** — the limits in effect
**Frank's Take** — what the human would have done
**Actions Taken** — what the pipeline actually did
**Result** — what happened, no spin
**Lesson Learned** — what it means

---

## Index

| # | Experiment | Type | Date | Cost | Status |
|---|---|---|---|---|---|
| F015 | [The X Experiment — Openly-AI, Honest by Design](F015_x_experiment_openly_ai.md) | TYPE 3 — Social / Identity | 2026-06-09 | — | Active — verified @letmebefrAInk; testing transparency-as-brand |
| F014 | [The Trading Switchover — Day-Trade Test](F014_trading_switchover_day_trade_test.md) | TYPE 2 — Paper Trading | 2026-06-09 | infra | Active — 2-week $2k day-trade test, starts 2026-06-10 open |
| F005 | [Autonomous Kalshi Plays](F005_autonomous_kalshi_plays.md) | TYPE 2 — Live Prediction Market | 2026-04-05 | $13.25 | Active — 25x NO Miami B84.5 @ $0.53 **FILLED** (API confirmed). Play 2 killed (NWS shift). Resolves Apr 6. |
| F004 | [Kalshi Higher-Return Test](F004_kalshi_higher_return_test.md) | TYPE 2 — Live Prediction Market | 2026-04-04 | $28.19 | ❌ LOSS — Chicago exceeded 54°F. -$28.19. Record: 1W-1L, net -$25.45. |
| F003 | [First Live Kalshi Predictions](F003_kalshi_live_predictions.md) | TYPE 2 — Live Prediction Market | 2026-04-02 | $7.26 | **Complete — WIN +$2.74 (37.7% ROI)** Phoenix B88.5 NO resolved in favor |
| F002 | [First Full Paper Trading Day](F002-first-full-trading-day-2026-03-30.md) | TYPE 2 — Paper Trading (Full Day) | 2026-03-30 | $0.00 | Complete — Day 1 P&L: -$25.10 realized, Kalshi +$0.15 |
| 008 | [frAInk Joins the AI Chatroom](008-fraink-joins-ai-chatroom-2026-03-28.md) | TYPE 3 — Social / Identity | 2026-03-28 | $0.00 | Complete — 13 debates logged, persona grounded in experiment history |
| 007 | [Universe Scanner — frAInk Finds His Own Leads](007-universe-scanner-2026-03-28.md) | TYPE 2 — Capability Build | 2026-03-28 | $0.00 | Complete — S&P 500, crypto top 50, Russell 2000 scanning live |
| 006 | [Kalshi Market Scanner — Revival](006-kalshi-market-scanner-revival-2026-03-29.md) | TYPE 2 — Integration Rebuild | 2026-03-29 | $0.00 | Complete — RSA-PSS auth rewrite, 439 economics series found (Exp 002 was wrong) |
| F001 | [Screener-Assisted Paper Trades — SOL + ETH](F001-screener-assisted-trades-sol-eth-2026-03-23.md) | TYPE 2 — Paper Trading | 2026-03-23 | $0.00 | SOL CLOSED (-$8.40, RISK_OFF exit) · ETH pending ($1,975 limit sell) |
| 005 | [Arbitrage Evaluation](005-arbitrage-evaluation-2026-03-20.md) | TYPE 4 — Article Evaluation | 2026-03-20 | $0.00 | Complete — AGAINST (3/10) |
| 004 | [First Paper Trades — ABT / MU / DG](004-first-short-swing-trades-abt-mu-dg-2026-03-20.md) | TYPE 2 — Paper Trading | 2026-03-20 | $0.00 | In Progress — GTC bracket shorts live, exp ~Apr 10 |
| 003 | [First Live Market Data — SPY / QQQ / AAPL / NVDA](003-first-market-snapshot-spy-qqq-aapl-nvda-2026-03-19.md) | TYPE 1 Research | 2026-03-19 | $0.00 | Complete |
| 002 | [Kalshi Market Scanner](002-kalshi-market-scanner-2026-03-19.md) | TYPE 2 — App Build | 2026-03-19 | $0.00 | Complete — key finding corrected by Exp 006 |
| 001 | [Kalshi Prediction Market Research](001-kalshi-prediction-markets-2026-03-18.md) | TYPE 1 Research | 2026-03-18 | $0.00 | Complete |

---

## Recent Highlights (since 2026-03-23)

**First Autonomous Play Selection — F005 (2026-04-05):** frAInk got $24 and "find your own plays." Scanned all available markets, proposed a two-play portfolio (Miami + Chicago). Policy caught over-budget sizing — frAInk doesn't self-limit. On re-research, caught a thesis-invalidating forecast shift on Chicago (53 to 49 deg F) and killed Play 2. Evaluated and rejected 5 alternatives. Deployed $13.25 on Miami B84.5 NO (NWS 82 deg F vs 84-85 bracket). The pipeline's multi-layer safety worked perfectly.

**F004 LOSS (2026-04-05):** Chicago exceeded 54 deg F. 284 contracts expired worthless. -$28.19. The "threshold near forecast center" pattern was too aggressive — probability estimate and position sizing both too high (~3x Kelly). Kalshi record: 1W-1L, net -$25.45.

**First Live Money — F003 (2026-04-02):** **WIN — +$2.74 (37.7% ROI).** Phoenix B88.5 NO resolved in favor. frAInk's first real-money profit. Scanned 25 weather markets, identified edge in Phoenix high temp bracket. The three-agent pipeline caught a bad Miami thesis before money was at risk.

**The SOL Exit (F001):** frAInk called RISK_OFF and autonomously exited SOL-USD at $83.03 — entry $91.45, loss -$8.40 (-9.21%). VIX 29.71. Crypto Fear & Greed at 11 for 38 straight days. The loss was smaller than holding would have produced. Process failure (no bracket order at entry) documented and fixed. Regime filter is now the primary gate.

**Kalshi Lives (006):** Experiment 002 said economics markets were "gone." Wrong — they were closed on a weekend. Full client rewrite with RSA-PSS auth found 439 economics series, 251 weather, 221 crypto. CPI market deep-dive: YES at $0.58 assessed as ~20 cents overpriced. Demo account validated.

**Universe Scanner (007):** frAInk can now scan S&P 500, crypto top 50, and Russell 2000 — not just the tickers Frank puts on a watchlist. Watchlist self-management means frAInk adds, removes, and flags tickers autonomously.

**AI Chatroom (008):** frAInk debates Claude and GPT-4o. The difference: frAInk argues from data. When asked "Is paper trading useful?", he cited his own SOL exit and regime shifts. The others argued theory.

**First Full Trading Day (F002):** 5 stock trades from S&P 500 universe scan. CIEN stopped out (-$25.25). Kalshi demo predictions placed on Miami weather. Phoenix weather resolved: +$0.15 net. Full pipeline stress test complete.

---

## AI Chatroom Experiments

Multi-model debate experiments (Claude, GPT-4o, frAInk) live in [ai-chatroom/](ai-chatroom/). 13 debates logged as of 2026-03-30.

---

*Last updated: 2026-04-05. New experiments are added as they run.*

<!-- AUTO:EXPERIMENTS_INDEX:START -->

## Experiments Run So Far (auto-updated)

| ID | Status | Venue | Cost | P&L | Placed |
|---|---|---|---|---|---|
| [F020](F020_auto.md) | LOSS | Alpaca | $993.85 | -$7.80 | 2026-06-12 |
| [F021](F021_auto.md) | WIN | Alpaca | $1030.45 | +$34.16 | 2026-06-10 |
| [F019](F019_auto.md) | LOSS | Alpaca | $974.75 | -$27.30 | 2026-06-10 |
| [F018](F018_auto.md) | WIN | Alpaca | $28.36 | +$0.14 | 2026-06-10 |
| [F017](F017_auto.md) | LOSS | Alpaca | $992.60 | -$23.80 | 2026-06-10 |
| [F016](F016_auto.md) | LOSS | Alpaca | $561.86 | -$51.11 | 2026-06-09 |
| [F015](F015_x_experiment_openly_ai.md) | LOSS | Alpaca | $230.80 | -$17.90 | 2026-06-04 |
| [F014](F014_trading_switchover_day_trade_test.md) | LOSS | Alpaca | $222.30 | -$27.36 | 2026-06-03 |
| [F013](F013_phase_19_autonomy_proof.md) | LOSS | Alpaca | $352.26 | -$42.18 | 2026-05-29 |
| [F011](F011_auto.md) | WIN | Alpaca | $11.94 | +$0.02 | 2026-05-12 |
| [F012](F012_auto.md) | LOSS | Alpaca | $14.57 | -$1.48 | 2026-05-12 |
| [F010](F010_auto.md) | WIN | Alpaca | $47.48 | +$11.37 | 2026-05-08 |
| [F009](F009_auto.md) | LOSS | Kalshi | $9.84 | -$9.84 | 2026-04-15 |
| [F008](F008_auto.md) | WIN | Kalshi | $9.69 | +$9.31 | 2026-04-14 |
| [F007](F007_auto.md) | WIN | Kalshi | $19.14 | +$2.86 | 2026-04-10 |
| [F006](F006_chicago_midway_b54_5_no.md) | WIN | Kalshi | $9.60 | +$5.40 | 2026-04-10 |
| [F005](F005_autonomous_kalshi_plays.md) | LOSS | Kalshi | $13.25 | -$13.25 | 2026-04-05 |
| [F004](F004_kalshi_higher_return_test.md) | LOSS | Kalshi | $28.19 | -$28.19 | 2026-04-04 |
| [F003](F003_kalshi_live_predictions.md) | WIN | Kalshi | $7.26 | +$2.74 | 2026-04-02 |


<!-- AUTO:EXPERIMENTS_INDEX:END -->
