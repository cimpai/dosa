# DOSA RFC-0

**Decision-Oriented Systems Architecture**

Status: Draft  
Version: 0.1  
Authors: CimpAI Authors  
Last Updated: 2026-04-05

---

## 0. Abstract

DOSA (Decision-Oriented Systems Architecture) is a protocol for building systems that make decisions under uncertainty.

It defines a structured decision loop, separates belief from action, enforces constraints as first-class elements, and introduces explicit control over uncertainty, exploration, and system stability.

DOSA is protocol-first and implementation-agnostic.

---

## 1. Motivation

Modern systems increasingly rely on probabilistic components, including machine learning models and LLMs.

However, most systems:

- mix inference with action
- rely on implicit assumptions
- lack explicit constraints and invariants
- cannot control uncertainty or exploration
- are difficult to reason about and debug

As a result, systems become:

- unpredictable
- hard to govern
- expensive to evolve

DOSA introduces a structured approach where:

- decisions are explicit
- constraints are enforced
- uncertainty is modeled
- system behavior is traceable

---

## 2. Design Principles

### 2.1 Constraint-first

Constraints have veto power over decisions.

A decision that violates constraints must not be executed.

---

### 2.2 Separation of Belief and Action

Belief represents what the system considers likely.

Policy and constraints determine what the system is allowed to do.

---

### 2.3 Models as Sensors

AI/ML/LLM components produce signals.

They do not make decisions.

---

### 2.4 Deterministic before Probabilistic

- deterministic constraints define valid space
- probabilistic reasoning operates within that space

---

### 2.5 Controlled Exploration

Exploration is not random.

It is allowed only when:

- risk is bounded
- actions are reversible
- expected value of information is positive

---

### 2.6 Safety as Stability

The system must remain within a valid state space.

Safety is not reactive, but structural.

---

### 2.7 Fail-Closed Systems

If the system cannot determine a safe action, it must degrade safely.

---

## 3. Core Ontology

### 3.1 Evidence

Raw input describing the environment or system state.

---

### 3.2 Signal

A structured transformation of evidence.

---

### 3.3 Belief

A representation of uncertainty over hypotheses.

---

### 3.4 Constraint

A rule that defines invalid states or actions.

---

### 3.5 Policy

A set of rules defining allowed actions given beliefs and constraints.

---

### 3.6 Decision

A selection of an action from the valid action space.

---

### 3.7 Action

An operation executed in the system or environment.

---

### 3.8 Outcome

The result of an action.

---

### 3.9 Feedback

Information derived from outcomes used to update belief.

---

### 3.10 Node

A unit that performs a full decision loop.

---

## 4. Decision Node

A Decision Node is the minimal executable unit in DOSA.

It follows a structured loop:

| Step                | Description         | Output        | Nature           |
|---------------------|---------------------|---------------|------------------|
| Input               | Raw input           | InputData     | external         |
| Evidence            | Structured facts    | Evidence      | deterministic    |
| Signals             | Derived features    | SignalSet     | probabilistic    |
| Belief Update       | State update        | BeliefState   | probabilistic    |
| Policy & Constraints| Valid actions       | ActionSpace   | deterministic    |
| Decision            | Action selection    | Decision      | mixed            |
| Execution           | Apply action        | ActionResult  | external         |
| Feedback            | Outcome             | Feedback      | external         |

---

### 4.1 Responsibilities

A node must:

- process evidence
- update belief
- enforce constraints
- select a decision
- execute actions
- record outcomes
- produce traceable logs

---

## 5. Layer Model

DOSA separates computation into layers.

---

### 5.1 Deterministic Layer

- constraints
- invariants
- rules
- veto logic

---

### 5.2 Probabilistic Layer

- belief representation
- uncertainty modeling
- Bayesian updates

---

### 5.3 Semantic Layer

- processing of unstructured input
- signal extraction from models

---

## 6. Layer Coupling Protocol

DOSA explicitly defines how information flows between layers.

---

### 6.1 Evidence → Belief

Belief updates are influenced by:

- prior belief
- new signals
- confidence
- update gain (inertia)

---

### 6.2 Belief → Policy

Belief does not immediately affect policy.

Promotion requires:

- sufficient change (delta threshold)
- confidence threshold
- persistence over time

---

### 6.3 Coupling Parameters

The system defines:

- update gain
- promotion threshold
- persistence requirement
- confidence threshold

---

### 6.4 Stability vs Adaptivity

The system must balance:

- stability (resistance to noise)
- adaptability (response to real change)

---

## 7. Decision Function

A decision is selected from valid actions:

$$
a \in A_{valid}(S)
$$

Subject to constraints:

$$
C(S, a) = true
$$

The system may optimize:

$$
\arg\max (expected\ utility + information\ value - cost)
$$


---

## 8. Exploration Protocol

Exploration is allowed under controlled conditions.

---

### 8.1 Modes

- shadow (no effect)
- replay (historical)
- limited live (bounded)

---

### 8.2 Conditions

Exploration must satisfy:

- bounded risk
- reversibility
- positive expected value of information

---

## 9. Safety Envelope

DOSA defines a Safety Envelope:

> the bounded region of valid system states and transitions

---

### 9.1 State Constraints

Define valid states.

---

### 9.2 Action Constraints

Define valid transitions.

---

### 9.3 Boundary Behavior

- near boundary → increased monitoring
- breach → block or rollback
- outside → safe mode

---

### 9.4 Fail-Closed Principle

Invalid or uncertain states must lead to safe degradation.

---

## 10. Graph Model

Nodes form a graph.

---

### 10.1 Edges

- belief flows upward
- constraints flow downward

---

### 10.2 Hierarchy

Nodes may:

- delegate decisions
- aggregate beliefs
- enforce global constraints

---

## 11. Observability

DOSA requires full traceability.

---

### 11.1 Decision Log

Each decision must be recorded.

---

### 11.2 Belief Evolution

Belief changes must be observable.

---

### 11.3 Replay

System behavior must be reproducible.

---

## 12. Non-Goals

DOSA is not:

- an LLM framework
- an orchestration tool
- a rule engine
- an ML pipeline

---

## 13. Open Questions

- causal inference integration
- adaptive coupling parameters
- distributed consistency
- learning under constraints

---

## 14. License

This specification is licensed under the Apache License 2.0.
