# DOSA CORE

## Table of contents

0. [One-pass model](#0-one-pass-model) - [Minimal example](#minimal-example), [Boundary of the system](#boundary-of-the-system)
1. [Problem space](#1-problem-space)
2. [Axiomatic foundation](#2-axiomatic-foundation)
3. [Formal architecture](#3-formal-architecture)
4. [Implementation mechanisms](#4-implementation-mechanisms)
5. [Dynamics and evolution](#5-dynamics-and-evolution)
6. [Verification and security](#6-verification-and-security)
7. [Socio-economic interaction](#7-socio-economic-interaction)
8. [Constructive interpretation (Curry-Howard layer)](#8-constructive-interpretation-curry-howard-layer)

---

## 0. One-pass model

DOSA defines systems in which:

- a transition exists only when it is constructed together with a witness of its admissibility;
- a state exists only when it is a value of an admissible type;
- execution is derived from admissible transitions;
- inadmissible transitions do not "fail with an error" - in the system's ontology they are not there.

Everything else in this document refines that idea.

### Minimal example

Given:

- State `A`

Candidate:

- `compute(B)`

Transition **exists iff**:

- ∃ `B`, `witness(A -> B)`

If witness cannot be constructed:

- -> no transition exists  
- -> system state does not change

In one line: computing a new value is not enough - without building a witness the step does not enter the system's history, and DOSA state does not change.

### Code intuition (informal)

```ts
type Transition<S, Next> = {
  next: Next
  witness: unknown
}

// If construction fails, the system does not get an invalid transition;
// it gets no transition at all.
function step<S, Next>(current: S, intent: unknown): Transition<S, Next> | null {
  const out = tryBuildWitness(current, intent)

  if (!out) {
    return null // no transition exists
  }

  return { next: out.next, witness: out.witness }
}
```
Without a witness, a value does not become a transition.

### Boundary of the system

DOSA does not claim that **every** physical event maps to a transition in the model. Only effects that admit a **canonical** representation as evidence and can be used to build a witness for an admissible step enter the system's ontology. Everything else stays **outside** the system: it is not relied on for execution, or it is mere observation without a change of DOSA state, or it is a signal for formal migration (rebuilding the admissible space and admissibility rules).

## 1. Problem space

*Why this matters: phenomenology of failures, hard environmental limits, gaps in linear and procedural models.*

### 1.1. Phenomenology of failure

Modern systems decide in a space where execution is separated from constraints, and rules are implicit - in code, people's heads, documents, and processes. That yields drift: behavior can match code and data formally yet violate business, safety, or common-sense invariants. Correctness is checked after the fact (logs, tests, review), not built into the very possibility of execution, so errors scale with the rate of change. In distributed and AI-heavy systems this worsens: decision trajectories become opaque, provenance is lost, and "being right" stops being a locally checkable property - it is smeared across services, models, and people.

The gap is a single, formalized space of admissible decisions that is simultaneously executable, checkable, and portable across nodes. Invariants and constraints must be primary geometry of the system, compiled into a checkable artifact, and every transition must carry proof of admissibility and origin. Changes must happen only through controlled transformations of that geometry, without implicit patches and hidden drift, and data transfer must preserve path continuity, integrity, and version consistency. Otherwise execution speed inevitably outruns the system's ability to stay correct, and error cost grows faster than optimization gains.

Systemic collapses appear when execution trajectories stop being bounded by explicit invariants and start optimizing local metrics away from global correctness. That shows up as silent drift (valid by code and data yet destructive to business meaning, safety, or compliance); provenance loss (cannot reconstruct why a decision was taken); policy bypass (locally admissible service actions compose into an inadmissible path); cross-service inconsistency (nodes take incompatible decisions from rule and version skew); Goodhart-style overfitting to metrics. In AI loops this is amplified by hallucinated authority and implicit transitions: the model acts outside admissible space while infrastructure cannot filter it locally.

A separate class is evolution collapse: uncontrolled rule changes create topological defects - partial upgrades and shadow governance, schema and protocol drift, replay ambiguity, orphaned states. Migrations stretch into long inconsistency windows with cascade risk; rollback becomes nondeterministic; safety degrades to "trust the network". The system may keep running and even scaling while internally incorrect until a sharp collapse - incidents, leaks, losses, or sanctions that cannot be localized or explained quickly.

### 1.2. Boundary conditions

Any system hits irreversible physical limits: finite channel capacity, signal latency, measurement error, component failure, energy and memory bounds. Full observability, instant synchronization, and zero failure probability are not assumptions; the map between logical state and physical realization is always approximate and must use tolerances, quorums, and degradation modes. Admissible trajectories must tolerate partial availability, delay, and noise, and guarantees must be invariants with explicit bounds (epsilon, timeouts, consistency levels), not idealized assumptions.

Economic and legal limits are just as hard: resources and budgets are finite; error cost scales with integration depth and failure odds; change has a price (engineering, migration, downtime), and the system should optimize total cost of decisions, not raw speed. Regulatory constraints - privacy, safety, auditability, log immutability, retention and deletion, jurisdiction - cannot be bypassed by local optimization. Those limits shape admissible space: decisions and migrations must be demonstrably compatible with resource budgets and normative invariants, or the system exits the region of legitimate, stable operation.

### 1.3. Gap analysis

Linear and procedural models treat control as a phase (validation, review, tests), not as a property of the execution space. Rules are scattered across code, services, and processes; they are not compiled into one admissible space and therefore are not locally checkable at every step. Correctness becomes after-the-fact: the system acts first, then is checked; service composition yields trajectories that are "allowed separately but inadmissible together". Without explicit topology and formal invariants, provenance is brittle, rule versions desynchronize, and safety depends on trusting the environment rather than on proof of transition admissibility.

Those models also lack discipline for changing the system's shape: evolution happens through patches and partial updates, creating shadow rules, incompatible versions, and orphan states. Migrations stretch in time, producing long inconsistency windows where replay and audit lose determinism and rollback becomes nondeterministic. Economically that raises error and integration debt; legally it raises compliance risk when origin and correctness of actions cannot be proved. Without a compilable representation of constraints and proof portability across nodes, the system cannot guarantee that admissible steps stay inside bounds - it only chases violations that already happened.

---

## 2. Axiomatic foundation

*Root of trust: primitives, immutable axioms, ontology (what state and correctness are).*

### 2.1. Primitives

The axiomatic base is a minimal set of primitives from which the whole system is built and checked: State (typed representation of state), Transition (deterministic state transformation), Invariant (a constraint on states and transitions that must not be broken), Evidence (observable representation of inputs and effects), Decision (choice of an admissible transition from evidence and policy), and Identity/Authority (cryptographically anchored source of permission to act). Every transition must be expressed as a composition of these primitives, and admissibility must be a function of invariants and provable input properties. Primitives are implementation-independent and form a closed alphabet: richer constructs are compositions that preserve invariants.

Proof and portability primitives sit on top: Commitment (cryptographic binding of content), Proof/Signature (proof of admissibility and origin), Envelope (separation of payload and proof), Topology (space of admissible states and transitions), and Version/Migration (formal transformation of topology). The root of trust is not a list of rules but an algorithm that validates compositions of primitives so every admissible transition carries checkable proof and preserves invariants under local verification. The system is a composition of proof-carrying transitions over a fixed primitive set; correctness is by construction, not after the fact.

### 2.2. Global axioms

Global axioms are immutable "laws of physics" for the system, bounding its existence regardless of protocol version. They include: admissibility (every reachable state lies in a formally specified space), integrity preservation (data and their representation cannot change without a cryptographically checkable transformation), provenance (every transition has checkable proof of source and path continuity), composition (combining admissible transitions stays admissible under assembly rules), and observability (every material change leaves a checkable trace suitable for replay and audit). These axioms are not tuned per deployment for local optimization - they define the geometry of admissible states.

Environmental limits also act as axioms: finite resources and time (no infinite computation or instant global sync), partial observability and noise (measurements are approximate and need tolerances), no trust without proof (every step is locally checkable), and monotonic history (recorded facts are not retroactively rewritten without an explicit protocol transformation). These stay fixed across migrations: parameters and compositions change, not the laws. Behavior that violates them hits topological incompatibility - it cannot be compiled into a valid execution space.

### 2.3. Ontology

#### Central thesis of DOSA

**A transition exists if and only if it can be constructed together with its witness.**

In the DOSA model a transition exists **if and only if** admissibility is **exhibited** together with it: logical and cryptographic proof / envelope / commitment carriers relative to a fixed space specification. Without that construction there is **no transition** in the system's ontology - only a physical effect not fixed in admissible space. This is not primarily a security slogan but a claim about **what counts as an element of system history**.

#### Event vs validation wording

**A transition is not an event that is later validated.**

**A transition is an event that exists only together with its witness.**

**Without a witness, there is no transition - only an unrecognized physical effect.**

**Corollary.** System history is not a log of events but a **constructible path** in admissible space. *System history is not a record of events, but a constructible path in the admissible space.*

#### Minimal kernel (no metaphors)

The same ontology in compact form; everything else in the document derives from this skeleton (topology migrations, envelope, quorums, economics as metric on the space, and so on).

- `S` is state; `T: S -> S` is a transition; `I` is an invariant (often on state; step constraints can extend `S` or use a predicate on triples).
- `Verify(witness, S, T, S')` (or equivalent) is checkable evidence for a step relative to a fixed space specification.
- Inductively: `valid(S')` iff `I(S')`, there is an admissible step from some `S` with witness, and `valid(S)` (plus a base for initial states).
- **Execution** searches (generates candidates for) admissible transitions in the above sense; without successful witness the step does not enter history.

#### State and correctness

State is canonical, typed, committed representation at a time, including domain values, dependencies, and minimally sufficient history (commitments) for replay. It is a point in topology and is defined not only by values but by origin: a state owns a provable chain of transitions that produced it. State is not identical to raw memory - it is a validatable projection of physical implementation, allowing observation slack (epsilon) and formal consistency models (quorums, versions, time windows). A state exists only if it can be expressed with primitives and mapped to an admissible node or coordinate in topology.

Correctness is membership in admissible space plus checkable proof of membership. Formally: a state is correct iff (1) it satisfies all global and local invariants, (2) it is reached from a correct predecessor by an admissible transition, and (3) it carries checkable proof of path continuity and integrity (commitments / signatures). Correctness is not a post-hoc boolean flag - it is constructed with the state: a transition either yields a new correct state with proof, or it does not exist. Mismatch between logical model and physical realization is correctness failure and demands halt or formal topology migration.

---

## 3. Formal architecture

*Control topology: geometry of the admissible, second-order operations, orthogonality of governance and execution.*

### 3.1. Defining the space

Admissible states form a formally specified manifold built from types, invariants, and transition rules. Each point is canonical state; admissible moves are directed edges from admissible transitions; curvature comes from constraints that make some directions costly or unreachable. Global axioms fix topological invariants (connectivity, no forbidden pockets, closure under admissible composition); local constraints form atlases and charts (subspaces / contexts); metric (cost, risk, epsilon slack) sets admissible distances and trajectory stability under partial observation and noise. The system does not live in an arbitrary state space but in a strict geometry where admissible states and paths are elements of that manifold in advance.

Space definition is a compilable specification: invariants and assembly rules induce a language of admissible trajectories, and geometry is fixed in a checkable artifact that supports local verification of point membership and transition admissibility. Any execution trajectory is a curve on the manifold carrying proof of admissibility and continuity; forbidden regions need no explicit runtime check - they are topologically unreachable. Changing the shape of space is allowed only as formal manifold transformation (migration) that preserves or consciously redefines invariants; partially valid intermediate shapes are excluded. Control geometry is primary: execution is navigation, correctness is membership.

### 3.2. Transformation rules (second-order operations)

Transformation rules are second-order operations - admissible changes to the geometry itself, not motion inside it. Formally they are maps between manifolds, taking topology at version v to topology at v+1 while preserving root axioms and provable invariants: add/remove nodes and edges, refine types, tighten or loosen local bounds (within allowed parameters), recompose modules, change metric (cost / epsilon). Each operation is a composition of primitives with proved invariant preservation, explicit pre/post conditions, and closure in a transformation algebra (idempotence, invertibility / partial invertibility, commutativity where allowed). Operations that bridge into forbidden regions, break connectivity of admissible space, or break provable origin and integrity are not in the algebra.

Each transformation goes through second-order compile and verify: a new geometry artifact is built and global axioms and composition rules are proved or refuted. Migration is atomic application with a clear activation boundary and no partially valid intermediate states; coordinated transition strategies (versioning, compatibility, state maps from v to v+1) are allowed, not "shadow" edits. The system separates fast motion inside topology from rare, tightly controlled changes to topology itself: evolution is a formally bounded operation, not accreted patches, avoiding drift and topological defects.

### 3.3. Orthogonality

Orthogonality means the control layer sets admissible geometry without computing values inside it. Execution works on states and transitions (computes payload); governance supplies admissibility as external but compilable constraint structure. They meet through a strict interface: every execution transition carries admissibility proof checked against compiled topology; rules are not reinterpreted each step but applied as a precomputed verifier. Computation and validation differ in semantics and runtime schedule but are tied by contract: no transition exists without checkable conformance to geometry.

That separation lets layers evolve independently: execution can optimize, scale, and change without altering admissible shape while it honors the proof contract; governance changes only through controlled topology transformations and recompilation. Rule logic is not embedded in business code, and data does not earn the right to continue a path without external proof of admissibility. The control layer is immutable relative to current execution (like physical law); execution is free inside those laws; the joint is a strict, checkable boundary, not a blend.

---

## 4. Implementation mechanisms

*Compilation to artifacts, isomorphism loop, protocol envelopes.*

### 4.1. Compilation

Compilation turns high-level invariants and composition rules into canonical constraint representation suitable for local, deterministic checking. The specification is normalized (types, transitions, invariants, parameters), expressed as a language of admissible trajectories, and fixed with cryptographic commitments (hashes, structure roots), yielding an immutable geometry artifact. A verifier is built that checks not "rules as text" but whether a concrete transition belongs to the compiled space. Each step gets admissibility proof: input/output commitments, transition and version identifiers, and a signature or token binding the action to the artifact. Here the signature carries geometry: it attests the transition lies in the admissible set, not merely authenticates the sender.

At runtime a node emits payload with separate proof (envelope); the receiver runs local checks: data integrity, topology and protocol version alignment, transition correctness against the compiled artifact, and path continuity. Missing or bad proof means the transition does not exist. Rule updates happen only by recompilation and releasing a new artifact with a clear activation boundary; partially valid intermediate shapes are excluded. Invariants become computationally mandatory: satisfaction is enforced by proof construction and checked **locally in time that does not grow with history depth** for a fixed compiled artifact and bounded per-step witness size - not by reinterpreting full rule text and post-hoc gatekeeping on every step.

### 4.2. Isomorphism loop

The isomorphism loop continuously binds the logical model to physical embodiment through a formal perception and check pipeline. Physical state (memory, network, storage, timing, replicas) is encoded as canonical evidence with explicit epsilon slack, quorums, and time windows, then committed and matched to expected topology invariants. Each transition uses that evidence as proof input: step validity depends on rules and on confirmed match of material realization to logical state. The loop is part of topology (not external monitoring): it closes proof onto physics so mismatch between "should be" and "is" is detectable and formally readable as admissible vs inadmissible state.

On isomorphism failure the system does not coast: it stops the transition (fail-stop) or starts formal degradation or migration that rebuilds admissible space given observed reality. Loop invariants include observation continuity, checkable measurement provenance, version alignment, and monotonic history; measurements carry cryptographic commitments and participate in transition proofs. Correctness is "logic times matter": execution proceeds only while isomorphism holds within given tolerances; the system is a self-checking loop where physical realization is part of proof.

### 4.3. Protocol envelopes

A protocol envelope is canonical wrapping that separates meaning and proof: payload (domain data) and proof envelope (admissibility and integrity). The envelope fixes execution context as immutable commitments: payload_hash, input_digest, transition_id, node_id, topology_version, protocol_version, path_commitment (aggregate of prior steps), timestamp or logical clocks, and cryptographic signature. It does not store rules; it materializes their outcome: this transition belongs to compiled space and data were not altered. Payload stays the pure meaning carrier; proof is a separate, tightly bound layer for origin and trajectory continuity.

Execution is proof-carrying: a node emits data inside an envelope with proof; the receiver runs local verification without trusting the source. Envelope invariants include payload immutability (hash commitment), path continuity (no grafting steps outside the chain), topology and protocol version match, signature validity against the trust root, and transition correctness against the compiled artifact. Missing or bad proof makes the message inadmissible for continuing execution. The node graph is an execution graph where every edge carries portable proof; the right to take the next step is not trust but locally checkable admissibility.

---

## 5. Dynamics and evolution

*Runtime: transitions, consistency of "shape", migration protocol.*

### 5.1. Transitions

Transition A to B is atomic transformation of canonical state under an admissible operator parameterized by evidence and bounded by topology invariants. Mechanically: choose admissible operator (Decision) `->` compute next state by deterministic Transition `->` compute input/output commitments `->` build admissibility proof (proof) tying the transition to current topology version and path continuity. A transition exists only if source state is in admissible space, all global and local invariants hold, and there is checkable proof that A to B is in the compiled set of admissible transitions.

Transition execution emits a protocol envelope with payload separated from proof; receiver-side checking is local and **bounded in complexity by step size and artifact size**, not by full state space. Missing or bad proof means no transition; partial effects are forbidden (atomicity); history is append-only; replay under the same inputs yields the same result (determinism within declared epsilon). Mismatch between logical model and physical state, as seen by the sensor loop, blocks the transition or triggers formal degradation or migration. System dynamics is navigation on the admissible manifold by proof-carrying steps; correctness is constructed with the transition, not validated after the fact.

### 5.2. Consistency model

Consistency is preservation of shape (geometry of admissible states) under high-speed motion. Locally each transition is validated against the compiled topology artifact with proof of admissibility and path continuity; globally, commitments and quorums make a state admissible when its representation fits epsilon slack and enough independent observations or replicas agree. History is append-only; topology and protocol versions are explicit in proofs; causality is carried by commitment chains (path_commitment), so each state is locally checkable without a centralized oracle. "Shape" is preserved not by synchrony but by immutable invariants and portable proofs that bound admissible trajectories.

Under concurrency the system allows multiple partial views but bounds divergence via formal merge rules: commutative operations, deterministic conflict resolution, invariant-preserving composition. Nodes accept only messages whose envelopes prove version compatibility and path continuity; unprovable branches are dropped or isolated. Beyond tolerances, controlled mechanisms apply: degradation (narrow admissible transitions), quorum reassessment, or formal topology migration. The system stays consistent "in shape" under asynchrony and high throughput because correctness is built from proofs and merge rules, not global locks.

### 5.3. Migration protocol

Migration protocol is formal topology at v to topology at v+1 where change of system shape is a second-order function with provable invariant preservation (or explicit redefinition). Source and target geometries are canonicalized; a state map v to v+1 and a language of admissible transitions for the new version are built; verification checks global axioms, module composition correctness, and absence of bridges into forbidden regions. The result is a new compiled artifact defining admissible space for v+1 with compatibility rules for v (admissible transitions and their proofs). Migration forbids implicit patches: any change is an explicit geometry operation with pre/post conditions and formal proof linking versions.

Activation is atomic via versioned switch and controlled compatibility window: nodes accept only envelopes proving membership in admissible space (v or v+1) and path continuity through allowed version transitions. Parallel coexistence is limited by strict merge and deduplication rules; unprovable or stale branches are isolated. Partially valid states are excluded: either the old artifact or the new one applies; intermediate forms do not exist. On incompatibility, formal rollback is inverse transformation or a new migration. Topology updates avoid invariant violation and long inconsistency windows: correctness is by construction, not post-hoc policing.

---

## 6. Verification and security

*Properties, threats, formal proofs.*

### 6.1. Correctness properties

The system supports dependable operation and security through correctness-by-construction: shape preservation (every reachable state lies in admissible space), path continuity (every transition has checkable proof of origin and topology membership), data integrity (payloads bound to commitments and cannot change without breaking proof), and local verifiability (each step checked independently and deterministically against the compiled artifact). Missing proof means no transition, not "invalid after the fact"; forbidden regions are topologically unreachable. History is append-only; versions are fixed; causality is in commitment chains, enabling replay and audit without trusting the environment.

Continuous safety closes on physical reality and controlled evolution: the isomorphism loop aligns logical model to material state within epsilon and on mismatch triggers fail-stop or formal degradation or migration; compilation turns rules into an immutable verifier with **local per-step checking that does not require scanning history** for a fixed artifact; versioned migrations change geometry only through provable transformations, excluding partially valid states and drift. Together this states **intent** for safety and liveness in the distributed-systems sense: the system must not enter forbidden states (safety) and must be able to take admissible steps when correct inputs and proofs exist (liveness). Concrete safety and liveness theorems and their strength **depend on an explicit failure model** (crash vs Byzantine, network and timing assumptions) and must be stated per implementation; here they are carried by proof construction, not after-the-fact control.

### 6.2. Threat model

The threat model assumes distrust of every node, channel, and input: attackers forge messages, break ordering and causality, coordinate Byzantine nodes, and attempt "topology attacks" - injecting inadmissible transitions, bridging into forbidden regions, or swapping geometry versions. Defense is proof-carrying protocol: each step must carry checkable proof of admissibility and path continuity tied to input/output commitments and current topology version; nodes accept only envelopes that pass local verification without trusting the source. Quorums and signature thresholds limit forgery and single-point failure; commitment chains block grafting foreign states; explicit versioning blocks rule skew. Invariant bypass does not land in "bad state" - it yields no transition.

Physical faults (partial outage, data corruption, delay, partition) use degradation invariants: epsilon, quorums, and deterministic merge preserve shape under asynchrony; beyond bounds the system goes fail-stop or restricted mode and produces no unprovable effects. Evolution "topology attacks" (incompatible changes, shadow rules, partial upgrades) are blocked because geometry changes only through formally verified migration with a new compiled artifact; nodes accept only transitions that prove version compatibility and admissibility of state maps. Safety is structural: under Byzantine behavior and damage the system either continues inside provable trajectories or stops or isolates branches without entering forbidden regions.

### 6.3. Formal proofs

Formal correctness is specified over state and transition space with safety and liveness invariants and provable composition. A language of admissible trajectories (type system / transition logic) requires each state and step to carry proof of membership; proofs are checked locally and compositionally (proof-carrying transitions). Safety is unreachability of forbidden regions (invariant preservation); liveness is existence of admissible continuations when inputs exist (progress). Correctness by construction holds because second-order operations (topology change) are allowed only with proved preservation of global axioms and closure under composition; checking reduces to a finite set of invariant-preservation and assembly theorems, not exhaustive state enumeration.

In practice this combines model checking and proofs in types: specification in formal logic (e.g. temporal safety and liveness), checked on finite abstractions (model checking), fixed as invariant-preserving constructors (type guarantees) so "incorrect" states do not typecheck and cannot be built. Cryptographic commitments bind proofs to concrete transitions and topology versions for replay and local verification without trust. Together: any realizable trajectory either carries valid proof and lies in admissible space, or cannot be constructed or accepted - violations are ruled out at the possibility of execution, not after the fact.

---

## 7. Socio-economic interaction

*Metrics, human (Architect), scaling without losing axiomatic integrity.*

### 7.1. Metric alignment

Metric alignment maps economic signals into parameters of admissible geometry: observed quantities (revenue, cost, risk, SLA, penalties, demand) are encoded as evidence, normalized, and projected via formal functions into space metric (edge weights, penalties, epsilon slack, transition priorities). Economic value does not drive actions directly; it bends the space where actions are possible: favorable trajectories get "shorter" (lower cost or risk), unfavorable ones get "steeper" or unreachable. Invariants cap that projection: no metric may weaken base constraints (safety, compliance, integrity); influence flows only through admissible topology parameters and invariant-preserving maps.

Alignment dynamics close the loop: measure `->` normalize `->` project into geometry `->` compile or update parameters `->` execute with proofs `->` feedback. Nodes accept parameter changes only if provably compatible with current topology version and if they create no bridges into forbidden regions; out-of-range signals trigger degradation or formal migration. Economics becomes structure of space, not external pressure: the system optimizes trajectories without breaking invariants, and cost and value are measurable motion on the admissible manifold.

### 7.2. Human in the loop

The human (Architect) sits at meta-control: not computing transitions but defining the space where transitions are possible. The role is to state and refine invariants, metric parameters, and second-order transformation rules, translating external goals and limits (business, law, ethics) into canonical system geometry. In hierarchy the Architect is a source of hypotheses about topology change: propose admissible transformations, set acceptance criteria and epsilon bounds, then drive compilation and formal verification. The Architect does not "drive" execution in the small; they construct conditions under which correct execution is the only admissible option.

In recursive systems-of-systems this level repeats at each scale: local architects govern subspaces, inherit global axioms and parameters from above, and their decisions obey the same rules of provable composition and migration. Feedback closes through observations and economic signals: deviations (pressure on bounds, rising cost, quality drop) read as need to change geometry, but every change follows formal protocol and cannot be smuggled "around the side". Humans remain the source of goals and interpretation but cannot implicitly break invariants: influence flows only through provable space transformations, not direct meddling in execution order.

### 7.3. Scalability

Scalability is fractal composition: the same axiomatic core (invariants, provable composition rules, proof protocol) applies at every level and subspace. Each subsystem has local topology inheriting global axioms and compiling to its own checkable artifact; subsystem interaction uses protocol envelopes carrying admissibility proof and version compatibility. Module composition is invariant-closed: if A and B are correct and their interface obeys assembly rules, their union is correct by construction. The system scales as a union of isomorphic fragments without centralizing verification: correctness is local; global shape is preserved inductively.

Growth does not blur axioms because shape change is allowed only as formal transformation at the right level and is checked by the same second-order rules. Local optimizations (metric, epsilon, quorums) stay inside allowed ranges and cannot weaken global constraints; new hierarchy levels appear with clear boundaries and agreement protocols. Load spreads across space via parallelism and independent regions; consistency is carried by portable proofs and deterministic merge, without global locks. The system grows fractally: more nodes and deeper composition, but axiomatic integrity holds at every scale and for every local operation.

---

The sections above describe DOSA as **geometry of the admissible** and **ontology of transitions**: what counts as state, how topology is structured, how constraints compile, and how proof-carrying envelopes bind execution to governance. What follows is a **constructive reading** of the same model: state, transition, and admissibility in Curry-Howard terms (propositions as types, proofs as programs or terms), leading to an **executable core** - DSL, compiled artifacts, and runtime where the existence of a step is the same as the ability to build a witness. This does not replace the ontology of sections 1-7; it is a **second layer of description** of the same discipline: from manifesto to implementation bridge.

## 8. Constructive interpretation (Curry-Howard layer)

*Bridge to implementation: types, inhabitation, witness, DSL, and runtime without redefining envelope and topology from the core.*

### 8.1. Why Curry-Howard for DOSA

Curry-Howard is not decoration: in DOSA correctness cannot be an external phase after action. Re-reading **propositions as types** and **proofs as terms** lets a step be not "compute then check" but a construction whose existence already includes admissibility proof: an inadmissible step is not "caught" - it **does not appear** in the space of typable trajectories. That is the same ontology as in section 2.3 and section 5.1 in constructive existence language.

For DOSA this is not a literal import of full lambda calculus into distributed systems; it is a **shift of lens**: "to exist in the system" is approximately "to be typable with constructive witness"; everything else is outside execution ontology. The central thesis of section 2.3 in this lens is the **iff** between transition existence and constructible witness (see also section 8.5).

### 8.2. Existence as constructiveness (link to ontology)

The distinction "happened physically" vs "exists as a transition" is already fixed in section 2.3. Here is the logical gloss: in a constructive setting you cannot assert existence without a way to exhibit the object. "A transition exists" means "there is a construction realizing the step **and** its admissibility" - formally close to **inhabitation** of the transition type by a witness term. Classical existence without a witness is not the model for a system event.

### 8.3. Semantic frame: propositions, proofs, typechecking

An invariant reads as a **proposition** of admissibility; a transition type states **what** must be proved for the step to exist; a **witness** is a term inhabiting that type. Admissibility checking reduces to checking that a construction correctly types, linking prior state, operator, and result.

Separation of **value** and **event** (see section 4.3): payload does not exhaust the event; the event is payload **plus** inhabitation of the admissibility type. That removes the class "valid by data, inadmissible by rules" without a separate after-the-fact layer.

DOSA does **not** reduce to pure type theory: asynchrony, network, noise, probability need outer layers. But the **admissibility core** remains: everything admissibility-related is a typable construction with checkable inhabitation; the rest is canonical evidence at the boundary (section 4.2, section 8.9) or outside transition ontology.

### 8.4. State as typable object

State is a canonical object **inhabiting** `StateType`, carrying not only data shape but admissibility conditions (aligned with section 2.3, "State and correctness"). A configuration outside the type is not "bad state" - it is **not DOSA state**. Logical state is separated from raw memory or wire bytes: recognition follows canonization, typing, and **lineage** (already in the core). The state set is not all observable bytes but **typable, provably admissible** objects; this is a second language tying state space to topology from section 3.1.

### 8.5. Transition as a type with witness

A function `S -> S'` only describes value computation; a **DOSA transition** needs a type that includes **admissibility conditions** against topology, invariants, and lineage (section 5.1). Hence **"can compute S'"** vs **"can construct a transition"**: the latter needs a term inhabiting the admissible step type. Intuition: `exists nextState` with witness inseparable from proof of step correctness; DSL syntax may hide the pair "result + proof"; semantics match the **central thesis of section 2.3**.

### 8.6. Invariants as propositions (stratified proof)

An invariant in a document or external test can be violated and caught later; in DOSA it is a **mandatory proposition** for inhabiting the relevant types: without proof there is **no** assembly of state or step. Invariants differ (structural, domain, protocol, version, resource); full static proof of everything is not required: each maps to an explicit **proof mechanism** - static typing, runtime evidence, cryptographic commitments / envelope (sections 4 and 6). One admissibility frame without slipping back to implicit rules.

### 8.7. Topology as grammar of inhabitation

Topology is not diagram metaphor but **closed grammar** of state types, transition types, and composition rules: what can be constructed. A "forbidden region" is non-inhabitation of the corresponding judgment or type; reachability is constructive build. Link to DSL (section 8.11): the language must express that grammar in canonical checkable form; compilation yields inhabitation-check artifacts and proof / envelope schemes - executable topology aligned with sections 3-4.

### 8.8. Composition and "admissibility category" discipline

Composing steps needs a **compatible** witness for the whole chain, not only locally per edge (otherwise global history breaks). You want a sensible identity step, associativity of composition at the proof-chain level, and preservation of invariants and lineage. A strict category on day one is optional; **composition discipline** is mandatory (sections 3.2, 6.1).

### 8.9. Evidence and dependent-style boundary

Many admissibility propositions are **not** derived from types in a vacuum: quorum, signature, SLA, epsilon, model output arrive as **evidence** from the environment. Admissibility is a function both of step structure and of canonized, checkable observations (section 4.2). **Dependent types** intuition: step type may depend on concrete evidence; witness is built only from admissible canonical form. A full dependent language for general-purpose programming is **not** required: a restricted, implementable fragment in the DSL is enough.

### 8.10. Probabilistic and AI components: proposal vs admissibility

Models and randomness do **not** create transitions by themselves: they generate **candidates** (proposals). A transition appears only after building a witness against topology and lineage (sections 2.3, 5.1). Nondeterminism stays in the search layer; **existence** stays in the constructive layer (the Curry-Howard core stays intact).

### 8.11. DSL: three description layers

The DSL specifies an **admissible world**, not a "convenient config": state and transition types, invariants, evidence, composition, topology versions, and migrations (semantics as in sections 3-5). Explicit split: **declarative** layer (ontology / topology), **operational** layer (payload evaluators without mixing admissibility), **proof layer** (witness and evidence requirements). Goal: a restricted, **compilable** language with enough expressiveness for typechecking, runtime verification, and envelope, without claiming to be a universal programming language.

### 8.12. Compilation: verifier artifacts

Compilation (section 4.1) in this layer: normalize types and transitions, attach propositions to evidence sources, canonical transition and version identifiers. Output is **not** necessarily "a program" in the usual sense but layered artifacts: static guarantees where possible, runtime validators for contextual evidence, envelope and lineage construction rules. Pipeline: **DSL `->` normalized model `->` type and verification artifacts `->` runtime verifier `->` envelope schema**.

### 8.13. Runtime: checkability as execution

Runtime primarily answers: **can** an admissible step be constructed from state, candidate, evidence, and topology artifact? Success `->` envelope with witness and lineage binding; failure `->` **no** system transition (sections 4.1, 5.1). Minimal loop: **Canonicalizer**, **Witness builder**, **Verifier**, **Envelope emitter**, **Lineage tracker**. Ordinary business computation stays **secondary**: until the constructive boundary is crossed it is physical process, not a DOSA trajectory element.

### 8.14. Migrations: "second-order type" transformations

Migration is not a procedural patch but **transformation of admissibility rules** (sections 3.2, 5.3): the "language" of what may exist changes. You need a **compatibility** witness across versions, a map state at v to state at v+1, and lineage continuity; otherwise shadow governance. Evolution is as proof-carrying as execution.

### 8.15. What DOSA does not promise

Fundamental environmental limits remain: physical nondeterminism, need for sensors, cost of proof, dependence of some properties on external systems and trusted modules. Many guarantees stay **hybrid** (types plus cryptography plus operational discipline). The shift is not "fully formalize the world" but **what counts as an existing event** - only constructively exhibitable with witness.

### 8.16. Minimal executable core

At minimum: typed state; finite set of admissible transitions; compact DSL for invariants and evidence dependence; envelope referencing topology version; runtime that either builds an admissible transition or denies existence. Stack: typed host language plus specialized DSL; compilation `->` validators; evidence via adapters; append-only lineage. This is **not** a workflow engine: typing, witness, and admissibility are primary; computation supplies candidates. That already realizes the thesis of section 2.3; extensions include distributed proofs, richer topology, AI as proposal layer (section 8.10).
