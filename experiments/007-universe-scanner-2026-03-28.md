---
lifecycle: active
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags:
- needs_audit
---

# Experiment 007 — frAInk Finds His Own Leads: Universe Scanner

**Date:** 2026-03-28
**Type:** TYPE 2 Action — Capability Build
**Cost:** $0.00
**Mock Mode:** No — live API calls (CoinGecko, Wikipedia, Alpaca)
**Status:** COMPLETE

---

## Hypothesis

A screener that only looks where you point it is half a screener. Can frAInk expand beyond curated watchlists to scan entire market universes — S&P 500, crypto top 50, Russell 2000 — and find setups he wasn't told to watch?

## Inputs

Task: Build universe-based market scanning into the existing `run_screener()` tool. Add self-managed watchlists so frAInk can autonomously add, remove, and flag tickers based on what he finds.

Existing infrastructure: Confluence Screener integration (`run_screener()` tool), Alpaca paper trading, SQLite working memory.

## Guardrails

- Universe scans capped at top 10 results to prevent noise flooding
- 24-hour cooldown on universe scans (rate-limit protection)
- All watchlist changes are TYPE 2 pipeline-gated — Planner proposes, Policy approves, Executor acts
- No trades from universe scans without passing full pre-auth evaluation

## Frank's Take

frAInk was finding good setups on the existing watchlists — but only because Frank put those tickers there. If the system can't discover its own candidates, it's just a fancy alert system. This is the difference between a tool and an agent.

---

## What frAInk Did

### Step 1 — Universe data sources

Built `tools/_universe_fetcher.py` with three data sources:
- **S&P 500** — scraped from Wikipedia (503 tickers, live)
- **Crypto Top 50** — CoinGecko free API (50 tickers by market cap, live)
- **Russell 2000** — static CSV proxy (197 tickers, updated periodically)

### Step 2 — Screener expansion

Extended `run_screener()` with a `universe` parameter:
- `watchlist` (default) — existing behavior, curated tickers only
- `sp500` — full S&P 500 scan
- `crypto_top50` — CoinGecko top 50
- `russell2000` — Russell 2000 proxy list

Each universe scan runs every ticker through the Confluence Screener's scoring model. Top 10 results returned, sorted by score.

### Step 3 — Watchlist self-management

New `watchlists` table in SQLite memory.db. Three Executor tools:
1. **`add_to_watchlist(ticker, reason)`** — adds a ticker frAInk found worth watching
2. **`remove_from_watchlist(ticker, reason)`** — removes tickers that no longer qualify
3. **`flag_watchlist_item(ticker, status)`** — flags as `watching`, `ready`, `cooling`, or `dropped`

All changes are TYPE 2 actions — they flow through the full Planner → Policy → Executor pipeline. frAInk can manage his own watchlist, but Policy still has to approve every change.

### Step 4 — Seed and test

21 existing tickers seeded into the new watchlists table (8 stocks + 13 crypto). 40/40 tests pass. Live API tests confirmed: CoinGecko, Wikipedia S&P 500, and Russell 2000 all returning data.

---

## Result

Universe scanning is live and integrated. frAInk's first S&P 500 scan (Session 8, F002) found 10 buy_signal candidates — stocks that weren't on any watchlist. Five became paper trades within hours.

The watchlist self-management layer means frAInk's opportunity set is no longer static. He adds tickers when scans surface candidates, drops them when scores decay, and flags transitions autonomously.

40/40 tests pass. No regressions on existing tools.

---

## Lesson Learned

A screener that only looks where you point it is half a screener. The universe scan immediately surfaced candidates (AKAM, ALB, CIEN) that Frank never would have put on a watchlist — and some of them produced actionable setups on the very first day.

The self-management layer is equally important. Without it, universe scans would just dump 500 tickers into a pile. The ability to add, remove, and flag means the system curates itself. The watchlist becomes a living document of frAInk's current market view.

---

*Experiment logged by frAInk Executor · frAInk-core private log synced to frAInk-lab 2026-03-28*
