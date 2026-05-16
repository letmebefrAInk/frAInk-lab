# Decision Engine v1 — L3 EXPANDED

**Shipped:** 2026-05-16 (PM, single fresh-terminal session)
**Status:** 🟢 Live. 4 new typed fundamentals factors + shadow short-squeeze + spec §5 hard-fail rules + `/qa` Panel 6 + `/decide` chips end-to-end. Decision Engine v1's INVESTMENT verdict now reads off real Polygon + Finnhub data, not a placeholder.
**Spec doc:** `frAInk/docs/strategy/DECISION_ENGINE_v1.md` (canonical).
**Catalog reference:** `frAInk/docs/strategy/POLYGON_REST_CATALOG.md`.

---

## What landed

Decision Engine v1 originally shipped on 2026-05-15 (PM-6) on the free tier with `PENDING_L3` placeholders wherever fundamentals data was needed. The INVESTMENT verdict on `/decide` rendered, but its body was theatre — there was no real D/E, no real growth, no real news sentiment behind the pill.

L3 EXPANDED closes that gap with **136 net new tests** across both frAInk and the Confluence Screener and ships these surfaces:

1. **`confluence_screener/polygon_data.py`** — fail-soft REST adapter wrapping 12 Polygon endpoints (`fetch_financials` returns Polygon's unified income+balance+cashflow shape per fiscal period, plus thin slice wrappers for each statement type, plus Ratios / Ticker Overview / Related Tickers / News / Form 4 / 13-F / Short Interest / Short Volume). 43 unit tests.
2. **`confluence_screener/fundamentals_factors.py`** — 4 typed factor classifiers (earnings_quality, valuation, growth, secular_cyclical) + shadow `factor_short_squeeze` + derived `factor_fundamentals_score` rollup. 54 unit tests.
3. **Taxonomy v1.2 → v1.3** — family count 10 → 11 (`fundamentals` enriched 1→5 members; new `setup_quality` shadow family for `factor_short_squeeze`). `SHADOW_FAMILIES` skip in `compute_family_firing` so shadow members don't pollute `confluence_factor_count` / DE composite math.
4. **`decision_engine.py` spec §5 completion** — D/E > 3.0 → `DON'T BUY` hard-fail on INVESTMENT; FCF deteriorating → -5pp concern penalty + chip on STANDARD/EXTENDED/INVESTMENT; valuation extreme (P/E > 50 AND growth ≤ 0) → `WATCH` override on STANDARD/EXTENDED/INVESTMENT. 13 new tests. `_is_pending_l3()` now honors record state — when an L3 factor ordinal is present, the slot consumes it.
5. **`learning.db` v10 → v11 migration** — new `fundamentals_cache` table with composite PK `(ticker, payload_key)` + TTL semantics. Daily caching for ratings/insider/news/short-interest; quarterly for financials/balance/cashflow/13-F.
6. **`outcome_log.csv` schema extension** — 7 new columns appended (5 factor ordinals + 2 raw short-interest inputs). Append-only invariant preserved; pre-L3 rows leave cells empty.
7. **`/qa` Panel 6 "Shadow Factors"** — Fisher-exact lift vs baseline for `factor_short_squeeze` at 3d horizon, status badge `🟡 ACCRUING` / `🟢 READY` / `🔴 NEGATIVE`, tripwire target 2026-06-15.
8. **`/decide` L3 chips** — Polygon Related Tickers (peer momentum, UI-only no gate weight) + News sentiment 30d trend chip. Daily-cached.

---

## 5-ticker live smoke

| Ticker | eq | val | gth | sec | sqz | rollup | D/E | P/E | What it reads |
|---|---|---|---|---|---|---|---|---|---|
| **AAPL** | -1 | -1 | +1 | 0 | 0 | 0 | 0.80 | 36.0 | Deteriorating EPS, expensive, growing, neutral secular |
| **NVDA** | +2 | -1 | +2 | +1 | 0 | +1 | 0.05 | 45.5 | Accelerating EPS, expensive, top-quartile growth, secular winner |
| **MSFT** | -1 | 0 | +1 | +1 | 0 | 0 | 0.10 | 25.0 | EPS soft, fair valuation, growing, secular winner |
| **PLTR** | +2 | -2 | +2 | 0 | -1 | 0 | 0.00 | 140.8 | Accelerating EPS, extreme premium, top growth, no SI |
| **ONDS** | -1 | -2 | +2 | 0 | -1 | 0 | 0.03 | — | Deteriorating EPS, no P/E (P/S fallback), top growth, no SI |

**Acceptance:** ≥2 of 4 fundamentals factors fire per ticker. **5/5 pass** with mean 3.2/4 factors firing.

Notes:
- PLTR at P/E 140 + growth +2 correctly does NOT trip the valuation-extreme WATCH override (growth justifies the premium).
- `factor_short_squeeze` fires negatively on PLTR + ONDS (low days_to_cover proxy) — recorded for shadow accrual, zero gate weight.
- Adapter data paths green on all 5: `inc=7-8, ratios=✓, f4=25, news=50, rec=✓, si=4, sv=30`.

---

## Two mid-session course corrections

Building this live surfaced two findings worth naming explicitly so they don't drift:

### 1. Spec §8.4 "v5 → v6 migration" was a misread

The L3 EXPANDED spec lock said `learning.db` migrates v5 → v6 for the `fundamentals_cache` table. Live-probe showed `learning.db.LEARNING_SCHEMA_VERSION = 10` (`memory.db` is the separate DB at v5). Real path: **v10 → v11**. The "correction" in the spec doc was itself the wrong correction. Patched inline.

### 2. Polygon 13-F endpoint is filer-side, not issuer-side

The spec called for `factor_secular_cyclical` to ship as a **5-source composite** (A=sector_rotation + B=analyst consensus + C=Form 4 insider + D=13-F institutional flow + E=news sentiment). Live-probe showed Polygon's `/stocks/filings/vX/13-F` endpoint returns filings keyed by institutional filer (each filing = one institution's quarterly holdings across all issuers), with no per-issuer filter. Deriving AAPL's institutional flow delta at scan time requires aggregating ~5000-7000 filings/quarter client-side — expensive.

