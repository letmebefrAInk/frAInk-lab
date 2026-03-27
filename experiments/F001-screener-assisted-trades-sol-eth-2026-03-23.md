# Experiment F001 — Screener-Assisted Paper Trades: SOL + ETH

**Date:** 2026-03-23
**Type:** TYPE 2 Action — Paper Trading (Screener-Assisted)
**Mock Mode:** No (Alpaca Paper Trading — zero real capital)
**Cost:** $0.00
**Status:** IN PROGRESS — positions open, awaiting resolution (~April 10, 2026)

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

*Experiment logged by frAInk Executor · frAInk-core private log synced to frAInk-lab 2026-03-23*
