# Scope — Phase 1

This document defines the **explicit scope and boundaries** of Phase 1 of the Policy Engine project.

Its purpose is to prevent ambiguity, scope creep, and misinterpretation of what the system does and does not do at this stage.

Phase 1 is intentionally limited.  
Those limits are **design decisions**, not temporary gaps.

---

## 1. Objective of Phase 1

Phase 1 establishes the **foundational policy engineering capability**.

The objective is to prove that:

- security controls can be derived deterministically from infrastructure descriptions
- those controls can be expressed in a platform-agnostic way
- enforcement artifacts can be generated consistently via Terraform
- every decision can be explained and traced

Phase 1 is successful if the engine can reason correctly, not if it covers every possible platform.

---

## 2. What Phase 1 Explicitly Includes

Phase 1 includes the following capabilities.

### 2.1 Infrastructure Input

- Accept Terraform configurations as the primary input
- Parse Terraform statically (no state access)
- Support unresolved values, variables, and modules
- Allow an optional context profile to declare intent Terraform cannot express

Terraform is treated as a **declarative specification**, not as an execution record.

---

### 2.2 Platform-Agnostic Reasoning

- Normalize infrastructure into abstract components
- Reason about:
  - exposure
  - identity
  - data sensitivity
  - environment
  - trust boundaries
- Select applicable security controls based on this normalized model

No platform-specific assumptions are allowed at this stage.

---

### 2.3 Security Control Model

- Define security controls once, in abstract terms
- Each control includes:
  - applicability conditions
  - definition of secure vs insecure
  - risk rationale
  - standards references
- Controls do not reference specific technologies or vendors

Controls are treated as **policy intent**, not enforcement rules.

---

### 2.4 Compliance Evaluation

- Evaluate each applicable control statically
- Produce one of three outcomes:
  - COMPLIANT
  - NON-COMPLIANT
  - UNKNOWN
- Treat UNKNOWN as a valid and expected result

UNKNOWN indicates lack of information, not correctness.

---

### 2.5 Implementation Adapter (Phase 1)

Phase 1 implements **one adapter only**:

- Kubernetes enforcement via Terraform

The adapter:
- translates abstract controls into Kubernetes enforcement mechanisms
- generates Terraform artifacts using Kubernetes and Helm providers
- installs and configures OPA Gatekeeper
- injects traceability metadata

The adapter does not make security decisions.

---

### 2.6 Enforcement Integration

- Use OPA Gatekeeper strictly as a runtime evaluator
- Generate constraints and parameters, not strategy
- Enforce allow/deny decisions at admission time
- Log violations for reporting

OPA is an enforcement component, not a policy engine.

---

### 2.7 Outputs

Phase 1 produces:

- Terraform enforcement packages (additive overlays)
- OPA Gatekeeper installation artifacts
- Constraint definitions with parameters
- A human-readable explanation of decisions
- A machine-readable report for analysis

All outputs are non-destructive.

---

### 2.8 Validation

Phase 1 validation is local and static:

- Terraform syntax validation
- Terraform planning
- Optional local Kubernetes enforcement demonstration

Cloud credentials are not required.

---

## 3. What Phase 1 Explicitly Excludes

The following are **out of scope by design**.

### 3.1 Deployment and Automation

- No automatic application of Terraform changes
- No CI/CD integration
- No rollback or drift detection

Phase 1 generates artifacts, it does not execute them.

---

### 3.2 Runtime Monitoring

- No runtime security monitoring
- No behavioral analysis
- No alerting or SIEM integration

OPA enforcement events are inputs to reporting only.

---

### 3.3 Cloud-Specific Services

- No reliance on paid cloud services
- No assumption of AWS, Azure, or GCP availability
- No cloud billing integration

Phase 1 must run fully offline.

---

### 3.4 Exhaustive Standards Coverage

- No attempt to fully implement CIS, NIST, or ISO frameworks
- Only a limited, representative control set is required
- Coverage is demonstrative, not exhaustive

Correct reasoning is prioritized over quantity.

---

### 3.5 Business Logic and Governance Decisions

- No automated risk acceptance
- No automated compliance scoring for business reporting
- No approval workflows
- No policy exceptions inferred at runtime

All such decisions remain human-driven.

---

## 4. Non-Goals (Permanent)

The following are **not goals** of this project, in Phase 1 or later.

- Replacing OPA
- Replacing Terraform
- Acting as a vulnerability scanner
- Acting as a runtime security platform
- Automatically fixing infrastructure without review

---

## 5. Success Criteria for Phase 1

Phase 1 is considered successful if:

- security controls are defined once and reused
- Kubernetes enforcement artifacts are generated correctly
- all decisions are explainable and traceable
- the system operates fully offline
- future adapters can be added without changing the core logic

---

## 6. Transition to Later Phases

Phase 1 establishes:

- the control abstraction
- the reasoning model
- the adapter boundary
- the enforcement contract

Later phases may add:
- user interfaces
- deployment workflows
- additional adapters
- reporting enhancements

Those additions must not alter Phase 1 assumptions.

---

## 7. Final Statement

Phase 1 is intentionally constrained.

Its purpose is to prove that **policy engineering is possible as a deterministic, explainable discipline**, independent of any single platform.

All future complexity builds on this foundation.
