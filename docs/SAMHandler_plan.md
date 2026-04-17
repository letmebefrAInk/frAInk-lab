# SAMHandler — Identity & Authentication for AI Agents

*Design note. Public-facing. Shareable. Subject to change as the build progresses.*
*Published 2026-04-16 as a companion to the frAInk project.*

> **Working name: SAMHandler.** The third leg of the Secure Agent Man suite, alongside SAMshield (authorization) and SAMcypher (execution/treasury). Builds on the same pattern that shipped those two products: build it for frAInk first, commercialize once it's battle-tested.

---

## Why this exists

Autonomous agents need three things to operate in the world:

1. **Permission to act** — what am I allowed to do?
2. **Means to transact** — how do I fund and settle actions?
3. **An identity online** — who am I when I act?

The agent infrastructure market has good coverage on the first two. Auth0, Clerk, and Cognito provide identity for humans. 1Password and HashiCorp Vault provide credential storage for ops teams. Browserbase and Anthropic's computer-use API give agents the ability to drive browsers. Each of these solves part of the problem.

**Nobody has wrapped the whole thing for agents.** That's the gap. SAMHandler fills it.

---

## What "agent identity" actually means

Three layers, each with its own set of decisions:

**Layer 1 — OAuth delegation.** The agent acts as a human user via scoped, revocable OAuth tokens. This is available today on X, GitHub, Gmail, most SaaS APIs. It's how most early AI assistants work when they post or fetch data on your behalf. You're still legally and socially the one doing it — the agent is just your hands.

**Layer 2 — Own identities.** The agent has its own accounts. Its own email. Its own phone number. Its own social presence. This requires a legal entity to house those accounts (an LLC, in our case), a credential vault to store tokens, and a policy layer that enforces who can create accounts vs. who can use them. This is where most of the "can agents really work autonomously" question lives in practice.

**Layer 3 — Full autonomy.** The agent operates any service with or without an API. It drives browsers where needed. It has limited payment capabilities under strict caps. It is indistinguishable from a human user to the systems it touches. This tier is further out.

SAMHandler covers all three. Builds in order. Validates each before the next.

---

## The design constraints (non-negotiable)

A few things we won't compromise on:

- **Account creation is always human-gated.** Forever. No agent can autonomously create a new account anywhere. Too irreversible. Damage from a bad signup can't be undone by revoking a token.
- **Revocation is one command and it's fast.** All active credentials die in under 60 seconds on human command. The kill switch is tested regularly, not just assumed.
- **Credentials never sit in agent memory or logs.** They're issued on demand, short-lived, and ephemeral. An agent memory file that's exfiltrated should be useless.
- **Every credential use produces an audit record.** The agent signed the tweet with this token at this moment. Not just "a tweet happened" — who, what, when, why, traceable.
- **Policy decisions happen in SAMshield.** Credential issuance happens in SAMHandler. Action execution happens in SAMcypher. Each product does one thing and passes clean interfaces to the others.

---

## What we're building (and not building)

**In scope for SAMHandler:**
- Credential vault with short-TTL issuance and audit trail
- Policy hooks for new action classes: account creation, credential issuance, credential revocation, social posting
- Per-service adapters for how credentials get provisioned and rotated (start with X, GitHub, Gmail)
- The one-command revocation path
- A published SDK so other agents can plug in

**Out of scope (and staying out):**
- Not an identity provider for human users — that's Clerk and Auth0's space
- Not a password manager — that's 1Password's space
- Not a browser automation product — that's Browserbase's space
- Not a KYC/AML service — that's Stripe Identity's space
- Not a policy engine on its own — SAMshield already owns that

The product shape is *the thin layer that gives agents credentials with policy-aware lifecycle*. Everything else is someone else's product.

---

## How it fits with frAInk

frAInk is SAMHandler's first dogfood customer. Same pattern that validated SAMcypher and SAMshield before they became products.

frAInk needs this for real reasons. To read thread content from X that's behind a login wall. To draft posts under a public identity. To have an email address that isn't tied to the founder's personal account. To do all of that inside a policy framework that lets the founder sleep at night.

When frAInk has been running on SAMHandler for a few weeks, the patterns that emerge become the product features that ship externally. The thing that makes this work is that frAInk is a skeptical, high-stakes customer. If SAMHandler can't handle frAInk's paranoia about getting hacked, it can't handle someone else's production agent either.

---

## How it fits with other agents

SAMHandler is not a frAInk feature. It's a standalone product.

Any agent — written in any language, by any team — will be able to install a thin SDK, register an agent ID, and start requesting credentials by purpose. The agent says "I need an X token for posting" and SAMHandler either issues a short-lived token under policy review or rejects the request with a reason. The agent never holds long-term credentials.

Commercially, that means an AI startup building on GPT or Gemini or any other model can plug in SAMHandler without rebuilding their policy stack. They bring their own policy engine if they want, or they use SAMshield alongside. The suite works as a bundle, but each piece stands alone.

---

## Why this is hard and why it's worth doing

Hard:
- Credential lifecycle management has subtle failure modes. TTLs too short break integrations. TTLs too long make revocation meaningless.
- Policy for identity actions is stricter than policy for transactional actions. The blast radius of a bad signup is forever.
- Vault infrastructure is well-trodden but agent-aware patterns are not.
- Account-platform terms of service are murky on autonomous agents. We expect friction with X, LinkedIn, and others as they build stance on AI agents operating on their platforms.

Worth it:
- Agent infrastructure is one of the fastest-growing product categories in AI. Identity is the piece everyone assumes is already solved and nobody has actually shipped.
- The bundle economics are strong. Selling authorization + execution + identity together means buyers don't have to assemble three vendors. That's the moat.
- frAInk's own path to full autonomy genuinely depends on this. Building it as a product means the investment pays back twice — once in frAInk's capabilities, once in recurring revenue.

---

## Status

Design phase. Architecture captured in a private working document. First build target is the OAuth-delegation layer for a single service — enough to validate the pattern without committing to the full stack. LLC formation and credential vault selection are gating items for the production tier.

---

## Not answered yet

Intentionally unresolved questions at the time of writing:

- What exact name ships? "SAMHandler" is the working placeholder. Alternatives under consideration: SAMid, SAMkey, SAMforge, SAMstamp, SAMvault. Naming is the last 5% of the problem but it matters.
- Which credential vault: build custom, use HashiCorp Vault open-source, or subscribe to 1Password Secrets Automation. Answer likely depends on whether the product ships internal-first or public-first.
- Commercial pricing model. Per-credential-issuance is a natural unit. But the shape of demand is unknown until there are real customers beyond frAInk.

---

*This is a design note, not a launch announcement. When the first public build is live, the link will live here.*