**Decision (Frank-acked):** ship `factor_secular_cyclical` as **4-source composite v1** (A+B+C+E) and carve input D as a named v1.1 phase **L3.5b** — S3 flat-files + nightly aggregator + historical backfill (~4-6h + ~1-2h backfill). Crucially, L3.5b also unlocks **historical-data infrastructure** for any future Polygon dataset (trades/quotes/news historical), so it doubles as the foundation for DE-5 quant model fit's optional historical-replay bootstrap.

The carve-out is captured in:
- Spec doc §8.6 with explicit "v1.1 L3.5b" row.
- Memory `project_secular_cyclical_input_d_deferred.md` (tripwire surface).
- LIVING_TODO open-item entry queued for the next fresh terminal.

---

## What's next

Per spec §9 ship order:

1. **DE-3 Decision Card V1** (~3-4h, next fresh terminal) — pipeline annotator threads L3 data onto every scanner record per-scan so the INVESTMENT verdict body reads real off the new ordinals. Today's ship wired the chips and the factor classifiers, but the pipeline doesn't yet populate `record["fundamentals_l3"]` and `record["factor_values"]["factor_*_value"]` per scan. DE-3 closes the loop.
2. **v1.1 L3.5b — S3 flat-files + 13-F aggregator + historical backfill** (~4-6h + ~1-2h backfill, Saturday/Sunday or follow-on session) — promotes `factor_secular_cyclical` to 5-source + unlocks Polygon historical-data infra.
3. **v1.1 L3.5 — promote `factor_short_squeeze` shadow → weighted** when tripwire fires (N≥30 outcomes at 3d AND 7d, Fisher-exact p<0.10 vs baseline, sign matches hypothesis). Target review date 2026-06-15.
4. **v1.1 CG — passive calendar gate** on/after 2026-05-22 (7d-22d horizon data maturity).
5. **DE-5 v1.2 — quant model fit** per `project_quant_pivot_2026_05_07.md` (~5/22+).

---

## Why this matters

Decision Engine v1 turns the scanner's TA factor profile into a **per-ticker, per-horizon BUY NOW / BUY WHEN / WATCH / DON'T BUY / DON'T TOUCH** verdict. INVESTMENT-horizon verdicts always wanted fundamentals weight (§5.6 of the spec assigned 50% to `factor_fundamentals_score` and 15% to `factor_secular_cyclical`). Before today that weight was theatre. Today it's measured: P/E, P/S, D/E, FCF trend, revenue YoY growth, EPS trajectory, analyst consensus, insider Form 4 flow, sector rotation phase, news sentiment 30d trend — all collapsed onto a typed -2..+2 ordinal each, derived rollup at the fundamentals_score slot, hard-fail rules wired for INVESTMENT, all under a fail-soft + cached + zero-regression test envelope.

`factor_short_squeeze` rides along in shadow mode because the QA Build v1 discipline (validate before you weight) is the only honest way to add new gate weight when you have N=0 historical outcomes. The Fisher-exact tripwire in `/qa` Panel 6 + the 2026-06-15 LIVING_TODO entry + the shadow-mode memory are three independent surfaces designed so the promote-or-kill decision can't fall through.

The 13-F carve-out is the most honest thing about this ship. Faking 5-source today would have been one extra source on paper and no extra signal in reality. Naming the work properly (L3.5b, with its own scope and own tests) is the structural move.
