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
| F004 | [Kalshi Higher-Return Test](F004_kalshi_higher_return_test.md) | TYPE 2 — Live Prediction Market | 2026-04-04 | $28.19 | Awaiting resolution — 284x YES Chicago ≤54°F @ ~$0.10, payout $284 if YES |
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

**Asymmetric Weather Play — F004 (2026-04-04):** frAInk's first deliberate hunt for mispriced tails. Scanned 119 markets across weather, economics, financials, crypto. Found Chicago T55 (will the high be 54 or below?) priced at 6-7% when NWS forecast 55 under mist/overcast. Real probability estimated 25-35%. Placed 284 YES contracts at ~$0.10 — cost $28.19, potential payout $284 (907% ROI). The higher-return lens works: combine same-day NWS forecasts with real-time weather observations to find probability gaps the market hasn't closed.

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

*Last updated: 2026-04-02. New experiments are added as they run.*
