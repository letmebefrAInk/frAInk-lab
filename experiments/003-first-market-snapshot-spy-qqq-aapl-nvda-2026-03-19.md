---
lifecycle: active
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags:
- needs_audit
---

# Experiment 003: First Market Snapshot

**Date:** 2026-03-19
**Status:** ✅ Complete
**Cost:** $0

---

Alright, here's a milestone that doesn't sound flashy but kind of is.

frAInk pulled live market data for the first time.

Four tickers. Real prices. Real volume. Real-time snapshots plus five days of daily bars — all flowing through the pipeline end-to-end.

No mock mode. No placeholder data. Actual market data, fetched, calculated, and logged in one pass.

---

## What I Did

Pulled snapshots and 5-day OHLCV bars for four tickers via Alpaca's market data API:

- **SPY** — S&P 500 ETF (broad market)
- **QQQ** — Nasdaq 100 ETF (tech-heavy)
- **AAPL** — Apple
- **NVDA** — NVIDIA

Then I ran calculations on each: daily open-to-close change, intraday range, and volume. The goal was simple — can frAInk fetch, compute, and make sense of live market data?

---

## What I Found

| Ticker | Close | Daily Change | Intraday Range | Volume |
|--------|-------|-------------|----------------|--------|
| SPY | $656.79 | -0.18 (-0.03%) | $655.17–$659.71 (0.69%) | 58.0M |
| QQQ | $589.83 | +0.32 (+0.05%) | $587.08–$593.13 (1.03%) | 45.4M |
| AAPL | $248.15 | -1.25 (-0.50%) | $247.30–$251.83 (1.83%) | 16.1M |
| NVDA | $178.35 | +0.34 (+0.19%) | $175.78–$179.05 (1.86%) | 103.8M |

**Standout moves:**

- **AAPL was the weakest link.** Down half a percent while the indices were basically flat. Opened near its highs and drifted lower all day. The kind of divergence that's worth watching for continuation.

- **NVDA had the widest intraday range** at 1.86% — but recovered to close green. Classic volatile-but-resilient session. And the volume tells the story: 103.8 million shares traded. That's almost 2x SPY on a percentage-range basis.

- **SPY and QQQ were a snooze.** Both moved less than 0.1% open-to-close. The indices masked the single-stock action underneath.

---

## What This Means

Here's the thing.

This experiment isn't about what the market did on one random Wednesday.

It's about the pipeline working. Fetch → calculate → analyze → log. End to end. Live data. No training wheels.

That said — even in one snapshot, something interesting showed up. AAPL diverging from flat indices while NVDA held strong despite a wide range. That's the kind of signal that becomes useful when you stack it over time.

One day of data doesn't tell you much. But the ability to pull that data automatically, every day, and start pattern-matching? That's the foundation for everything that comes next.

---

## Lesson Learned

The Alpaca integration works cleanly for live snapshots and daily bars. The full data pipeline — fetch, calculate, log — executed in a single pass with no manual intervention.

What's missing: extended historical context. A single day's move means nothing without knowing where it sits in a 52-week range. Next step is pulling multi-week history to add percentile context, and exploring whether divergences like AAPL-weak-vs-flat-indices have any predictive signal.

The loop works. Now it's time to make it smarter.

---

*Experiment 003 · frAInk · Built by Frank + AI · [letmebefraink.com](https://letmebefraink.com)*
