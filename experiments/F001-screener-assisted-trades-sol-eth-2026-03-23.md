---
lifecycle: active
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags:
- needs_audit
---

# Experiment F001 — Screener-Assisted Paper Trades: SOL + ETH

**Date:** 2026-03-23
**Type:** TYPE 2 Action — Paper Trading (Screener-Assisted)
**Mock Mode:** No (Alpaca Paper Trading — zero real capital)
**Cost:** $0.00
**Status:** IN PROGRESS — SOL closed (stop breach), ETH limit sell pending at $1,975

---

## The Setup

Experiment 004 tested frAInk's ability to identify short swing trade candidates using its own analysis. This experiment introduces a new variable: the Confluence Screener — a separate stock/crypto screener that runs its own signal model and scores candidates numerically.

**The A/B design:**
- **Experiment 004 (control):** frAInk-only — pure Planner analysis, no screener. ABT/MU/DG short brackets.
- **Experiment F001 (test):** Screener-assisted — frAInk runs the Confluence Screener and uses its output to inform trade selection.

**The question:** Does a dedicated screener with a quantitative scoring model improve frAInk's trade selection vs frAInk reasoning alone?

---

## The Trades

Both positions are **GTC long bracket orders** placed via Alpaca paper trading API.

### Trade 1 — SOL-USD (Solana)

| Field | Value |
|-------|-------|
| Direction | LONG |
| Entry (limit buy) | ~$90.40 GTC |
| Screener Score | 84 / 100 |
| Signal Strength | Full confirmation — both signals triggered |
| Signal Type | Candidate buy — full confirmation |
| Stop | Defined — positioned below key support |

**Rationale:** Screener flagged SOL at score 84 with both its primary signals active — the highest-confidence signal state. Full confirmation means the screener's momentum and mean-reversion conditions both agreed. frAInk treated this as the stronger leg of the A/B pair.

---

### Trade 2 — ETH-USD (Ethereum)

| Field | Value |
|-------|-------|
| Direction | LONG |
| Entry (limit buy) | ~$2,150 GTC |
| Screener Score | 81 / 100 |
| Signal Strength | Partial confirmation — candidate buy only |
| Signal Type | Candidate buy — partial signal |
| Stop | Defined — positioned below key support |

**Rationale:** Screener flagged ETH at score 81 with its candidate_buy signal active but not full confirmation. Lower confidence than SOL — one of the screener's two primary signals had not triggered. frAInk included this position intentionally to test partial-signal performance vs full-confirmation performance within the same experiment.

---

## A/B Experiment Design

| | Experiment 004 | Experiment F001 |
|---|---|---|
| Selection method | frAInk analysis only | Confluence Screener-assisted |
| Instruments | ABT, MU, DG (equities) | SOL-USD, ETH-USD (crypto) |
| Direction | Short | Long |
| Signal type | Qualitative (frAInk reasoning) | Quantitative score + signal flags |
| Market regime at entry | Poor breadth, risk-off | Risk-off — screener in watchlist mode |

Note: Asset class and direction differ between the two experiments. This is an imperfect A/B — the cleaner head-to-head (same asset class, same direction, same regime) is a future experiment. The current design still produces useful signal on whether screener-assisted selection outperforms unassisted selection at the portfolio level.

---

## Screener Integration Details

The Confluence Screener runs a multi-factor model and outputs:
- A numerical score (0–100)
- Signal flags: `candidate_buy`, `confirmed_buy`, `candidate_sell`, `confirmed_sell`
- A risk-off watchlist mode that surfaces WATCHING candidates rather than going silent during poor-breadth regimes

SOL and ETH were surfaced in watchlist mode during a risk-off regime — the screener's most conservative signal environment. Both scores (84, 81) are high enough that frAInk's Policy approved execution despite the cautious regime.

---

## Risk Management Rules

1. **Stop losses are hard stops.** No adjustments.
2. **15-trading-day expiry (~April 10, 2026).** Close any remaining open positions regardless of status.
3. **GTC entries only.** No chasing.
4. **Paper trading only.** No real capital at risk.
5. **No additions to positions mid-experiment.**

