# DOSA Safety Envelope

Status: Draft  
Version: 0.1  
Authors: CimpAI Authors  

---

## 0. Abstract

The Safety Envelope defines the bounded region of valid system states and transitions in DOSA.

A DOSA system is considered operationally valid only while operating within this envelope.

Safety in DOSA is structural, not reactive.

Behavior outside the Safety Envelope must not continue normal operation.

---

## 1. Motivation

Most systems treat safety as an external mechanism:

- post-hoc validation
- guardrails
- reactive shutdown

This leads to:

- delayed failure detection
- unsafe intermediate states
- inconsistent system behavior

DOSA introduces a different model:

Safety is not added to the system.  
Safety defines the system.

---

## 2. Definition

The Safety Envelope defines the admissible region of operation.

It is the set of all admissible:

- states
- transitions
- actions

within which a DOSA system may operate.

States outside this region are invalid and must trigger fail-closed behavior.

A system operating outside this set is in an invalid state.

See §6.5 for the distinction between invalid state and invalid system.

### 2.1 State Definition

For the purpose of the Safety Envelope, system state includes:

- belief state
- node-local state (mode, internal parameters)
- pending actions or decisions
- observable external context required for validation

The exact structure of state is defined by schemas.

---

## 3. Core Principles

### 3.1 Safety as a Structural Property

Safety is not a feature or module.

It is a property of valid system configuration and operation.

---

### 3.2 Admissibility of State

Every system state must lie within the Safety Envelope.

States outside the envelope are invalid and must not be accepted.

---

### 3.3 Invariant Transitions

Every transition between states must preserve safety invariants.

Invalid transitions must not be executed.

---

### 3.4 Fail-Closed Operation

If the system cannot guarantee that a state or transition is safe:

- it must not proceed
- it must degrade or halt

---

### 3.5 No Undefined Behavior

There is no valid execution defined by the protocol outside the Safety Envelope.

This applies to normative system behavior, not to physical system failures.

---

## 4. Scope

The Safety Envelope constrains:

- belief validity
- policy admissibility
- decision legality
- execution safety
- feedback integration

It applies across the full decision lifecycle.

The Safety Envelope is defined at the node level.

Composition of envelopes across multiple nodes is out of scope of this document.

---

## 5. Envelope Dimensions

The Safety Envelope may be defined across multiple dimensions.

---

### 5.1 State Constraints

Define valid system states.

Examples:

- belief bounds
- consistency conditions
- invariants

---

### 5.2 Action Constraints

Define valid actions.

Examples:

- allowed operations
- permission boundaries
- domain constraints

---

### 5.3 Transition Constraints

Define valid state transitions.

Examples:

- rate limits
- monotonicity
- bounded change

---

### 5.4 Risk Constraints

Define acceptable levels of risk.

Examples:

- maximum uncertainty
- expected loss thresholds

---

### 5.5 Observability Constraints

Define required visibility.

Examples:

- required signals
- minimum confidence
- traceability

---

### 5.6 Reversibility Constraints

Define whether actions must be reversible.

Examples:

- rollback capability
- compensation mechanisms

---

### 5.7 Parameter Semantics

Safety Envelope parameters may operate over normalized domains:

- uncertainty ∈ [0, 1]
- confidence ∈ [0, 1]
- expected loss ∈ [0, 1]

Other parameters (e.g. rates, counts) must define explicit units.

Time is measured in discrete decision cycles unless otherwise specified.

---

## 6. Boundary States

System operation relative to the envelope can be classified as:

---

### 6.1 Inside

All constraints are satisfied.

Normal operation.

---

### 6.2 Near Boundary

Constraints are close to violation.

Near-boundary conditions are defined relative to constraint slack.

Exact thresholds are implementation-specific and defined in schemas or policies.

System should increase monitoring and reduce aggressiveness.

---

### 6.3 Boundary Breach

Constraints are violated.

Normal operation must stop.

---

### 6.4 Outside

System is in an invalid operational state.

Only safe recovery or shutdown is allowed.

---

### 6.5 Invalid State vs Invalid System

A distinction is made between:

- invalid state: a state outside the Safety Envelope during operation
- invalid system: a system that cannot enforce the Safety Envelope

Invalid states must trigger fail-closed behavior.

Invalid systems are not considered DOSA-compliant.

---

## 7. Boundary Behavior

When approaching or crossing the boundary, the system must:

- reduce coupling influence
- limit exploration
- restrict action space
- switch to conservative mode
- block unsafe transitions
- perform rollback where possible

---

## 8. Fail-Closed Principle

If safety cannot be verified:

- the system must not execute actions
- the system must degrade into a safe mode
- or halt execution

Fail-open behavior is not allowed.

---

## 9. Relationship to Coupling

Coupling controls how belief influences behavior.

The Safety Envelope defines where behavior is allowed.

Coupling operates within the Safety Envelope.

If coupling would push the system outside the envelope:

- influence must be reduced
- or blocked

Constraints defined by the Safety Envelope have priority over coupling.

---

## 10. Failure Modes

---

### 10.1 Silent Breach

System violates constraints without detection.

---

### 10.2 Delayed Breach

Violation occurs after unsafe execution.

---

### 10.3 Uncontrolled Propagation

Unsafe state spreads through the system.

---

### 10.4 False Recovery

System returns to operation without restoring safety guarantees.

---

## 11. Observability

The system must expose:

- current envelope state
- boundary proximity
- breach events
- safe mode transitions
- rollback events

Each event must include:

- node identifier
- protocol version
- timestamp (decision cycle index)
- relevant constraints
- action or transition attempted

---

## 12. Illustrative Configuration

Example configuration:

```
safety_envelope:
  max_uncertainty: 0.3
  max_expected_loss: 0.1
  max_action_rate: 10  # actions per decision cycle
  min_confidence: 0.8
  require_reversibility: true
```

This configuration is illustrative only.

Normative schemas are defined in /schemas.

---

## 13. Non-Goals

The Safety Envelope does not define:

- domain-specific business logic
- specific constraint implementations
- model architectures

---

## 14. Summary

The Safety Envelope defines the admissible space of system operation.

A DOSA system:

- must operate within this space
- must not execute outside it
- must degrade or halt if safety cannot be guaranteed

Safety in DOSA is a property of existence, not behavior.

A system that cannot satisfy the Safety Envelope is not a valid DOSA system.
