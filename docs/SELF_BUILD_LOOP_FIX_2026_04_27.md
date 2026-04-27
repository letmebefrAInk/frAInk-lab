# frAInk's self-build loop tried to ship its own tools last night
### It failed. Here's what we found and how we fixed it. — 2026-04-27

*Public-facing build log. Non-proprietary. The story of one Sunday auto-fire and the diagnostic it surfaced.*

---

## The short version

frAInk has an autonomy goal — at maturity, it should identify its own capability gaps, draft the code to close them, validate the drafts, and promote the survivors. We've been building that loop in stages since early April. Sunday night was the first cycle in which all three drafters (rules, tools, and the proposal scanner that feeds them) fired without human prompting.

Two of the three worked. The third produced four well-structured drafts that all failed validation, and stayed stuck in shadow status.

This is the story of that failure — what happened, why it's actually good news, and the one prompt change that unblocks every future cycle.

---

## What the self-build loop is

frAInk's self-improvement is not a single "let the model write code" loop. It's three coordinated jobs running on a weekly schedule:

1. **Proposal scanner** (Sunday 18:00) — reads frAInk's own journals from the prior week, extracts "things that didn't work, gaps I noticed, capabilities I wished I had," deduplicates them, and writes them to a `proposals` table.
2. **Rule proposer** (Sunday 18:30) — looks at proposals tagged as behavioral patterns (e.g. "I should avoid placing trades within X days of earnings") and drafts new rules that frAInk's Policy agent can evaluate. Drafts land in *shadow mode* — they fire on every relevant context but don't change behavior, so we can observe them before they go active.
3. **Tool proposer** (Sunday 19:00) — looks at proposals tagged as tooling gaps (e.g. "the screener needs to know what's already in the portfolio") and drafts new Python tool files. Drafts go through an AST capability inspector, then a sandboxed execution test, and only then become candidates for promotion.

Critically: the human is not in the inner loop. The human reviews promotions on the back end. The drafting + validation + shadow observation is autonomous.

---

## What ran on Sunday 2026-04-26

| Stage | Time (UTC) | Result |
|---|---|---|
| Proposal scanner | 22:00–22:03 | ✅ 27 new proposals captured from the week's journals |
| Rule proposer | 22:30 | ✅ 6 new shadow rules drafted (now firing for observation) |
| Tool proposer | 23:01–23:02 | ⚠️ 4 tools drafted — all 4 failed sandbox validation |

The proposal scanner correctly surfaced the high-leverage gaps frAInk had been complaining about in his own grade analysis: position-blindness in the screener (re-flagging stocks it already owns), earnings-blindness (flagging buy candidates days before earnings), absent-stop-loss enforcement, and a Kalshi reconciler stuck open since April 11.

The rule proposer correctly drafted six new shadow rules covering related behavioral gaps. Those are now logging fires on every relevant context — we'll get observation data over the next few weeks before any of them flip to active.

The tool proposer drafted four tool files that all passed the static AST inspector — meaning they didn't try to use any of the forbidden capabilities (subprocess, raw HTTP, exec/eval, etc.). But they all failed the second gate: the sandbox.

---

## The bug

Here's the diagnostic we did this morning.

frAInk's tool sandbox is deliberately strict. It runs each draft tool in a subprocess with a scrubbed environment — no API keys, no PYTHONPATH pointing at frAInk's tree, no access to live integrations. This is by design: a proposed tool that imports `from integrations.alpaca import AlpacaClient` at module top would be capable of placing real orders if the import resolved. The sandbox refuses that import on purpose, so a buggy draft can't reach live state during validation.

The tool-proposer prompt teaches around this: if your tool needs live state, defer the integration import into the function body, not the module top. Then add a "dry-run early return" — when the tool is called with no arguments, return a stub before reaching the deferred import. The sandbox can then exercise the module's load path and one no-args call without crashing.

All four Sunday drafts did this correctly. The deferred imports were inside the function body. The dry-run early returns existed.

But each tool also defined a `dry_run=True` kwarg, and each tool's `TEST_INPUTS` (the test cases the sandbox harness runs) included entries that looked like:

```python
TEST_INPUTS = [
    {},                                                # smoke test, hits early return
    {"order_id": "abc-fake-uuid", "dry_run": True},    # bypasses early return
]
```

The early return only checked `if not order_id`. The `order_id` was truthy ("abc-fake-uuid"), so the early return didn't fire — even though `dry_run=True` was set. The function fell through to the deferred import, the sandbox said `ModuleNotFoundError: No module named 'integrations'`, the draft was marked failed.

Four tools, identical failure mode, all from the same prompt-pattern gap.

---

## Why this is good news

Three observations:

**The validation worked.** The sandbox caught a category of subtle bug — "this tool would silently call live trading systems if you tested it with realistic inputs" — exactly the kind of failure we built it to catch. If the sandbox were less strict, those drafts would have promoted, and frAInk's first auto-built tool to talk to Alpaca might have made a real call during validation.

**The bug was in the prompt, not the architecture.** The drafter is following the rules it was taught. The rules just had one gap. That's a 30-line prompt edit, not a redesign.

**The loop is alive.** Two of three stages worked perfectly the first time. The third produced four drafts of meaningful, well-structured code that just happened to share one logical bug. That's a long way from "the model can't write tools."

---

## The fix

The system prompt for the tool proposer now includes:

- An explicit rule that the dry-run early return must honor a `dry_run=True` kwarg, not just check for missing args. Pattern: `if dry_run or (not symbols and not order_id):`
- A new section on `TEST_INPUTS` for tools that need live state. Two valid shapes only: (A) `[{}]` empty-only, or (B) `{}` plus entries that all set `dry_run=True` AND the early return honors that flag.
- An explicit "recent failure mode" callout describing exactly what the four Sunday drafts did wrong, so the model recognizes the anti-pattern when it sees it.

We verified the fix by hand-patching one of the Sunday drafts with the corrected dry-run gate and running it through the sandbox: clean pass, two harness calls, 26ms.

The 18 tests in the tool-proposer + sandbox test suites still pass.

---

## What happens next

Next Sunday's auto-fire (2026-05-03) will redraft the four stuck proposals — including the Kalshi reconciler that's been blocked since April 11 — using the corrected pattern. If they pass AST + sandbox, they become candidates for promotion. If they don't, we get another clean diagnostic and another small prompt fix.

Two of those proposals (`cancel_alpaca_order` v1 + v2) are now obsolete — `cancel_order` shipped through the Executor toolkit on April 21, and the proposer didn't have visibility that the canonical version already exists. That's a smaller secondary issue worth a future fix: feed the proposer the current Executor + Planner toolkits so it doesn't draft duplicates.

---

## The bigger thread

A self-improving agent isn't a model that writes itself in one pass. It's a slow, careful, verifiable loop where every output goes through capability gates before it ships. The sandbox finding a bug is a feature, not a setback. The model finding a bug it didn't know was a bug — and the human reading the diagnostic, fixing the prompt, and pushing — is the actual work of building autonomy.

frAInk doesn't need to be perfect to ship his own tools. He needs validation that catches mistakes before they reach production, a feedback loop that explains what went wrong, and a human in the outer loop who fixes patterns when patterns appear.

All three were present this morning. The loop ran. The next one will too.
