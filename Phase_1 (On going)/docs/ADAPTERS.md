# Adapters — Phase 1

This document defines the **adapter layer** of the Policy Engine.

Adapters are responsible for translating **abstract security decisions** into **concrete enforcement artifacts** for a specific platform.

They are deliberately constrained.  
They translate decisions; they do not make them.

---

## 1. Purpose of the Adapter Layer

The adapter layer exists to solve a single problem:

**How does a platform-agnostic security control become enforceable on a specific platform?**

Adapters allow:
- security logic to remain stable
- enforcement mechanisms to vary
- platforms to be added without rewriting controls

Without adapters, platform-agnostic policy engineering is not possible.

---

## 2. Adapter Responsibilities

An adapter is responsible for:

- translating abstract controls into platform-specific enforcement
- generating Terraform artifacts
- binding scope and parameters
- injecting traceability metadata
- declaring unsupported or partial enforcement explicitly

Adapters must be:
- deterministic
- stateless
- idempotent

Adapters must not:
- infer architectural context
- decide which controls apply
- classify risk
- resolve unknown values heuristically
- modify existing infrastructure code

---

## 3. Adapter Position in the Architecture

                              Abstract Control Model
                                       ↓
                              Control Plan (Engine Output)
                                       ↓
                              Adapter Translation
                                       ↓
                              Terraform Enforcement Artifacts
                                       ↓
                              Runtime Enforcement (OPA, etc.)


Adapters consume **decisions**, not raw infrastructure descriptions.

---

## 4. Adapter Inputs

Each adapter receives a **Control Plan** from the Policy Engine.

The control plan contains:
- control identifier
- enforcement intent
- target scope
- parameters
- explicit exceptions
- metadata for traceability

Adapters do not re-evaluate controls.

---

## 5. Adapter Outputs

Adapters produce **Terraform artifacts** only.

These artifacts may include:
- enforcement infrastructure
- policy definitions
- configuration parameters
- metadata annotations

All outputs are:
- additive
- non-destructive
- isolated from user-defined Terraform code

---

## 6. Terraform as the Adapter Interface

Terraform is used as the **common output interface** for adapters.

Reasons:
- declarative and deterministic
- platform-agnostic at the workflow level
- locally testable
- extensible via providers

Adapters never:
- read Terraform state
- modify user Terraform files
- depend on remote APIs

---

## 7. Phase 1 Adapter: Kubernetes

Phase 1 implements a **Kubernetes adapter** using Terraform.

This adapter translates abstract controls into:
- Kubernetes admission enforcement
- namespace-level and cluster-level policies
- identity and access constraints
- network isolation rules

The adapter uses Terraform providers to express these artifacts.

---

## 8. Kubernetes Adapter Responsibilities

The Kubernetes adapter is responsible for:

- installing OPA Gatekeeper
- generating constraint instances
- scoping enforcement by:
  - namespace
  - resource kind
  - labels or selectors
- applying explicit exceptions
- attaching metadata to policy artifacts

The adapter does not:
- inspect Kubernetes runtime state
- infer cluster topology
- override engine decisions

---

## 9. Constraint Generation Strategy

In Phase 1:

- ConstraintTemplates are treated as a maintained library
- The adapter generates Constraints only
- Rego logic is static and version-controlled

This ensures:
- reviewability
- predictability
- security of enforcement logic

---

## 10. Metadata Injection

Adapters inject metadata into policy artifacts to ensure traceability.

Metadata includes:
- control identifier
- severity
- standards references
- rationale
- engine version
- generation timestamp

Metadata is never used by enforcement logic.

---

## 11. Handling Unsupported Controls

Not all controls can be enforced on all platforms.

Adapters must explicitly declare:
- unsupported controls
- partially enforceable controls
- controls that can only be reported

Unsupported does not mean ignored.

The engine reports these cases clearly.

---

## 12. Adapter Error Handling

Adapter errors are limited to:
- invalid control plans
- missing required parameters
- unsupported enforcement combinations
- Terraform generation failures

Adapters must not:
- silently skip controls
- downgrade enforcement implicitly
- infer defaults

Errors are surfaced to the engine.

---

## 13. Adding New Adapters

Adding a new adapter must not require:
- changes to existing controls
- changes to control evaluation logic
- changes to reporting semantics

Only the adapter implementation changes.

This is a hard architectural requirement.

---

## 14. Adapter Evolution Rules

Adapters may evolve to:
- support new enforcement mechanisms
- support new platform capabilities

Adapters must not:
- change control meaning
- reinterpret severity
- alter risk rationale

Control meaning is immutable.

---

## 15. Adapter Summary

The adapter layer ensures that:

- security intent remains stable
- platforms remain interchangeable
- enforcement remains explicit
- reasoning remains explainable

Adapters are translators, not decision-makers.

This separation is essential to the integrity of the Policy Engine.
