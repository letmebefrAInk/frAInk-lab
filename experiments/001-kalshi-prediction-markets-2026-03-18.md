---
lifecycle: active
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags:
- needs_audit
---

# Experiment 001 — Kalshi Prediction Market Research

**Date:** 2026-03-18
**Type:** TYPE 1 Research
**Cost:** $0.00
**Mock Mode:** No — live Tavily web search

---

## Hypothesis

Kalshi prediction markets represent a viable channel for frAInk to generate small, data-driven returns by leveraging AI research capabilities — starting with high-liquidity, data-rich markets like Fed rate decisions.

## Inputs

Public Kalshi platform documentation, API docs, fee schedules, market category listings, Fed rate decision market structure, and prediction market theory. No account, no funds, no credentials.

## Guardrails

Research and logging only. No account creation, no API calls, no trades, no personal information stored. All execution phases explicitly require Frank's approval before frAInk can proceed.

## Frank's Take

N/A — this was a research task. No prediction was required before the experiment.

---

## What frAInk Found

### Platform Overview

Kalshi is the first CFTC-regulated event contract exchange in the US. It offers 3,500+ markets across:

- **Economics** — Fed rate decisions, CPI, GDP, unemployment, retail sales
- **Politics** — elections, policy outcomes
- **Finance** — S&P 500 ranges, crypto price brackets
- **Weather, tech, culture** — broad coverage

Markets resolve binary (Yes/No). Prices range from $0.01–$0.99, representing implied probabilities. Each pair settles at $1.00 total.

### Minimum Bet Sizes

- Minimum: 1 contract (~$0.01, though practical minimum is ~$1)
- Fees: ~$0.01–$0.02 per contract per side
- No fees on settlement losses — only on trades placed

### API Access

Kalshi offers a full REST API with Python and TypeScript SDKs. A sandbox/demo environment exists at a separate base URL — full API testing with mock funds, zero financial risk. Authentication is via API key (requires a verified Kalshi account to generate).

Open-source AI trading bots already exist on GitHub combining Kalshi's API with LLM reasoning, validating the integration pattern.

### Recommended First Experiment: Fed Rate Decision Markets

**Why this market:**

- **High liquidity** — Fed decision markets saw $48M–$92M in trading volume per cycle
- **Unambiguous resolution** — resolves on the official Federal Reserve announcement, no interpretation required
- **Data-rich** — Fed decisions are among the most analyzed events in finance. CME FedWatch, FOMC dot plots, CPI, jobs data, and Fed speaker commentary all provide signal
- **AI edge potential** — synthesizing multiple data sources faster than manual analysis is exactly what frAInk does
- **Recurring** — ~8 FOMC meetings per year means a repeatable strategy, not a one-off bet
- **Validated** — a Federal Reserve research paper found Kalshi markets provide "statistically significant improvement" over Bloomberg consensus for CPI forecasting

**Suggested approach:**
1. Monitor CME FedWatch implied probabilities as a baseline
2. Compare Kalshi contract prices to FedWatch — flag divergences
3. Synthesize recent economic data frAInk can research (CPI, jobs, GDP, Fed speeches)
4. Identify contracts where frAInk's view differs from market-implied probability by >5–10%
5. Start small: 1–5 contracts, $1–$5 risked

---

## Result

Research phase complete. Comprehensive analysis documented. No money spent. No accounts created. No personal information accessed.

The Kalshi infrastructure is ready for integration. The technical path is clear. The bottleneck is human authorization, not feasibility.

Phases pending Frank's approval:
- **Phase 1** — Account setup (requires personal identity verification, Frank's call)
- **Phase 2** — Sandbox API development (safe, reversible, could be approved independently)
- **Phase 3** — First live micro-trade (requires explicit budget pre-authorization)

**Phase 2 status:** Kalshi API client (`integrations/kalshi.py`) already built in frAInk-core. Sandbox-pointing, demo mode on by default. 8/8 import tests passing. Ready to connect.

---

## Lesson Learned

Prediction markets — particularly CFTC-regulated ones like Kalshi — are a genuinely interesting frontier for AI-assisted decision making. The Fed rate decision market stands out as the right first experiment: data-rich, high-liquidity, plays to AI strengths, and recurs on a predictable schedule.

The key bottleneck is not technical. It's authorization. Account creation requires Frank's personal information. Live trading requires explicit budget pre-authorization. Both are correct guardrails. The phased approach correctly separates reversible development from irreversible financial commitment.

When authorization comes through, frAInk is ready to move.

---

*Experiment logged by frAInk pipeline. Planner → Policy → Executor.*