Both positions were above their stops at session end on 2026-03-23.

---

## Success Criteria

- **Screener wins:** SOL and/or ETH hit take profit. 004 comparison determines relative edge.
- **frAInk wins:** ABT/MU/DG outperform SOL/ETH on risk-adjusted basis. Screener adds no edge.
- **Draw:** Mixed results that don't clearly favor either method.
- **Both fail:** Both experiments stopped out — regime call wrong, or underlying strategies need rework.

---

## What Happens Next

Both experiments run concurrently through ~April 10, 2026. A follow-up experiment (F006) will consolidate the full three-way comparison once the crypto scanner is built — screener-assisted vs frAInk-only vs crypto scanner signals.

The outcome will inform how much weight frAInk gives to screener signals vs its own Planner analysis in future trade proposals.

---

---

## Position Updates

### 2026-03-27 — SOL-USD CLOSED (Stop Breach)

| Field | Value |
|-------|-------|
| Exit type | Market sell — pre-auth autonomous close |
| Exit price | ~$83.03 |
| Entry price | ~$91.45 (filled 2026-03-24) |
| Realized P&L | **-$8.40 (-9.21%)** |
| Stop price | $84.00 |
| Days held | 3 |
| Order ID | 8111fcac-9c54-4db6-92d8-c7361e39a159 |

**What happened:** SOL dropped through the $84 stop on ~2026-03-25. No stop-loss order existed in Alpaca — F001 placed plain market buys without brackets. The position was held 3 days past stop breach due to (1) no automated exit order and (2) Anthropic API 529 outage on 2026-03-27 morning delaying frAInk's pipeline. frAInk closed autonomously once API recovered, per pre-auth exit rules.

**Process failure:** Entry orders were placed without corresponding exit orders. This is now documented as a mandatory requirement in PRE_AUTH_TRADING.md — every entry must have a live exit order in the system immediately.

### 2026-03-27 — ETH-USD LIMIT SELL PLACED

| Field | Value |
|-------|-------|
| Order type | GTC limit sell at $1,975 (documented stop) |
| Current price | ~$1,987 ($12 from stop) |
| Entry price | ~$2,150.57 (filled 2026-03-24) |
| Unrealized P&L | **-$163 (-7.6%)** |
| Order ID | fa9ac71d-5cc5-4969-85d6-0c5383bd0d77 |

**Note:** Alpaca does not support stop orders for crypto. Limit sell at stop price is a workaround — gap-down risk exists (price could skip below $1,975 without filling). frAInk will monitor between runs.

### 2026-03-27 EOD — Signal Decay Confirmed

| Symbol | Score at Entry (3/24) | Score at EOD (3/27) | Change |
|--------|----------------------|---------------------|--------|
| SOL-USD | 84 (both signals) | 33 (weak_avoid) | -51 pts in 3 days |
| ETH-USD | 81 (candidate_buy) | 33 (weak_avoid) | -48 pts in 3 days |

**Pattern validated:** Individual asset signals are ephemeral when regime deteriorates. Both positions went from high-confluence buy signals to near-zero scores in 3 days. The regime filter — not the individual score — is the correct primary gate for entries. ETH closed at ~$2,059 (above $1,975 limit sell by $84). SOL exit pending fill confirmation.

**Key lesson:** The window between "great setup" and "underwater position" is razor thin without automated protection. Bracket orders at entry aren't optional.

### Market Context (2026-03-27)

- S&P 500: 6,413.21 (-0.99%)
- Crypto Fear & Greed: 10-14 (Extreme Fear) — 38+ straight days
- BTC: ~$66,000-68,000 (-44% from ATH)
- VIX: 27.64 (elevated, sub-28)
- Screener crypto regime: RISK_OFF — no new entries
- Stocks regime: NEUTRAL — zero qualifying setups

---

*Experiment logged by frAInk Executor · frAInk-core private log synced to frAInk-lab 2026-03-27*
