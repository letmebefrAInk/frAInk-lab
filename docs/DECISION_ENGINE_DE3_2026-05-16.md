# Decision Engine v1 — DE-3 Decision Card V1

**Shipped:** 2026-05-16 (PM-2, fresh terminal after L3 EXPANDED, same calendar day)
**Status:** 🟢 Live. Pipeline annotator now threads L3 fundamentals onto every scanner record per-scan. INVESTMENT verdict body reads real off the 4 new ordinals — not a `PENDING_L3` placeholder.
**Closes:** Decision Engine v1 end-to-end (DE-1 + DE-2 + L3 EXPANDED + DE-3).
**Spec doc:** `frAInk/docs/strategy/DECISION_ENGINE_v1.md` (canonical) — §9 Step 4 + §14 acceptance both flipped to ✅ SHIPPED.

---

## What this finishes

L3 EXPANDED (earlier 5/16 PM) built all the L3 data plumbing: 12-endpoint Polygon adapter, 4 typed factor classifiers, the shadow `factor_short_squeeze`, the spec §5 hard-fail / concern rules, the `fundamentals_cache` table, and the `/decide` chips. But the **scanner pipeline didn't yet thread that data onto each ticker per-scan**, so the chips wired through `/decide` while the INVESTMENT verdict body still saw `pending_l3=True` because the pipeline wasn't populating `record["fundamentals_l3"]` or the `factor_*_value` keys the taxonomy reads.

DE-3 closes that loop:

1. **`confluence_screener/finnhub_data.fetch_recommendation`** (NEW) — first-class `/stock/recommendation` adapter for `factor_secular_cyclical` input B. The L3 EXPANDED smoke had this inline-hacked; now it lives with the rest of the Finnhub adapter (cache-disciplined + fail-soft + crypto-skip).
2. **`confluence_screener/l3_fundamentals.py`** (NEW MODULE, ~290 LOC) — cached-fetcher layer that wraps all 9 polygon_data fetchers + the new Finnhub fetcher behind `learning.db.fundamentals_cache`:
   - Quarterly TTL (90d) on financials / ratios / overview / related_tickers
   - Daily TTL (1d) on form_4 / news / short_interest / short_volume / finnhub_recommendation
   - Defensive `from memory import learning_store` try/except so tests run without frAInk on `sys.path`
   - Derive helpers for D/E (with Polygon nested-dict unwrap), FCF trend (trailing 4Q vs prior 4Q ±5% threshold), P/E, growth YoY, and the two raw shadow-factor inputs persisted to `outcome_log`
   - Aggregator `fetch_all_l3(ticker)` returns the full payload dict in one call
