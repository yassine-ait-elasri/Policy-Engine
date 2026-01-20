# Data Flow — Phase 1

This document describes the **end-to-end information flow** of the Policy Engine in Phase 1.

It explains how data moves through the system, how decisions are derived, and how uncertainty is handled.  
The focus is on **reasoning order**, **data ownership**, and **decision boundaries**.

---

## 1. Overview

Phase 1 follows a **strict, linear data flow**.

Each stage:
- consumes well-defined inputs
- produces explicit outputs
- does not rely on side effects
- preserves uncertainty

No stage performs hidden inference or runtime lookups.

---

## 2. High-Level Flow

                                                    Terraform Configuration
                                                              ↓
                                                    Static Parsing
                                                              ↓
                                                    Normalized Infrastructure Model
                                                              ↓
                                                    Control Applicability Analysis
                                                              ↓
                                                    Compliance Evaluation
                                                              ↓
                                                    Control Plan
                                                              ↓
                                                    Terraform Enforcement Generation
                                                              ↓
                                                    OPA Gatekeeper Artifacts
                                                              ↓
                                                         Validation
                                                              ↓
                                                          Report


Each step is described in detail below.

---

## 3. Terraform Configuration Input

### 3.1 Input Characteristics

The primary input is a Terraform configuration directory.

Characteristics:
- declarative
- static
- provider-agnostic at the syntax level
- potentially incomplete or parameterized

The configuration may contain:
- variables
- locals
- modules
- expressions
- unresolved values

No Terraform state is used.

---

### 3.2 Trust Model

Only **explicitly declared configuration** is trusted.

Any value that:
- depends on variables
- depends on module outputs
- depends on runtime evaluation

is treated as **UNKNOWN**.

UNKNOWN values are propagated through the pipeline.

---

## 4. Static Parsing

### 4.1 Purpose

Static parsing extracts the **declared structure** of the configuration.

It does not attempt to:
- evaluate expressions
- resolve variables
- expand modules

---

### 4.2 Output

The parser produces a raw structural representation:
- resource declarations
- references between resources
- declared attributes
- unresolved expressions

This representation is an intermediate artifact and is not used for reasoning.

---

## 5. Normalized Infrastructure Model (NIM)

### 5.1 Purpose

The Normalized Infrastructure Model (NIM) is the **canonical representation** used for all reasoning.

It abstracts away:
- provider-specific resource types
- syntactic differences
- tooling concerns

---

### 5.2 Model Content

The NIM represents infrastructure as:

- abstract components
- relationships between components
- contextual attributes
- explicit uncertainty markers

Examples of abstract components:
- workload
- identity
- network boundary
- entry point
- secret material
- control-plane component

---

### 5.3 Context Propagation

Context is attached at normalization time and includes:
- exposure level
- environment classification
- data sensitivity
- trust boundaries

If context cannot be inferred, it is marked as UNKNOWN and preserved.

---

## 6. Control Applicability Analysis

### 6.1 Purpose

This stage determines **which controls apply** to which components.

It does not evaluate compliance.

---

### 6.2 Decision Inputs

Applicability is based on:
- component type
- exposure level
- environment
- data sensitivity
- declared relationships

No platform-specific knowledge is used at this stage.

---

### 6.3 Output

The output is a set of:
- component–control pairs
- with explicit applicability justification

Controls that do not apply are excluded explicitly.

---

## 7. Compliance Evaluation

### 7.1 Purpose

This stage evaluates **whether applicable controls are satisfied**.

---

### 7.2 Evaluation Rules

For each applicable control:
- evaluate static conditions against the NIM
- never assume defaults
- never infer missing information

---

### 7.3 Evaluation Outcomes

Each evaluation produces one of:
- COMPLIANT
- NON-COMPLIANT
- UNKNOWN

UNKNOWN indicates insufficient information, not partial compliance.

---

## 8. Control Plan Construction

### 8.1 Purpose

The control plan is the **contract** between reasoning and implementation.

It defines:
- which controls must be enforced
- where enforcement applies
- which parameters are required
- which exceptions exist

---

### 8.2 Plan Content

For each control in the plan:
- control identifier
- target scope
- enforcement intent
- required parameters
- exception definitions
- traceability metadata

The plan is complete and self-contained.

---

## 9. Terraform Enforcement Generation

### 9.1 Purpose

This stage translates the control plan into **Terraform artifacts**.

---

### 9.2 Generation Rules

- No existing Terraform code is modified
- All output is additive
- Platform translation is delegated to adapters
- Enforcement logic is not embedded in the core engine

---

### 9.3 Output

Generated artifacts include:
- Terraform configuration for enforcement infrastructure
- OPA Gatekeeper installation artifacts
- Constraint definitions with parameters and metadata

---

## 10. OPA Gatekeeper Artifacts

### 10.1 Role

OPA Gatekeeper artifacts define **runtime enforcement behavior**.

They:
- encode binary evaluation logic
- scope enforcement to defined targets
- apply parameters and exceptions

---

### 10.2 Limits

OPA artifacts:
- do not select controls
- do not evaluate risk
- do not compute compliance metrics
- do not generate remediation

OPA evaluates objects, nothing more.

---

## 11. Validation

### 11.1 Purpose

Validation ensures that generated artifacts are:
- syntactically correct
- internally consistent
- safe to apply

---

### 11.2 Validation Steps

- Terraform syntax validation
- Terraform planning
- Optional local Kubernetes enforcement test

Validation does not include deployment in cloud environments.

---

## 12. Reporting

### 12.1 Purpose

Reporting provides **human and machine-readable explanations**.

---

### 12.2 Report Content

The report includes:
- applicable controls
- enforcement status
- non-compliant findings
- unknown evaluations
- rationale for each decision
- standards mapping

OPA violation events are referenced but not treated as authoritative.

---

## 13. Error and Uncertainty Handling

Errors are classified as:
- parsing errors
- normalization errors
- generation errors

Uncertainty is never treated as an error.

UNKNOWN is preserved and surfaced explicitly.

---

## 14. Determinism and Reproducibility

Given the same inputs:
- the same control plan is produced
- the same Terraform artifacts are generated
- the same report is emitted

No stage depends on runtime state or external APIs.

---

## 15. Data Flow Summary

Phase 1 enforces a disciplined flow:

- architecture first
- decisions second
- enforcement last
- explanation always

This structure ensures that security policy engineering remains deterministic, auditable, and extensible.
