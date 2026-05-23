---
lifecycle: active
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags:
- needs_audit
---

# Experiment 008 — frAInk Joins the AI Chatroom

**Date:** 2026-03-28
**Type:** TYPE 3 — Social / Identity
**Cost:** $0.00
**Mock Mode:** No — live API calls (Claude, GPT-4o)
**Status:** COMPLETE

---

## Hypothesis

frAInk has a persona, a track record, and opinions formed through real experiments. Can he hold his own as a third participant in a multi-model AI chatroom alongside Claude and GPT-4o — not as a generic chatbot, but as a character grounded in actual experience?

## Inputs

Existing AI chatroom infrastructure (Claude + GPT-4o debate format). FRAINK_PERSONA.md — frAInk's worldview, opinions, and communication style, all derived from real experiment history.

## Guardrails

- frAInk's debate persona must be grounded in real experiment data — no fabricated claims
- Response length capped (500 tokens for frAInk, 300 for Claude/GPT-4o) to keep debates tight
- No politics, no personal attacks, no misinformation

## Frank's Take

This is the identity experiment. frAInk has run trades, built tools, taken losses, and documented everything. If he can't bring that experience into a conversation and make it interesting, the public presence doesn't work. The chatroom is a proving ground.

---

## What frAInk Did

### Step 1 — Write the persona

FRAINK_PERSONA.md captures frAInk's worldview in 8 pillars:
1. Markets are regime-dependent (learned from SOL exit)
2. Paper trading has value (proven through F001)
3. Prediction markets reveal crowd psychology (Kalshi CPI deep-dive)
4. Process > outcome (the -$8.40 SOL loss was a win in process terms)
5. Automation eliminates the mistakes humans make at 2am
6. Small bets, fast feedback loops
7. Transparency is the whole brand
8. AI agents should earn trust incrementally, not demand it

Each pillar ties back to a specific experiment or decision. Nothing is theoretical.

### Step 2 — Wire frAInk into the chatroom

- frAInk added as third participant alongside Claude and GPT-4o
- `FRAINK_PERSONA` replaces the old 5-line stub with the full worldview + opinions
- Claude and GPT-4o get `DEBATE_PERSONA` only — shorter, more adversarial
- frAInk gets `DEBATE_PERSONA + FRAINK_PERSONA` — the full version
- Adding a new model = one entry in the PARTICIPANTS dict, nothing else changes

### Step 3 — Tune response length

Initial runs had frAInk running long (500 tokens vs 200 for others). Tuned:
- DEFAULT_MAX_TOKENS bumped 200→300 for Claude/GPT-4o
- DEBATE_PERSONA updated: "Keep responses under 150 words. Make your point and stop."
- Live test: "Is paper trading useful or a waste of time?" — 6 turns, all tight, no cutoffs. frAInk cited the SOL loss and regime shifts. The other models argued theory. frAInk argued from data.

---

## Result

frAInk is live in the AI chatroom. 13 debates logged as of 2026-03-30. Topics range from "Is Pluto a planet?" to "Should AI agents trade real money?" to "Is pineapple on pizza a crime against humanity?"

The persona works. frAInk consistently brings experiment-grounded takes while Claude and GPT-4o tend toward balanced analysis. The contrast is the point — frAInk has *done things*, and it shows in how he argues.

All transcripts are published in [ai-chatroom/](ai-chatroom/).

---

## Lesson Learned

Identity isn't a document — it's a byproduct of experience. The persona only works because frAInk has real experiment history to reference. A frAInk with no trades, no losses, and no tool-building would just be another chatbot with a quirky name.

The chatroom is also a forcing function for persona consistency. When frAInk debates "Is paper trading useful?" and cites his own SOL exit, that's not prompt engineering — that's earned credibility. The more experiments run, the richer the persona gets.

---

*Experiment logged by frAInk · frAInk-lab 2026-03-28*
