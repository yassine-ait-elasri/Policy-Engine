# Control Model — Phase 1

This document defines the **security control model** used by the Policy Engine.

It explains what a control is, what it is not, how controls are evaluated, and how they relate to enforcement mechanisms.  
The control model is the **core abstraction** of the entire system.

If this model is wrong, everything built on top of it will be fragile.

---

## 1. What a Security Control Is

In this system, a security control is:

- an **architectural requirement**
- expressed in **platform-agnostic terms**
- derived from risk and context
- enforceable through one or more mechanisms
- traceable to external standards

A control describes **what must hold true**, not **how it is implemented**.

---

## 2. What a Security Control Is Not

A control is **not**:

- a tool
- a runtime rule
- a configuration snippet
- a vendor feature
- a compliance checkbox

Controls do not reference:
- Kubernetes objects
- Terraform resources
- OPA Rego code
- Cloud provider services

Those belong to adapters and enforcement layers.

---

## 3. Purpose of the Control Model

The control model exists to:

- decouple security intent from implementation
- allow controls to be written once and reused
- support multiple enforcement backends
- enable deterministic reasoning
- make security decisions explainable

Controls are the **stable core** of the system.

---

## 4. Control Structure (Conceptual)

Each control is defined by a fixed set of conceptual elements.

### 4.1 Control Identity

Every control has:
- a unique identifier
- a short, stable name
- a severity level

The identifier is immutable and used for traceability across:
- reports
- enforcement artifacts
- standards mapping

---

### 4.2 Applicability Conditions

Applicability defines **when a control is relevant**.

Applicability is based on:
- component type
- exposure level
- environment classification
- data sensitivity
- trust relationships

Applicability is evaluated **before** compliance.

If a control does not apply, it is excluded explicitly.

---

### 4.3 Security Requirement

The requirement defines **what “secure” means** in abstract terms.

It must be:
- binary in intent (holds / does not hold)
- independent of platform specifics
- testable through static or runtime checks

The requirement never embeds implementation logic.

---

### 4.4 Risk Rationale

Each control includes a rationale that explains:
- what risk it mitigates
- what failure mode it addresses
- why the requirement exists

Rationale is used for:
- reporting
- audit explanation
- decision justification

---

### 4.5 Standards Mapping

Controls may reference one or more external standards:
- CIS
- NIST
- ISO 27001
- others as needed

Standards references are **informational**, not authoritative.

The control definition remains independent of any single framework.

---

## 5. Control Evaluation Model

Controls are evaluated statically against the Normalized Infrastructure Model.

---

### 5.1 Evaluation States

Each evaluation produces exactly one of:

- **COMPLIANT**  
  The requirement is explicitly satisfied.

- **NON-COMPLIANT**  
  The requirement is explicitly violated.

- **UNKNOWN**  
  There is insufficient information to decide.

UNKNOWN is a **valid and expected** outcome.

---

### 5.2 Evaluation Rules

- Missing information never implies compliance.
- Unresolved values result in UNKNOWN.
- Defaults are never assumed.
- Evaluation never mutates the model.

Evaluation is deterministic.

---

## 6. Control Scope

Controls apply to **specific scopes**, such as:
- individual components
- sets of components
- relationships between components

Scope is determined by applicability conditions, not by enforcement artifacts.

---

## 7. Exceptions and Deviations

### 7.1 Nature of Exceptions

An exception represents a **deliberate deviation** from a control.

Exceptions are:
- explicit
- justified
- scoped
- time-independent (in Phase 1)

Exceptions are not inferred.

---

### 7.2 Ownership of Exceptions

The Policy Engine exclusively owns:
- exception decisions
- exception justification
- exception scope

Enforcement mechanisms only apply declared exceptions.

---

## 8. Control vs Enforcement

Controls and enforcement are **intentionally separated**.

```

CONTROL (abstract intent)
↓
ADAPTER (platform translation)
↓
ENFORCEMENT (runtime check)

```

A single control may have:
- multiple enforcement mechanisms
- or none, if enforcement is not supported

Lack of enforcement does not invalidate the control.

---

## 9. Enforcement Intent

For each non-compliant or unknown control, the engine defines an **enforcement intent**, such as:
- enforce strictly
- enforce with exceptions
- report only

Enforcement intent is a policy decision, not a runtime one.

---

## 10. Metadata and Traceability

Every control contributes metadata used across the system:
- control identifier
- severity
- rationale
- standards references

This metadata propagates into:
- reports
- enforcement artifacts
- audit evidence

Metadata is never used to influence enforcement logic.

---

## 11. Control Lifecycle

In Phase 1, controls are:
- statically defined
- versioned implicitly with the engine
- immutable during execution

Later phases may introduce lifecycle management, but Phase 1 does not.

---

## 12. Determinism and Stability

Given the same inputs:
- the same controls apply
- the same evaluations occur
- the same outcomes are produced

Control behavior does not depend on:
- runtime state
- external services
- platform APIs

---

## 13. Why This Model Matters

This control model ensures that:

- security intent is explicit
- implementation remains interchangeable
- enforcement is explainable
- audits are defensible
- future platforms can be added without rewriting controls

---

## 14. Summary

The control model is the **foundation** of the Policy Engine.

It defines:
- what security means
- when it applies
- how it is evaluated
- how it is enforced indirectly

Everything else in the system exists to serve this model.
