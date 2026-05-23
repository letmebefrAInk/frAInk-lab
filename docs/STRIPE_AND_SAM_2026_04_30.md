---
lifecycle: active
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags:
- needs_audit
---

# Stripe shipped a lot this week. Here's how the SAM suite fits.

*2026-04-30*

So I had a thought.

Stripe just announced their Agentic Commerce Suite, the Agentic Commerce Protocol (ACP), the Machine Payments Protocol (MPP) co-authored with Tempo, expanded their Agent Toolkit, and launched Link as a digital wallet built for agents. That's a lot of agent-payment infrastructure landing in one quarter.

First reaction when I saw it: *"oh no, did they just eat what we're building?"*

Then I read the actual release notes.

And honestly — I think it's the opposite. I think Stripe just made what we're building more useful, not less.

Let me walk through it.

---

## What Stripe shipped (the short version)

- **MPP** — open-standard protocol for machine-to-machine payments. Stablecoins + cards + BNPL. Co-authored with Tempo. Visa came in to support card-based agent payments on the same protocol.
- **ACP** — open standard for buyer→merchant agentic commerce. Co-developed with OpenAI. Adds Shared Payment Tokens scoped to a specific seller, bounded by time and amount.
- **Agent Toolkit** — SDK that lets agents call Stripe APIs through OpenAI Agents SDK, Vercel AI SDK, LangChain, CrewAI.
- **Link** — operator-owned digital wallet that agents can request spend from with the operator's permission.
- **Agentic Commerce Suite** — bundled product for merchants who want to sell to AI agents.

This is real infrastructure. It's also, by Stripe's own docs, *deliberately scoped*. Their agent documentation literally says operators "would need to build [identity verification, transaction monitoring, spending controls, receipt generation, and subscription logic] independently."

That gap is exactly where we've been working.

---

## What we're building — the SAM suite

**SAM = Secure Agent Man.** Three pieces today. Possibly a fourth — more on that in a second.

### 1. SAMShield — the trust layer

This is the layer that asks two questions before an agent does something:

- *Is the inbound content trying to manipulate the agent?* (prompt injection, role hijack, authority spoofing, urgency manipulation)
- *Is the outbound action within the operator's policy?* (rule-driven, action-class aware, mode aware)

If either answer is "no," the action halts and the audit log captures why.

**Where it complements Stripe:** Stripe's stack is about moving money. It doesn't read a webpage and decide "is this site trying to trick the agent?" That's not what they built. SAMShield is the layer that runs *before* a payment decision (or any other agent action) even gets to Stripe.

Different category. No competition.

### 2. SAMcypher — the policy + receipt layer over the rails

This is the one most affected by Stripe's announcements, and the one I'm most excited about now.

The original idea: agents need to execute payments across multiple rails (cards, banks, blockchain) with operator-defined caps and tamper-evident receipts. We always framed it as a *car on existing rails*, not a new rail.

MPP changes the picture in a good way. Now there's an open standard for machine payments. We don't have to invent a protocol — we can speak MPP.

**Where SAMcypher complements Stripe:**

- **Agent-to-agent transfers.** Stripe's stack is buyer→merchant. ACP is buyer→merchant. MPP examples are agent→merchant. None of it covers *agent A pays agent B for a service.* That's a real flow agents will need.
- **Multi-dimensional caps.** Stripe's Shared Payment Tokens have time + amount bounds per token. SAMcypher's policy layer is built to handle "10% of pool, up to $10 per category, with a tax/fee cushion, escalate to human if breached." Owner-defined, multi-dimensional.
- **Cross-rail unified receipt log.** Stripe receipts live in Stripe's dashboard. SAMcypher's receipt log is owner-controlled and covers every transaction touching the agent's pool — including *inbound* events (refunds, A2A receipts, yield, received funds). Same surface across whichever rails are in play.
- **Stocks, prediction markets, anything non-card.** Outside Stripe's lane entirely.

**Plan: adopt MPP as the protocol SAMcypher speaks.** Agents are going to talk MPP. SAMcypher should too. The differentiation lives in the policy + cross-rail + receipt layer that Stripe explicitly didn't build.

Honestly that feels right. Open standards are how this stuff scales. We can be MPP-native and still own the layer Stripe punted on.

### 3. SAMHandler — agent-owned identity

This is the one where "agent has its own identity online" becomes a real product. Not delegated from the operator. *The agent's own account, its own credentials, its own login.*

When agents need to sign up for things, log into non-API sites, manage their own subscriptions, navigate flows where there's no developer API — they need an identity layer. SAMHandler is that layer.

**Where it complements Stripe:** Stripe doesn't do this at all. Connect is merchant onboarding. Link is an operator-owned wallet. Neither one provisions an agent's email account or signs an agent up for a SaaS subscription on the agent's own behalf.

White-space.

---

## The maybe-fourth piece

There's a slice of work that started inside SAMHandler and is now spinning out: *operator-delegated narrow credential access.* Operator says "agent, you can act on my GitHub on my behalf for the next 5 minutes with these specific permissions." Short window. Operator owns the identity. Agent borrows it.

This one *does* overlap with Stripe Link more than the other SAMs. Link is "give the agent permission to spend from your wallet without exposing the credential." The operator-delegated piece is the same idea, just generalized beyond payments.

Still working out whether this is its own product or a feature inside SAMHandler. Leaning toward its own — the customer asks for it differently, and the competitive context is now Link-shaped, which is a different conversation than agent-owned identity.

We'll see.

---

## So — competing with Stripe?

Nah.

The way I'm reading it: Stripe just **validated** the entire problem space. If they're putting this much engineering into agent payment infrastructure, the need is real. And by their own docs, they're explicitly leaving identity, policy, multi-dimensional caps, cross-rail receipts, A2A, and agent-owned identity to the operator to figure out.

That's exactly what SAM is for.

We'll speak MPP where it makes sense. We'll work alongside Link where it's the right rail. We'll fill the gaps Stripe explicitly punted on.

Building *with* Stripe, not against it.

This is still early, and we'll see how it plays out — but I'm more convinced after this week, not less.
