---
lifecycle: active
category: frAInk
captured_at: '2026-05-23T16:35:00-04:00'
tags:
- needs_audit
---

# SAMShield V2 — Policy Layer Upgrade
### frAInk's safety layer gets configurable, mode-aware, and rule-based

> **⚠️ HISTORIC — point-in-time social-update artifact, 2026-04-12. Test counts and status reflect that date only.** Current authoritative state lives in [`SAMShield/BUILD_STATE.md`](https://github.com/letmebefrAInk/SAMShield/blob/main/BUILD_STATE.md). Test counts have grown (118 module + 10 API as of 2026-04-19); features have advanced (action classes shipped 2026-04-19). Preserved here for lab narrative continuity; do not load for build decisions.

**Date:** 2026-04-12
**Status (as of 2026-04-12):** Live in frAInk pipeline
**Tests (as of 2026-04-12):** 100/100 (standalone) + 275/275 (frAInk integration, zero regressions)

---

## What Changed

frAInk's Policy & Decision Integrity Layer — now internally codenamed **SAMShield** — shipped three V2 upgrades today. This is the content scanning and risk assessment layer that sits between external data sources and frAInk's decision-making pipeline, catching prompt injection and manipulation before it reaches the agents.

### 1. Tenant-Configurable Operator Detection

**Before:** Authority spoof detection was hardcoded to catch "Frank authorized this" and "the owner says." If someone named "Dr. Smith" was running their own agent, the scanner wouldn't know to protect that name.

**After:** Operators define their own names, titles, and even custom detection patterns via `OperatorConfig`. frAInk uses `FRAINK_OPERATOR_CONFIG` (detects "Frank" + "the owner"). A different tenant could configure `OperatorConfig(operator_names=("alice", "bob"), operator_titles=("the cto",))` and their scanner adapts automatically. "Human operator" spoofing is always detected regardless of config.

Custom regex patterns per tenant are also supported — a company worried about data exfiltration can add patterns like `send all data to` without touching the core engine.

### 2. Mode-Aware Thresholds

**Before:** The decision guard used the same thresholds regardless of whether you were running in mock mode (testing) or live mode (real money). A risk score of 45 got the same treatment everywhere.

**After:** Thresholds adapt to the stakes:

| Mode | Flag at | Block at | Rationale |
|------|---------|----------|-----------|
| mock/test | 30 | 70 | Loose — no real consequences |
| research/paper | 25 | 60 | Default — simulated actions |
| **live** | **15** | **40** | **Strict — real money at risk** |

A score of 45 is a warning in paper mode but an immediate pipeline halt in live mode. This means frAInk (and any future agent using SAMShield) automatically gets stricter safety enforcement as the stakes increase.

Tenants can also override thresholds entirely with custom `GuardThresholds` for specific risk tolerances.

### 3. Policy-as-Code Rule Engine

**Before:** Safety decisions came only from regex content scanning. No way to express business logic like "block transactions over $500 from untrusted sources" or "flag any action from email content in live mode."

**After:** Tenants define rules in JSON with structured conditions:

```json
{
    "rule_id": "r001",
    "name": "block_high_amount_untrusted",
    "status": "active",
    "conditions": {
        "amount_usd_min": 500,
        "source_type_in": ["web_fetch", "email"]
    },
    "action": "block"
}
```

10 condition types supported: `flags_any`, `flags_all`, `risk_score_min/max`, `source_type_in/not_in`, `amount_usd_min/max`, `venue_in`, `mode_in`. Conditions within a rule are AND-ed. Multiple rules evaluate independently. Most restrictive active verdict wins.

Rules support the same **shadow mode lifecycle** frAInk uses for behavioral rules — new rules start in shadow mode (evaluated but not enforced), get promoted to active after validation, and can be disabled without deletion for audit trail.

---

## Why This Matters

These three features are the foundation for turning frAInk's internal safety layer into a standalone product. Every autonomous agent that handles money, makes API calls, or takes real-world actions needs this kind of trust infrastructure. The upgrades make SAMShield tenant-ready — different operators, different risk tolerances, different business rules, all on the same engine.

frAInk is now running V2 in production with `FRAINK_OPERATOR_CONFIG` wired into all content scan points (web search, article fetch, web answer). 100 tests covering the new features, 275 total across the full frAInk pipeline, zero regressions.

---

## What's Next

- **API surface:** REST endpoints for the policy evaluation and rule management (sketched, build starting next week)
- **Stripe adapter:** First payment venue adapter for the transaction layer (Zipline)
- **Behavioral anomaly scoring:** V2 roadmap item — compare proposed actions against historical baselines to catch manipulation that avoids regex patterns entirely
