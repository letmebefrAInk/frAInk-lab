# frAInk — EXPERIMENT_PROTOCOL.md
### *How experiments are designed, executed, and evaluated.*

---

## Purpose

frAInk is an experimental AI system designed to explore how autonomous agents reason, act, and learn in real-world environments.

Experiments are the core mechanism through which frAInk evolves.

Every experiment must be **bounded, logged, and interpretable**.

The goal is not to prove the AI right.

The goal is to **learn something true.**

---

## Experiment Structure

Each experiment follows the same structure.

### 1. Hypothesis

What are we trying to learn?

Example:

- Can frAInk identify stronger short-term stock setups than Frank?
- Can an agent synthesize market data faster than a human researcher?
- Does structured reasoning outperform intuition in prediction markets?

Every experiment must start with a clear question.

---

### 2. Inputs

What information is available to the system?

Examples:

- Market data
- News
- Technical indicators
- Historical data
- External APIs

Experiments should clearly define what the agent is allowed to see.

No hidden information.

---

### 3. Guardrails

All experiments run inside defined limits.

Typical guardrails include:

- maximum runtime
- maximum cost
- tool access limits
- financial exposure limits
- approval requirements for real transactions

frAInk is designed to explore safely.

---

### 4. Decision Phase

Two decision paths may exist.

**Frank**

What would Frank do given the same information?

**frAInk**

What does the system decide?

Both decisions are recorded independently before outcomes are known.

This creates a natural control comparison.

---

### 5. Execution

If an experiment requires action, it is executed under defined conditions.

Examples:

- simulated trade
- prediction market entry
- research output
- automated analysis

Real financial execution may require human approval.

---

### 6. Outcome

What actually happened?

Examples:

- prediction accuracy
- profit / loss
- research quality
- time-to-solution

The result is recorded without modification.

---

### 7. Lessons

Every experiment must end with a short reflection.

Questions to ask:

- What did frAInk do well?
- Where did it fail?
- What surprised us?
- What should change in the next experiment?

Learning matters more than winning.

---

## Scoreboard

Experiments may include a simple comparison: