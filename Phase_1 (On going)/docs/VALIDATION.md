# Validation — Phase 1

This document defines **what validation means** in Phase 1 of the Policy Engine and **how correctness is established**.

Validation in this project is not about proving that infrastructure is secure at runtime.  
It is about proving that **policy reasoning and enforcement artifacts are correct, consistent, and safe to apply**.

---

## 1. Purpose of Validation

Validation serves three goals:

1. Ensure generated artifacts are **technically correct**
2. Ensure decisions are **internally consistent and explainable**
3. Prevent unsafe or misleading outputs

Validation does **not** aim to:
- guarantee security outcomes
- replace runtime testing
- simulate production environments

---

## 2. Validation Scope

Phase 1 validation applies to:

- generated Terraform artifacts
- generated enforcement configuration
- policy-to-enforcement consistency
- decision traceability

Validation explicitly excludes:
- runtime behavior guarantees
- cloud provider correctness
- performance or availability impacts

---

## 3. Validation Layers

Validation is performed in **layers**, each with a clearly defined responsibility.

```

Policy Decisions
↓
Artifact Generation
↓
Static Validation
↓
Optional Local Enforcement Validation
↓
Reporting

```

Each layer builds on the previous one.

---

## 4. Decision Consistency Validation

### 4.1 Purpose

This validation layer ensures that **policy decisions are coherent** before any artifacts are generated.

---

### 4.2 What Is Validated

- Every enforced control was selected by the engine
- Every enforcement artifact maps to a defined control
- Enforcement intent matches control severity
- Exceptions are explicit and justified
- UNKNOWN evaluations are preserved and reported

---

### 4.3 Failure Conditions

Validation fails if:
- an enforcement artifact exists without a corresponding control
- a control is enforced outside its declared scope
- an exception is applied without justification
- UNKNOWN is silently treated as COMPLIANT

---

## 5. Terraform Static Validation

### 5.1 Purpose

Terraform validation ensures that generated artifacts are **syntactically and structurally correct**.

---

### 5.2 Validation Methods

- Terraform syntax validation
- Terraform planning

These checks ensure:
- valid Terraform configuration
- resolvable references
- correct provider usage

---

### 5.3 Explicit Limitations

Terraform validation does not:
- apply infrastructure changes
- verify runtime behavior
- detect semantic security issues

Terraform is used strictly as a static validation tool.

---

## 6. Enforcement Artifact Validation

### 6.1 Purpose

This step ensures that enforcement artifacts are:
- complete
- internally consistent
- compatible with the enforcement engine

---

### 6.2 What Is Validated

- OPA Gatekeeper installation configuration
- Constraint definitions and parameters
- Scope definitions (namespaces, resource kinds)
- Metadata completeness

---

### 6.3 What Is Not Validated

- Business correctness of policies
- Adequacy of chosen controls
- Organizational risk tolerance

These remain human decisions.

---

## 7. Optional Local Enforcement Validation

### 7.1 Purpose

Optional local validation demonstrates that:
- enforcement artifacts function as expected
- invalid workloads are rejected
- valid workloads are allowed

This step is **demonstrative**, not required.

---

### 7.2 Execution Model

- Use a local Kubernetes cluster (e.g., kind or minikube)
- Apply generated enforcement artifacts
- Attempt to deploy known-compliant and known-non-compliant workloads

No cloud credentials are required.

---

### 7.3 Outcome Interpretation

Local enforcement validation:
- increases confidence
- does not prove production safety
- does not replace formal testing

Failure in this step does not invalidate policy reasoning but highlights enforcement mismatches.

---

## 8. Handling UNKNOWN During Validation

UNKNOWN is a first-class outcome.

Validation rules:
- UNKNOWN must be visible in reports
- UNKNOWN must not block artifact generation
- UNKNOWN must not be auto-resolved

UNKNOWN indicates uncertainty, not failure.

---

## 9. Error Classification

Validation errors are classified as:

- **Decision errors**: inconsistent or invalid policy decisions
- **Generation errors**: missing or malformed artifacts
- **Terraform errors**: syntactic or planning failures
- **Enforcement errors**: incompatible enforcement configuration

Errors are explicit and stop the pipeline when safety is compromised.

---

## 10. Validation Output

Validation produces:

- a pass/fail status per validation layer
- a list of detected issues
- references to affected controls
- guidance on where the failure occurred

Validation output is part of the final report.

---

## 11. Determinism and Reproducibility

Given the same inputs:

- validation results are identical
- failures occur at the same stage
- outputs remain stable

Validation does not depend on:
- time
- network access
- external services

---

## 12. Why Validation Is Structured This Way

This validation model ensures that:

- policy reasoning remains trustworthy
- enforcement artifacts are safe to review
- users are never misled by false confidence
- failures are explainable and actionable

Validation protects **correctness**, not **outcomes**.

---

## 13. Summary

In Phase 1, validation answers one question:

**“Is what we generated consistent with the decisions we made, and safe to apply?”**

It does not answer:
- “Is this infrastructure secure?”
- “Will this prevent all incidents?”

Those questions belong to later phases and human judgment.