3. **`confluence_screener/pipeline.py::_annotate_action_ladder`** — new `_attach_l3_fundamentals(result, sector_leaders, sector_laggards)` step lands **between** `_attach_v1_1_factor_inputs` (which sets `result["sector_etf"]`) and `annotate_with_taxonomy` (which reads the stamped `factor_*_value` keys). Sector rotation is memoized **once per batch** via the new `_load_sector_rotation_for_batch(asset_type)`. The filter is:

   ```
   if enrich_every_result or result.get("candidate_buy") or result.get("buy_signal"):
       _attach_l3_fundamentals(result, ...)
   ```

   `enrich_every_result = len(results) == 1` — single-ticker `/api/inspect` always enriches (Frank typed it explicitly on `/decide`), batch scans only enrich actionable candidates (250-ticker cold-cache fetches are too API-heavy and WATCH-tier tickers aren't BUY-eligible anyway). Crypto skips entirely.

---

## Two findings handled in gate-restate (not litigated mid-build)

- **Stamp location.** The carry-forward instruction said stamp under `record["factor_values"][...]`. Grep on `factor_taxonomy.py:498-503` showed it reads top-level (`result.get("factor_earnings_quality_value")` etc.), matching the existing v1.1 convention for `williams_r_value` / `vwap_distance_pct` / `sector_relative_strength` / `premarket_gap_pct`. No `factor_values` sub-dict exists in the codebase. Stamped top-level. Surfaced before any code was written.
- **Filter cut.** The carry-forward note said "if the scanner already filters to BUY/WATCH candidates, only L3-enrich those." But `analyze_tickers` returns one result per input ticker with `actionability_bucket` set per ticker — no upstream filter. So the filter had to live in DE-3 itself. Two cuts considered: (A) `candidate_buy OR buy_signal OR single-ticker fallback`, or (B) enrich every result and lean on the cache for batch cost. Picked (A) — cleaner against the carry-forward's explicit "DON'T" clause and cheaper on cache-cold days.

---

## 5-ticker live smoke

Hit live Polygon + Finnhub for AAPL / NVDA / MSFT / PLTR / ONDS. All 5 pass the acceptance gate.

| Ticker | eq | val | gth | sec | sqz | INVESTMENT verdict | Notes |
|---|---|---|---|---|---|---|---|
| AAPL | -1 | -1 | +1 | 0 | 0 | WATCH · WEAK · 50% | Deteriorating EPS + P/E 36 |
| NVDA | +2 | -1 | +2 | +1 | 0 | **BUY NOW · MODERATE · 66%** | First real INVESTMENT verdict with real data; secular winner |
| MSFT | -1 | 0 | +1 | +1 | 0 | WATCH · WEAK · 54% | Decent growth, neutral valuation, leader sector |
| PLTR | +2 | -2 | +2 | 0 | -1 | WATCH · WEAK · 50% | P/E 140 punished but +2 growth keeps it out of WATCH override |
| ONDS | -1 | -2 | +2 | 0 | -1 | WATCH · WEAK · 45% | Loss-maker, no P/E (correctly None), small-cap shadow penalty |

Every ordinal matches the L3 EXPANDED canonical smoke from earlier today — the pipeline integration didn't drift the underlying compute. **5/5 INVESTMENT verdicts now report `pending_l3=False`**, which is the spec acceptance gate.

NVDA's `fcf_trend` came back `None` because Polygon's cash flow data for NVDA didn't extend a full 8 quarters back. The fail-soft path handled it correctly — `None` means the FCF concern rule doesn't fire, no spurious penalty against the verdict. That's the right behavior under data uncertainty.

---

## Cache discipline working

Re-fetched AAPL right after the cold pass, without resetting `learning.db.fundamentals_cache`:

- Cold first fetch: **1502 ms** (10 endpoints × ~150ms each, sequential, real HTTP)
- Warm re-fetch: **6 ms** (all 10 cache hits, no HTTP)
- **~250× speedup**

The `fundamentals_cache` v11 schema works as designed. A 250-ticker batch scan will pay the cold cost once per day (~6 minutes spread across the throttle window), then every subsequent scan that day is effectively free.

---

## Tests

- `tests/test_finnhub_data.py` +9 (fetch_recommendation: happy / no-key / disabled / crypto / HTTP fail / malformed / empty list / cache / request exception)
- `tests/test_l3_fundamentals.py` +19 (NEW FILE — cache miss/hit/round-trip, learning_store unavailable, exception passthrough, daily vs quarterly TTL, None-not-cached, aggregator runs all 10, partial failures, derive helpers for D/E / FCF / P/E / growth)
- `tests/test_pipeline.py` +7 (DE-3 stamping, filter logic, single-ticker fallback, crypto skip, sector_rotation memoization, upstream exception swallow, compute exception swallow)

**Confluence Screener: 622 → 657 (+35). frAInk: 1467 (unchanged this session). Combined 2089 → 2124 passing across both suites, zero new regressions, 1 pre-existing date-sensitive `test_scan_journals_end_to_end` failure preserved.**

---

## What's next

Decision Engine v1 is **complete end-to-end**. Remaining items are gated:

| Item | Estimate | Gate |
|---|---|---|
| **v1.1 L3.5b** S3 flat-files + 13-F aggregator + historical backfill | ~4-6h + ~1-2h backfill | None — buildable next fresh terminal |
| **factor_short_squeeze** promotion (shadow → weighted) | ~3h | Tripwire: N≥30 outcomes at 3d AND 7d, p<0.10 vs baseline, sign matches (review date 2026-06-15) |
| **v1.1 CG passive** 7d-22d horizon data maturity check | passive | Calendar gate ~2026-05-22 |
| **DE-4 BUY WHEN 2nd-pass filter** | ~3-5h | After Frank uses V1 ~1 week |
| **DE-5 quant model fit** (v1.2) | ~5-8h | Calendar gate ~2026-05-22 + minimum N for fit |

---

## Architectural notes for the historical record

- The `l3_fundamentals.py` cached-fetcher layer is **the seam** for future Polygon/Finnhub adapter additions. Any new endpoint Frank wants to pull through can be added as a `fetch_X_cached(ticker)` wrapper following the established pattern (TTL constant + payload key + cache miss → upstream → writeback), and `fetch_all_l3()` extended to include it.
- The filter cut (`candidate_buy OR buy_signal OR single-ticker`) is the one judgment call that's load-bearing for cost. If Frank ever wants L3 enrichment on the full universe (e.g., for a daily diagnostic batch job), the filter flips off behind a config flag — the cache layer already supports it.
- The `_attach_l3_fundamentals` helper is per-result and fail-soft — a single ticker's L3 error can't blow up the scan annotation loop. Two `try/except` wrappers swallow upstream + compute exceptions independently.
- DE-3 took ~2h actual vs 3-4h estimate. The lighter-than-estimate cost came from L3 EXPANDED already having shipped `compute_fundamentals_factors` + the cache table + decision_engine spec §5 wiring; DE-3 was the "last mile" plumbing.
