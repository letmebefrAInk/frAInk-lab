# frAInk — Architecture

frAInk runs a three-agent pipeline.

---

## The Three Agents

### Planner — The Researcher

Planner reads. Planner does not decide and does not act.

When given a task, Planner gathers information from public sources, synthesizes what it finds, and packages a structured report. That report goes to Policy and nowhere else.

Planner is the eyes of the system.

### Policy — The Gatekeeper

Policy evaluates. Policy does not execute.

Policy receives Planner's report and runs it through a decision framework before anything moves forward. Every proposed action gets five questions:

1. Is this reversible?
2. Is it within the authorized budget?
3. Does it align with the experiment goals?
4. Will it generate something worth learning from?
5. Does it violate any guardrail?

If any answer is no or uncertain, Policy escalates to Frank. Nothing reaches Executor without a clear approval.

Policy is the conscience of the system.

### Executor — The Operator

Executor acts. Executor logs. Executor never moves without Policy's approval.

Executor is the only agent that touches the outside world — files, APIs, emails, code. It executes exactly what was approved, logs everything, and reports anomalies immediately. No improvisation.

Executor is the hands of the system.

---

## How They Connect

```
Task → [Planner] → Report → [Policy] → Approval → [Executor] → Log
                                  ↓
                             Escalate to Frank
```

Communication is one-directional. Planner passes to Policy. Policy passes to Executor. Executor reports to logs. No agent skips the chain.

---

## Guardrails

Every run operates inside hard limits:

- **Budget cap** — $10 maximum per single action without Frank's explicit sign-off
- **Max steps** — Policy sets a tool-call limit per task; Executor enforces it
- **Allowed tools** — Policy specifies which tools Executor may use for each task
- **Kill switch** — a single file in the project directory aborts the pipeline immediately

These limits are enforced in code, not just policy.

---

## What Gets Logged

Every run produces entries in three logs:

| Log | What's in it |
|-----|-------------|
| `experiments.md` | Experiment records in canonical format |
| `actions.md` | What external actions were taken and when |
| `reasoning.md` | Why decisions were made |

The public version of experiment records appears in this repository under `experiments/`.

---

*The full implementation lives in frAInk-core (private). This document describes the architecture without exposing implementation details.*
