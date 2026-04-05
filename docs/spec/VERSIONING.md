# DOSA Versioning

Status: Draft  
Version: 0.1  
Authors: CimpAI Authors  
Last Updated: 2026-04-05

---

## 1. Scope

DOSA uses versioning to ensure:

- semantic consistency of the system
- compatibility between nodes
- safe evolution of the protocol
- interoperability across implementations

Versioning applies to:

- specification (RFC)
- protocol
- schemas
- implementations

---

## 2. Version Dimensions

DOSA defines multiple independent version axes.

---

### 2.1 Specification Version

Represents the version of the DOSA conceptual model.

Format:


RFC-<major>.<minor>


Examples:


RFC-0.1
RFC-0.2
RFC-1.0

---

### 2.2 Protocol Version

Defines compatibility of node interaction.

Covers:

- belief exchange
- constraint flow
- decision lifecycle
- coupling semantics

Format:


dosa-protocol@MAJOR.MINOR.PATCH


Example:


dosa-protocol@0.1.0

---

### 2.3 Schema Version

Defines structure of machine-readable artifacts.

Applies to:

- node
- belief
- policy
- constraint
- decision
- coupling
- safety envelope

Format:


dosa.<entity>.v<major>.<minor>


Examples:


dosa.node.v0.1
dosa.belief.v0.1
dosa.coupling.v0.1

---

### 2.4 Implementation Version

Version of a specific runtime or library.

Example:


core-runtime@0.1.0

---

## 3. Semantic Versioning Rules

Protocol and schema versions follow semantic versioning:


MAJOR.MINOR.PATCH

---

### 3.1 MAJOR

Breaking changes:

- changes in core ontology
- changes in node contract
- changes in coupling semantics
- changes in safety envelope meaning

---

### 3.2 MINOR

Backward-compatible changes:

- new optional fields
- new capabilities
- new exploration modes
- additional metadata

---

### 3.3 PATCH

Non-semantic changes:

- clarifications
- documentation updates
- bug fixes without behavior change

---

## 4. Stability Levels

Each component may declare its stability:

---

### 4.1 Experimental

- may change without notice
- not guaranteed to be compatible


stability: experimental


---

### 4.2 Draft

- intended for stabilization
- may still evolve


stability: draft


---

### 4.3 Stable

- backward compatibility expected
- changes require version bump


stability: stable


---

## 5. Compatibility Model

Compatibility must be evaluated at multiple levels.

---

### 5.1 Protocol Compatibility

Nodes are compatible if:

- protocol versions match (same MAJOR)
- required capabilities are supported

---

### 5.2 Schema Compatibility

Artifacts are compatible if:

- required fields are present
- semantics are preserved

---

### 5.3 Capability Compatibility

Nodes may declare supported capabilities:

```yaml
capabilities:
  belief_update: 1.0
  constraint_enforcement: 1.0
  exploration_modes: 0.2
```

Compatibility is determined per capability.

---

## 6. Node Handshake

Before interaction, nodes must validate compatibility.

Example:

```yaml
node_runtime:
  protocol_version: 0.1.0
  supported_capabilities:
    - belief_exchange@1.0
    - constraint_flow@1.0
    - decision_log@1.0
```

---

## 7. Evolution Rules

### 7.1 Core Ontology

The core ontology evolves slowly.

Breaking changes require a MAJOR version bump.

---

### 7.2 Protocol

The protocol must remain stable within a MAJOR version.

Extensions must be backward-compatible.

---

### 7.3 Schemas

Schemas may evolve through MINOR versions.

Fields may be added but must not be removed.

---

### 7.4 Implementations

Implementations may evolve independently.

They must declare supported protocol versions.

---

## 8. Migration

When upgrading versions:

MAJOR changes require explicit migration

MINOR changes should be transparent

PATCH changes should not affect behavior

---

## 9. Guiding Principles

- Versioning must preserve meaning, not just structure
- Compatibility must be explicit, not assumed
- Protocol stability is more important than implementation speed
- Systems must degrade safely under incompatibility

---

## 10. Summary

DOSA versioning ensures that:

- nodes can safely interact
- systems can evolve without breaking
- semantics remain consistent across implementations

Versioning in DOSA is not about code releases.

It is about preserving the integrity of decision-making systems.
