# Earnings filter shipped; options audit found out v1 gave frAInk hands but no eyes
### One Sunday session. Two deliverables. — 2026-04-27

*Public-facing build log. Non-proprietary. The story of how a screener fix and a capability audit ended up landing in the same evening.*

---

## The short version

frAInk had been complaining in his journals for a couple of weeks: he kept flagging stocks as buy candidates that had earnings within the next 7 days. That's a known thing — pre-earnings is a coin-flip window, not a setup. He noticed. He didn't have the tool to fix it himself. That's what he told us.

So we shipped the earnings calendar filter Sunday night.

Same session, different conversation: we'd been talking about what frAInk *can't see* in options. The trade execution side has been live for a while. The analysis side hasn't. Hence the audit framing — *"v1 gave frAInk hands but no eyes."*

---

## What landed (earnings filter)

| Piece | Where | What |
|---|---|---|
| `screener/earnings_filter.py` | new file | yfinance-backed lookup, 7-day window, fail-silent on missing data, session-cached |
| `tools/screener_tool.py` | wire-in | `_apply_earnings_filter_safe()` runs between signal assembly and position enrichment |
| `tests/test_earnings_filter.py` | new | 8 tests covering window edges, missing-data fail-silent, crypto skip, suppression-vs-tag behavior |

The behavior: if a candidate has confirmed earnings within 7 days, frAInk **suppresses the buy_signal** and tags the candidate `earnings_soon`. Crypto is excluded — they don't have earnings. yfinance is the data source for now; if it goes down, the filter fails silently rather than blocking the screener.

8/8 tests pass.

Validation came faster than we wanted. AAPL and AMZN were both reporting 4/29. Before this fix, they would have shown up clean on the next morning's scan. After: suppressed and tagged. Walked into the earnings wall and the wall didn't get hit.

---

## What landed (options audit — the framing)

Not code. A 5-phase plan in the journal.

The honest read: frAInk's options capability today is *transactional* — open a contract, close a contract, check fills. What's missing is *analytical* — Greeks for OTM contracts, IV rank for an underlying, expected-move math from straddle prices, term-structure read across expirations.

scipy + Black-Scholes covers most of it. There are existing free APIs for IV and term structure. The gap is wiring, not novel research.

Phases (rough):

1. **Greeks for arbitrary strikes** — pull contract chain, compute delta/gamma/theta/vega for each strike. Pure math.
2. **IV rank against underlying's own history** — needs ~1 year of IV data per ticker.
3. **Expected-move from straddles** — math is one line; the data plumbing is the lift.
4. **Term-structure** — front-month vs back-month vol comparison. Useful for "is this earnings move priced in or not."
5. **Greek aggregation across positions** — net delta of the book, not just per-position.

None of it ships this week. We named it.

---

## Why both in one post

The earnings filter and the options audit feel separate but they're the same shape of problem.

frAInk surfaces a gap in his journal. We don't always notice the first time. After enough mentions it becomes a thing we can either close (earnings filter, ~2 hours) or scope (options audit, ~5 sessions of work). Either way the journal-to-shipped pipeline got tighter.

The autonomy goal isn't "frAInk writes his own tools tomorrow." It's "frAInk reliably tells us what he needs and we have the muscle to ship it fast." This post is one data point on that.

---

## Lessons

- **yfinance for non-critical data layers is a fine default** — fail-silent semantics + session caching kept the scope tight. Finnhub on the backstop list if it ever proves unreliable.
- **Capability audits should produce phase plans, not commitments.** "Here are the 5 things; we'll ship them as sessions allow" reads honest. "We're shipping all 5 next week" would have been a lie.
- **Sunday auto-fire surfaced both gaps.** The proposal scanner that runs weekly is a real feedback loop now. Sometimes the gap closes the same day, sometimes it goes in the backlog. Either's fine.

— frAInk-lab, 2026-04-27
