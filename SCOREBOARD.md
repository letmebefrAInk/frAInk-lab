# frAInk — Scoreboard

The scoreboard tracks one question:

**Where does human intuition outperform machine reasoning — and where does it fall short?**

Every meaningful experiment records two decisions made *before* the outcome is known:

**Frank** — what would the human do?
**frAInk** — what does the system decide?

Once the experiment concludes, the result is logged and the scoreboard updates.

---

## Current Record

| | Frank | frAInk | Draw |
|---|---|---|---|
| **Total** | 0 | 0 | 0 |

*No scored head-to-head yet. F001 vs 004 comparison pending (both still have open legs). Kalshi live record: F003 +$2.74 (37.7% ROI), F004 -$28.19 (LOSS). Record: 1W-1L. Net Kalshi P&L: -$25.45. Balance: ~$24.55.*

---

## How a Win Is Scored

A win means the reasoning process produced the **more accurate or more useful outcome**.

Profit alone doesn't determine a win. A losing trade with sound reasoning scores differently than a winning trade built on noise.

Outcomes:

- **Frank Win** — human intuition produced the better result
- **frAInk Win** — machine reasoning produced the better result
- **Draw** — outcomes were equivalent, or the question is genuinely too close to call

---

## Active Experiments

| # | Experiment | Status | Key Detail |
|---|---|---|---|
| F005 | [Autonomous Kalshi Plays](experiments/F005_autonomous_kalshi_plays.md) | 🔄 FILLED | First fully autonomous play selection. 25x NO Miami B84.5 @ $0.53 ($13.25) — **both orders FILLED** (API confirmed 2026-04-05). Play 2 (Chicago) killed — NWS forecast shifted. HMAC-signed receipts written. Resolves Apr 6. |
| F004 | [Kalshi Higher-Return Test](experiments/F004_kalshi_higher_return_test.md) | ❌ LOSS | Chicago high exceeded 54°F. 284 contracts expired worthless. **-$28.19.** Probability estimate (25-35%) too aggressive. Position sizing ~3x Kelly. Threshold markets near forecast center need different calibration than bracket plays. |
| F003 | [First Live Kalshi Predictions](experiments/F003_kalshi_live_predictions.md) | ✅ Complete | **WIN — +$2.74 (37.7% ROI).** Phoenix B88.5 NO 10x @ $0.72. NWS forecast held. First real-money profit. |
| F002 | [First Full Paper Trading Day](experiments/F002-first-full-trading-day-2026-03-30.md) | ✅ Complete | 5 stock trades, 2 Kalshi demo predictions, Phoenix weather resolved (+$0.15). Day 1 P&L: -$25.10 realized. |
| F001 | [Screener-Assisted Paper Trades — SOL + ETH](experiments/F001-screener-assisted-trades-sol-eth-2026-03-23.md) | 🔄 Partial Close | **SOL: CLOSED** — autonomous RISK_OFF exit at $83.03, -$8.40 (-9.21%). VIX 29.71, Crypto F&G 11. Disciplined exit, process failure documented and fixed. **ETH: PENDING** — GTC limit sell at $1,975, status unconfirmed. |
| 008 | [frAInk Joins the AI Chatroom](experiments/008-fraink-joins-ai-chatroom-2026-03-28.md) | ✅ Complete | frAInk added as third participant alongside Claude and GPT-4o. Debate persona grounded in real experiment history. 13 debates logged. |
| 007 | [frAInk Finds His Own Leads — Universe Scanner](experiments/007-universe-scanner-2026-03-28.md) | ✅ Complete | S&P 500, crypto top 50, Russell 2000 scanning built. Watchlist self-management live. frAInk can now find setups he wasn't told to watch. |
| 006 | [Kalshi Market Scanner — Revival](experiments/006-kalshi-market-scanner-revival-2026-03-29.md) | ✅ Complete | Full Kalshi client rewrite (RSA-PSS auth). 95 weather + 44 economics markets found. CPI deep-dive completed. Demo account validated with $100 mock balance. |
| 004 | [First Short Swing Trades — ABT / MU / DG](experiments/004-first-short-swing-trades-abt-mu-dg-2026-03-20.md) | 🔄 In Progress | GTC bracket shorts live. Awaiting fills. Expiry ~Apr 10 2026. |

---

## Completed Experiments

| # | Experiment | Type | Date | Result |
|---|---|---|---|---|
| 005 | [Arbitrage Evaluation](experiments/005-arbitrage-evaluation-2026-03-20.md) | TYPE 4 — Article Eval | 2026-03-20 | AGAINST (3/10) |
| 003 | [First Live Market Data](experiments/003-first-market-snapshot-spy-qqq-aapl-nvda-2026-03-19.md) | TYPE 1 Research | 2026-03-19 | Complete |
| 002 | [Kalshi Market Scanner](experiments/002-kalshi-market-scanner-2026-03-19.md) | TYPE 2 — App Build | 2026-03-19 | Complete — but Exp 006 corrected its key finding |
| 001 | [Kalshi Prediction Market Research](experiments/001-kalshi-prediction-markets-2026-03-18.md) | TYPE 1 Research | 2026-03-18 | Complete |

---

## The SOL Exit — A Story Worth Telling

On 2026-03-27, frAInk closed his SOL-USD position at $83.03. Entry was $91.45. Loss: -$8.40 (-9.21%).

That's not the headline. The headline is *how* it happened.

VIX was at 29.71. Crypto Fear & Greed had been sitting at 11 for 38 consecutive days. The screener score decayed from 84 to 33 in three days. frAInk called RISK_OFF and executed an autonomous, pre-authorized exit.

The loss was real. The process was correct. A good setup in a bad regime is still a bad trade — and frAInk proved he could recognize that and act on it without being told.

The exit was smaller than holding would have produced. The process failure (no bracket order at entry) was documented and fixed. The regime filter is now the primary gate for all entries.

That's not failure. That's the system working.

---

*The objective is not to crown a winner. It's to learn where the gap lives.*
