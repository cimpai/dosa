# DOSA

**Decision-Oriented Systems Architecture***

DOSA defines systems where:  
- transitions exist only with proof (witness)
- correctness is constructed, not validated
- execution is derived from admissibility

Start here:  
- docs/core/DOSA_CORE.md

DOSA is not a framework.  
It is a protocol for building systems that make decisions under uncertainty.

---

## Status

**RFC-0 (Draft)**

This repository defines the initial protocol, concepts, and reference structure
for DOSA. The specification is expected to evolve.

---

## What is DOSA?

DOSA is a protocol-first approach to building decision-making systems.

It separates:

- **belief** (what the system thinks is true)
- **constraints** (what the system is allowed to do)
- **decisions** (what the system chooses to do)

and makes each of them explicit, observable, and controllable.

---

## Why DOSA?

Most modern systems:

- mix inference with action
- rely on implicit assumptions
- lack explicit constraints and invariants
- cannot control uncertainty or exploration

DOSA introduces:

- **explicit decision modeling**
- **constraint-first execution**
- **controlled uncertainty (Bayesian reasoning)**
- **governed exploration**
- **system stability as a first-class concern**

---

## Core Principles

- **Constraint-first** — constraints have veto power over decisions
- **Belief ≠ Action** — knowing something does not imply acting on it
- **Models are sensors** — AI models provide signals, not decisions
- **Deterministic before probabilistic** — invariants first, uncertainty second
- **Exploration is controlled** — information is acquired under constraints
- **Safety = Stability** — systems must remain within valid state space
- **Fail-closed systems** — absence of certainty leads to safe degradation

---

## Decision Node

The minimal unit in DOSA is a **Decision Node**.

Each node follows a structured loop:

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

This loop is **explicit, traceable, and governed**.

---

## Layered Model

DOSA separates computation into layers:

- **Deterministic layer**
    - invariants
    - rules
    - hard constraints

- **Probabilistic layer**
    - uncertainty
    - belief updates
    - Bayesian reasoning

- **Semantic layer**
    - unstructured input processing
    - model-based signal extraction

---

## Layer Coupling

DOSA explicitly controls how information flows between layers.

The system defines:

- how fast beliefs update
- when beliefs affect policy
- how noise is filtered
- how stability is preserved

This prevents systems from becoming either:

- **too rigid** (glass)
- **too unstable** (fluid)

---

## Safety Envelope

DOSA does not rely on reactive "kill switches".

Instead, it defines a **Safety Envelope**:

> the bounded region of valid system states and transitions

Any decision outside this region is:

- blocked
- corrected
- or degraded into a safe mode

---

## Exploration

DOSA supports controlled exploration.

Exploration is:

- not random
- not implicit

It is:

> a deliberate investment in reducing uncertainty

and is allowed only when:

- risk is bounded
- actions are reversible
- expected value of information is positive

---

## Repository Structure

This repository follows a **protocol-first design**:

- `docs/spec/` — canonical specifications (RFC, coupling, safety, math)
- `schemas/` — machine-readable contracts
- `packages/core/` — minimal runtime primitives
- `packages/math/` — mathematical foundations
- `packages/reference-node/` — reference implementation
- `rfcs/` — evolving proposals

---

## License

This project is licensed under the Apache License 2.0.

See [LICENSE](./LICENSE) for details.

---

## License Philosophy

DOSA is released under Apache 2.0 to encourage:

- open adoption
- interoperability
- independent implementations

The goal is to establish DOSA as an open protocol
for decision-oriented systems.

---

## Trademark

"DOSA" and "CimpAI" refer to this project and ecosystem.

Use of the name must not imply endorsement by the authors.

---

## Contributing

DOSA is evolving.

See:

- `docs/spec/RFC-0.md`
- `docs/spec/RFC-PROCESS.md`

for how the protocol is defined and extended.

---

## Vision

DOSA aims to provide:

> a common protocol for systems that reason, decide, and act under uncertainty

similar to how:

- HTTP defines communication
- SQL defines data access

DOSA defines **decision-making systems**.
