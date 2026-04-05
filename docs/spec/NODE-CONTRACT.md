# DOSA Node Contract

Status: Draft  
Version: 0.1  
Authors: CimpAI Authors

---

## 0. Abstract

The Node Contract defines the minimal requirements for an executable unit in DOSA.

A DOSA node is a system component that processes input, updates belief, evaluates policy and constraints, produces decisions, and integrates feedback while operating within the Safety Envelope.

This document specifies what a node must do in order to be considered DOSA-compliant.

---

## 1. Motivation

Without a standardized node contract:

- systems cannot interoperate
- behavior becomes inconsistent across implementations
- safety and coupling guarantees cannot be enforced

DOSA defines a minimal contract to ensure:

- semantic compatibility
- predictable behavior
- enforceable safety and observability

---

## 2. Definition

A DOSA node is the minimal executable unit that:

- processes evidence
- maintains and updates belief
- evaluates policy and constraints
- produces a decision or safe non-decision
- executes or emits action intent
- integrates feedback from outcomes

A node must implement the Decision Lifecycle defined in RFC-0.

A node is uniquely identifiable and operates as an independent decision unit.

---

## 3. Core Principles

### 3.1 Constraint-First Execution

A node must enforce constraints before decision and execution.

---

### 3.2 Separation of Belief and Action

Belief must not directly trigger action.

Coupling must be applied before policy influence.

---

### 3.3 Safety as Structural Requirement

A node must operate within the Safety Envelope at all times.

---

### 3.4 Fail-Closed Behavior

If safety or validity cannot be guaranteed, the node must not proceed with normal execution.

---

### 3.5 Explicit Lifecycle

All node behavior must be observable through the Decision Lifecycle stages.

---

## 4. Inputs

A node must accept the following logical inputs:

### 4.1 Required Inputs

- input or event
- current node state
- applicable policy
- applicable constraints

---

### 4.2 Optional Inputs

- parent constraints
- parent belief summary
- external control signals
- feedback from previous outcomes

Parent-related inputs are optional and implementation-specific.

This document does not define full parent-child interaction semantics or graph topology.

---

Transport mechanisms (e.g. HTTP, messaging) are out of scope.

---

## 5. Outputs

A node must be capable of producing:

- updated belief
- policy evaluation context or influence
- decision (or non-decision)
- action intent or execution request
- outcome record
- feedback artifact
- observability events
- degradation or failure signal

Not all outputs are required for every decision cycle.

The following outputs are mandatory per cycle:

- decision outcome (including non-decision)
- observability artifacts

Other outputs (e.g. action intent, execution request, feedback) are conditional on the outcome type.

---

## 6. Decision Lifecycle

A node must implement the following lifecycle:

The lifecycle is executed in discrete decision cycles.

```
Input
→ Evidence
→ Signals
→ Belief Update
→ Policy & Constraints
→ Decision
→ Execution
→ Feedback
```

Coupling is applied at the boundary between Belief Update and Policy evaluation.

See COUPLING-PROTOCOL.md for details.

Coupling is not a separate lifecycle stage, but a controlled transformation that governs how belief influences policy.

Equivalent internal implementations are allowed, provided that all stages remain externally observable.

---

## 7. Required Capabilities

A node must implement the following capabilities:

**Core**

- belief handling
- constraint evaluation
- policy evaluation
- decision selection

**Control**

- coupling application
- safety envelope enforcement

**Integration**

- feedback integration

**Observability**

- observability emission

**Protocol**

- protocol version declaration

---

## 8. Compliance Requirements

A node is considered DOSA-compliant if it:

- implements the Decision Lifecycle
- enforces constraints before execution
- applies coupling before policy influence
- enforces the Safety Envelope
- fails closed on invalid states
- emits required observability artifacts
- declares supported protocol version and capabilities

---

## 9. Failure Semantics

A node must handle the following conditions:

---

### 9.1 Invalid Input

Input cannot be processed or validated.

---

### 9.2 Invalid State

State falls outside the Safety Envelope.

See SAFETY-ENVELOPE.md §6.5 for the distinction between invalid state and invalid system.

---

### 9.3 Constraint Violation

A potential action violates constraints.

---

### 9.4 Protocol Incompatibility

Node cannot satisfy required protocol version or capabilities.

---

### 9.5 Loss of Observability

Node cannot produce required observability signals.

---

In all cases:

Normal execution must not continue without verified safety.

---

## 10. Decision Outcomes

A node must support multiple outcome types:

- successful decision
- blocked decision (constraint violation)
- degraded decision (reduced capability)
- non-decision (safe refusal)
- failure (protocol or safety violation)

Decision is not mandatory.

A valid outcome may be to not act.

---

## 11. Observability Requirements

A node must emit observable artifacts for each decision cycle.

Each artifact must include:

- node identifier
- protocol version
- decision cycle index
- belief state or reference
- constraint evaluation result
- decision outcome
- safety envelope state
- coupling mode (if applicable)
- failure or degradation events

Normative event schemas and field definitions are specified in `/schemas` and related specifications.

This section defines required observability semantics, not serialization format.

---

## 12. Capability Declaration

A node must declare supported capabilities.

Example:

```
capabilities:
  belief_update: 1.0
  constraint_evaluation: 1.0
  coupling: 1.0
  safety_envelope: 1.0
  decision_log: 1.0
```

Capability negotiation is defined in higher-level protocols.

---

## 13. Minimal Node Identity

Each node must have:

- unique identifier
- protocol version
- declared capabilities

---

## 14. Non-Goals

The Node Contract does not define:

- transport protocols
- domain-specific logic
- specific belief update algorithms
- specific decision strategies
- graph topology

---

## 15. Summary

The Node Contract defines the minimal requirements for a DOSA node.

A compliant node is not defined by its internal implementation, but by:

- explicit lifecycle
- enforced constraints
- controlled coupling
- structural safety
- observable behavior

A node that does not satisfy these requirements is not a DOSA-compliant node.
