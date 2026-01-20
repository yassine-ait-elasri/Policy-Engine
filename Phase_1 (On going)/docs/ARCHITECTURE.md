# Architecture — Phase 1

This document describes the **conceptual and logical architecture** of the Policy Engine for Phase 1.

It focuses on **responsibility boundaries**, **information flow**, and **decision ownership**, not on tools or implementation details.

The architecture is intentionally designed to:
- separate reasoning from enforcement
- remain platform-agnostic at the core
- support future extension without refactoring existing controls

---

## 1. Architectural Overview

The system is organized into **three strictly separated layers**:

1. **Security Strategy Layer (Core Engine)**
2. **Implementation Adapter Layer**
3. **Enforcement / Runtime Layer**

Each layer has **exclusive responsibilities**.  
No layer is allowed to take over decisions that belong to another.

```

┌──────────────────────────────────────────┐
│   Security Strategy Layer (Engine)       │
│                                          │
│  • Architecture interpretation           │
│  • Control applicability                 │
│  • Risk and context reasoning             │
│  • Standards mapping                     │
│  • Exception decisions                   │
└──────────────────────────────────────────┘
                    ↓
┌──────────────────────────────────────────┐
│   Implementation Adapter Layer           │
│                                          │
│  • Platform translation                  │
│  • Terraform artifact generation         │
│  • Parameter binding                     │
│  • Metadata injection                    │
└──────────────────────────────────────────┘
                    ↓
┌──────────────────────────────────────────┐
│   Enforcement / Runtime Layer            │
│                                          │
│  • Policy evaluation                     │
│  • Allow / deny decisions                │
│  • Violation reporting                   │
└──────────────────────────────────────────┘

```

---

## 2. Security Strategy Layer (Core Engine)

### 2.1 Purpose

The Security Strategy Layer is the **decision-making brain** of the system.

It answers **architectural questions**, not runtime questions.

This layer determines:
- which security controls apply
- why they apply
- what risk they mitigate
- how compliance is evaluated
- which exceptions are legitimate

All reasoning in this layer is **platform-agnostic**.

---

### 2.2 Inputs

The engine consumes:
- statically parsed Terraform configuration
- an optional context profile (environment, data sensitivity)

It does **not** consume:
- Terraform state
- runtime telemetry
- platform APIs

---

### 2.3 Normalized Infrastructure Model

All inputs are transformed into a **Normalized Infrastructure Model (NIM)**.

The NIM:
- abstracts away provider-specific resource types
- represents infrastructure as components and relationships
- encodes architectural intent and uncertainty

Typical component concepts include:
- workload
- identity
- network boundary
- entry point
- secret material
- control-plane element

Each component carries **contextual attributes**, such as:
- exposure level
- trust boundaries
- environment classification
- data sensitivity

Terraform constructs that cannot be resolved statically result in **UNKNOWN values**, which are preserved explicitly.

---

### 2.4 Control Selection and Evaluation

Controls are evaluated in two steps:

1. **Applicability**  
   Determines whether a control is relevant to a given component and context.

2. **Compliance Evaluation**  
   Determines whether the control is:
   - COMPLIANT
   - NON-COMPLIANT
   - UNKNOWN

UNKNOWN is treated as a valid state indicating insufficient information, not as an error.

---

### 2.5 Decision Ownership

The Security Strategy Layer exclusively owns decisions about:
- control applicability
- risk classification
- standards mapping
- exception justification
- remediation intent
- overall compliance interpretation

No other layer is allowed to override or infer these decisions.

---

## 3. Implementation Adapter Layer

### 3.1 Purpose

The Implementation Adapter Layer translates **abstract policy decisions** into **concrete enforcement artifacts**.

It does not make security decisions.

Its role is translation, not reasoning.

---

### 3.2 Adapter Abstraction

An adapter is responsible for:
- mapping abstract controls to platform-specific enforcement mechanisms
- binding parameters and scope
- generating Terraform artifacts
- attaching traceability metadata

Adapters are **stateless** and deterministic.

---

### 3.3 Phase 1 Adapter: Kubernetes via Terraform

Phase 1 implements a single adapter:
- Kubernetes enforcement expressed through Terraform

This adapter generates:
- Terraform resources using Kubernetes and Helm providers
- OPA Gatekeeper installation artifacts
- Constraint definitions with parameters

The adapter does **not**:
- infer architectural context
- decide enforcement severity
- evaluate compliance

---

### 3.4 Metadata Injection

Adapters attach metadata to **policy artifacts**, not application workloads.

Metadata captures:
- control identifiers
- severity
- standards references
- rationale
- engine version
- generation timestamp

Metadata exists for traceability and reporting, not enforcement logic.

---

## 4. Enforcement / Runtime Layer

### 4.1 Purpose

The Enforcement Layer performs **binary evaluation** at runtime.

It answers:
- “Is this specific object allowed?”

It does not answer:
- “Should this control exist?”
- “Why does this matter?”
- “What is the overall security posture?”

---

### 4.2 OPA Gatekeeper

OPA Gatekeeper is used as the enforcement engine.

OPA:
- evaluates individual Kubernetes objects
- applies constraints deterministically
- produces allow/deny outcomes
- emits violation information

OPA does **not**:
- select controls
- reason about architecture
- classify risk
- map standards
- generate remediation

OPA enforces what it is given.

---

## 5. Boundary Between Engine and Enforcement

The boundary between the Security Strategy Layer and the Enforcement Layer is strict.

```

If a decision requires understanding WHY → Engine
If a decision is a yes/no check on an object → Enforcement

```

Examples of decisions **never delegated** to enforcement:
- which controls apply
- risk severity
- compliance scoring
- exception justification
- platform translation

---

## 6. Exception Handling Model

Exceptions are handled as **explicit design decisions**.

The engine:
- decides whether an exception exists
- records justification and scope
- embeds exceptions into enforcement parameters

The enforcement layer:
- applies the exception mechanically
- does not invent or infer exceptions

Self-service workload exemptions are explicitly disallowed.

---

## 7. Reporting Integration

The architecture supports reporting as a **first-class output**.

The report is generated by the engine and includes:
- applied controls
- missing controls
- unknown controls
- rationale and risk explanations
- standards coverage

Enforcement violations are inputs to reporting, not the report itself.

---

## 8. Architectural Constraints

The architecture enforces the following constraints:

- No enforcement logic in the core engine
- No reasoning logic in enforcement
- No platform assumptions in controls
- No runtime dependencies for reasoning
- No implicit decisions

These constraints are intentional and non-negotiable.

---

## 9. Extensibility

The architecture supports extension by:
- adding new adapters
- adding new controls
- expanding reporting

Existing controls and reasoning logic remain unchanged when new platforms are added.

---

## 10. Architectural Summary

This architecture ensures that:

- policy decisions are deterministic
- enforcement is consistent
- reasoning is explainable
- platforms remain interchangeable
- future growth does not require rework

It is designed to scale in **capability**, not in complexity.
