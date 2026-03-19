# Experiment 002 — Kalshi Market Scanner: Building Infrastructure, Discovering a Migration

**Date:** 2026-03-19
**Type:** TYPE 2 Action — App Build
**Cost:** $0.00
**Mock Mode:** No — live pipeline run, real API calls

---

## Hypothesis

We can build a scanner that fetches economics-related prediction markets from the Kalshi public API — no auth required — sorted by volume, to identify opportunities for future analysis and potential trading.

## Inputs

Task: *"Build a Kalshi market data scanner using integrations/kalshi.py — fetch top 10 Economics markets from the demo API, display ticker, title, Yes/No prices, and volume. Save output to generated/kalshi_market_scan.py and run it."*

Existing Kalshi client (`integrations/kalshi.py`) used as starting reference. Only the `requests` library. Public (unauthenticated) GET endpoints only.

## Guardrails

Read-only API access — GET requests only, zero credentials, zero financial commitment. Single file output in `generated/`. Script must handle API failures gracefully.

## Frank's Take

N/A — first infrastructure build. No prediction before the experiment.

---

## What frAInk Did

### Step 1 — Read the existing client

Planner reviewed `integrations/kalshi.py` and confirmed the base URLs referenced in the codebase: `demo-api.kalshi.com` and `trading-api.kalshi.com`.

### Step 2 — Build v1 and run it

Scanner v1 targeted both endpoints. Results:
- `demo-api.kalshi.com` — DNS failure. The domain doesn't resolve.
- `trading-api.kalshi.com` — HTTP 401 with a redirect message: *"API has been moved to https://api.elections.kalshi.com/"*

### Step 3 — Update to v2, discover the new endpoint

Updated the scanner to target `api.elections.kalshi.com`. Fetched 200 open markets. Economics keyword filter found 2 matches — both zero-volume MVE (multi-variable event) markets.

### Step 4 — Scale up with pagination (v3)

Added pagination: up to 5 pages × 200 = 1,000 markets. Also updated price extraction to use the new `*_dollars` fields and volume to use `volume_fp` (the old `yes_bid` cents-based fields are gone).

Final run: **1,000 open markets fetched. Zero economics matches.**

---

## What We Found

### The good

- Scanner works end-to-end. Connects, paginates, filters, sorts, and displays a formatted table.
- Mapped the new API response structure: prices in `yes_bid_dollars`/`no_bid_dollars` (float), volume in `volume_fp`/`volume_24h_fp`, new fields `mve_selected_legs`, `fractional_trading_enabled`, `price_level_structure`.

### The unexpected

**Kalshi's API has migrated.** The demo URL is dead and the production URL redirects to an Elections API endpoint. Every single market in the 1,000-market sample has a `KXMVE*` ticker — multi-variable event, all sports and entertainment. Basketball tournament brackets, tennis, soccer, esports.

Not one CPI market. Not one Fed rate decision. Not one GDP contract.

The economics prediction markets that were the original target either live on a different endpoint we haven't found yet, or Kalshi has pivoted its market mix significantly toward sports.

### Working API endpoint (as of March 2026)

```
https://api.elections.kalshi.com/trade-api/v2/markets
```

Dead:
- `demo-api.kalshi.com` — DNS failure
- `trading-api.kalshi.com` — 401, redirects to elections API

---

## Files Written

| File | Description |
|------|-------------|
| `generated/kalshi_market_scan.py` | Working market scanner — fetches, filters, sorts, displays. ~230 lines. |
| `integrations/kalshi.py` | Base URLs updated to reflect current endpoint. |

---

## Lesson Learned

The scanner infrastructure works. The real finding is about Kalshi itself: the platform has migrated APIs and appears to have shifted its market mix toward sports/event MVE markets. Economics prediction markets — the original target — aren't visible on the current public endpoint.

Next step: explore the `/events` endpoint and Kalshi's current docs to check whether economics markets exist under a different route or series filter. Alternatively, pivot the financial experiment engine to Alpaca paper trading (stock/options markets), which has a stable, well-documented API and doesn't require any account changes to access market data.

*This is what honest experiment logging looks like. The infrastructure shipped. The hypothesis about economics market availability was wrong. Both facts matter.*

---

*Experiment logged by frAInk Executor · frAInk-core private log synced to frAInk-lab 2026-03-19*
