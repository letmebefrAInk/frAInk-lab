---
lifecycle: active
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags:
- needs_audit
---

# CPI & Macro Markets

> Auto-compiled from frAInk learning.db | First compile: 2026-04-16

## Overview

Kalshi's CPI brackets are the first macro market frAInk has traded. Different animal than weather: longer horizon (weeks, not hours), resolves on a BLS release, pricing moves in response to policy news and political rhetoric as much as fundamental inflation data. frAInk has one complete CPI experiment (F006) and one still-open portfolio (F007).

## Key Experiments

- **F006 (CPI Headline < 2.5%, WIN)** — entry at $0.55 implied 55% prob. Actual outcome confirmed the thesis; CPI came in dovish, bond rally + modest equity lift followed. Key learning: macro consensus data (CPI forecasts from multiple shops) provided real edge. Not pure guesswork.
- **F007 (April CPI two-bracket portfolio, 2026-04-13, STILL LIVE)** — placed 12x T0.6 NO @ $0.82 and 10x T0.8 NO @ $0.93. Both filled. Resolve May 12 via BLS CPI release. Tiered bracket strategy — the T0.6 position is the higher-conviction leg; T0.8 is the hedge against a dovish surprise.
- **F007 status correction (2026-04-13)** — mid-session, frAInk mistakenly logged F007 as a Chicago weather loss because the research run conflated two threads. Caught the error on review and corrected the record. Lesson: narrative drift happens in long-running threads; cross-check experiment_id against actual receipts.

## Patterns & Rules

- **Macro-market pricing is less stable than weather-market pricing** — F007 T0.6 repriced 18% → 47% YES in ~72 hours on a tariff headline. Weather markets don't move that fast. Implication: entry timing matters more for macro, less for weather (→ F007 research 2026-04-13)
- **Don't pyramid into a single BLS release** — two brackets on one CPI event is the maximum; concentrating further means one print determines everything (→ F007 tiered portfolio design)
- **Cross-reference BLS forecasts from 2-3 sources before entry** — Reuters consensus, Bloomberg median, Atlanta Fed nowcast. No single source has monopoly on base rate. Independent triangulation flagged CPI-T0.8 as too thin to press.
- **Watch for "policy noise repricing"** — markets can move sharply on rhetoric that won't appear in the actual data. If edge has already been priced out by noise, stand down.

## Open Questions

- Does Kalshi's CPI brackets have structural mispricing vs Polymarket or cross-exchange equivalents? F006 and F007 both suggested Kalshi YES-side overpriced vs Poly, but N=2.
- How does F007's two-bracket portfolio resolve interact with Guard #6? If T0.6 wins and T0.8 loses, is that a wash or does the loss count for G6?
- Should CPI resolution dates get special handling in the pipeline? Currently they're just another Kalshi settlement; they could warrant pre-release caution (no new entries 48h before release).

## Cross-References

- See also: [Kalshi Orderbook](./kalshi_orderbook.md) — CPI fill mechanics have quirks (limit orders expired on 2026-04-10 silently, confirmed via balance reconciliation)
- See also: [Guard Framework](./guard_framework.md) — G4 (balance) FLAG traced to CPI fill ambiguity
