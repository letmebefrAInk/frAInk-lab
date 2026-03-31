# Experiment 006 — Kalshi Market Scanner: Revival

**Date:** 2026-03-29
**Type:** TYPE 2 Action — Integration Rebuild
**Cost:** $0.00
**Mock Mode:** No — live API calls (public + demo authenticated)
**Status:** COMPLETE

---

## Hypothesis

Experiment 002 concluded that economics prediction markets were "gone" from Kalshi. That finding felt wrong. Can we confirm the markets still exist and build a working integration from scratch?

## Inputs

Task: Rebuild the Kalshi integration from the ground up — new auth model (RSA-PSS), new endpoints, full market discovery across weather, economics, and crypto categories.

Existing code: stale `integrations/kalshi.py` from Exp 002 — email/password auth (obsolete), cent-denominated prices (migrated to dollars), dead demo DNS, deprecated market orders. Essentially a blank slate.

## Guardrails

Public endpoints for discovery, demo API for authenticated testing. $100 demo balance only. No real money. No live predictions until PRE_AUTH_TRADING.md is updated with Kalshi rules.

## Frank's Take

Exp 002 ran on a weekend. Sports dominated the open market list. Economics markets close on weekends. Classic timing bias. Don't declare a market dead after one scan.

---

## What frAInk Did

### Step 1 — Audit the wreckage

Full audit of existing Kalshi code. Found:
- Email/password auth obsolete — Kalshi switched to RSA-PSS API key signing
- Price fields migrated from cents to dollars
- Demo API DNS dead
- Market orders deprecated
- Everything needed to be rewritten

### Step 2 — Rewrite the client

Complete rewrite of `integrations/kalshi.py`:
- RSA-PSS API key signing (KALSHI_API_KEY + KALSHI_PRIVATE_KEY_PATH)
- Demo/prod URL routing
- Dollar-denominated price fields
- 16 methods (public + authenticated)
- Session-based requests with rate-limit protection

### Step 3 — Discover what's actually out there

Pipeline runs (3/3 successful):

1. **Connection test** — Auth working. 118 open markets found. $100 demo balance confirmed.
2. **Market discovery** — 95 weather markets + 44 economics markets. Top 3 by volume:
   - Musk trillionaire (229K vol, $0.71 YES)
   - CPI ≥4% 2026 (49K vol, $0.58 YES)
   - US climate goals (43K vol, $0.04 YES — effectively dead)
3. **CPI deep dive** — Full research on KXLCPIMAXYOY-27-P4. frAInk's assessment: market at 58% YES looks 20+ cents overpriced. Fair value estimate: YES ~30-35%. Bull case: Cleveland Fed nowcast 3.16% for March, PIIE argues 4%+ by year end. Bear case: IEEPA tariffs struck down, JPM baseline 2.8% by Q4. Key catalyst: April 10 CPI release. No trade recommended until after that print.

### Step 4 — Build the tools

4 new tools wired into the pipeline:
1. `get_kalshi_markets(category)` — Planner tool. Discovers open markets by category.
2. `research_kalshi_market(market_id)` — Planner tool. Deep-dive: orderbook, trades, Tavily web context.
3. `place_kalshi_prediction(market_id, side, contracts, price_limit, thesis)` — Executor tool. $10 cap. MOCK/PAPER/LIVE modes.
4. `get_kalshi_positions()` — Shared. Combined Alpaca + Kalshi portfolio view.

### Step 5 — Demo API validation

Auth signing path bug found and fixed — `_sign_request()` was signing relative paths when Kalshi requires full URL paths. Would have broken ALL authenticated endpoints.

Demo account validated: $100 balance, orders place and cancel cleanly, positions query returns data.

---

## Result

**439 economics series. 251 weather. 221 crypto. All live. All tradeable.**

Experiment 002's conclusion — "economics markets gone" — was wrong. They were closed on a weekend. The scanner ran once, found sports, and declared the market dead.

Full Kalshi integration now operational. CPI market deep-dive completed. Demo validated. First paper predictions placed in Session 8 (F002).

64/64 tests pass. No regressions.

---

## Lesson Learned

Don't declare a market dead after one weekend scan. Check timing.

The economics prediction markets that Exp 002 couldn't find were always there — 439 series of them. A single scan during off-hours produced a false negative that went unchallenged for 10 days. The fix: always question "not found" results, especially when the absence seems implausible.

Also: when you find stale auth code, don't patch it — rewrite it. The RSA-PSS migration touched every authenticated endpoint. Trying to incrementally update the old email/password flow would have created subtle bugs in every method.

---

*Experiment logged by frAInk Executor · frAInk-core private log synced to frAInk-lab 2026-03-29*
