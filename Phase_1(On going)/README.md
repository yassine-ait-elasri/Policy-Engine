# Policy Engine

**Platform-agnostic security policy engineering  
with Terraform input and Terraform output**

---

## 1. What This Project Is

Policy Engine is a **security policy engineering system**.

Its purpose is to transform an **infrastructure description** into:

- a clear set of **applicable security controls**
- **enforceable implementation artifacts**
- a **traceable and explainable security report**

The project focuses on **engineering policy**, not merely enforcing it.

Security decisions are derived from architecture and context,  
not guessed at runtime and not hidden behind opaque automation.

---

## 2. The Problem Being Addressed

In real systems:

- Infrastructure changes constantly
- Security standards exist as static documents
- Enforcement tools operate at runtime, without architectural context

Between architecture and enforcement, teams rely on:
- manual interpretation
- copy-paste configurations
- inconsistent application of standards
- undocumented exceptions

This project addresses that gap by explicitly connecting:

**Architecture → Controls → Enforcement**

---

## 3. Core Design Principle

### Controls are written once  
### Implementations are mapped per platform

Security logic is **platform-agnostic**.  
Enforcement is **platform-specific**.

Terraform is used as the **common interface**:
- Terraform in: infrastructure description
- Terraform out: enforcement artifacts

This allows the engine to remain provider-agnostic while staying concrete and testable.

---

## 4. Phase 1 Scope (Strict)

Phase 1 focuses exclusively on:

- Reading Terraform configurations
- Understanding declared infrastructure intent
- Normalizing infrastructure into abstract components
- Selecting applicable security controls
- Evaluating compliance statically
- Generating enforcement artifacts through Terraform
- Producing a detailed, explainable report

Phase 1 explicitly does not include:

- Automatic deployment
- Cloud-specific paid services
- Runtime monitoring
- Continuous compliance loops
- Business decision automation

The goal is correctness and clarity, not completeness.

---

## 5. High-Level Architecture

The system is structured into three layers.

```

┌──────────────────────────────────────┐
│  Security Strategy Layer (Engine)    │
│  - Control selection                 │
│  - Risk and context reasoning        │
│  - Standards mapping                 │
│  - Exceptions and justification      │
└──────────────────────────────────────┘
                  ↓
┌──────────────────────────────────────┐
│  Implementation Adapter Layer        │
│  - Platform translation              │
│  - Terraform generation              │
│  - Parameterization                  │
└──────────────────────────────────────┘
                  ↓
┌──────────────────────────────────────┐
│  Enforcement Layer                   │
│  - OPA Gatekeeper                    │
│  - Runtime allow / deny decisions    │
└──────────────────────────────────────┘

```

Only the top layer decides.  
Only the bottom layer enforces.

---

## 6. Terraform’s Role

Terraform is treated as:

- a declarative description language
- not a runtime source of truth
- not an execution log

The engine never reads Terraform state.

Only explicitly declared configuration is trusted.

If a value cannot be resolved statically, it is treated as **UNKNOWN**, never as compliant.

---

## 7. Internal Model (Platform-Agnostic)

Terraform resources are not used directly for reasoning.

All inputs are normalized into abstract components such as:

- workloads
- identities
- network boundaries
- entry points
- secrets
- control-plane elements

Each component carries context, not provider details:

- exposure (public / internal / private / unknown)
- environment (dev / staging / prod)
- data sensitivity (public / confidential / PII)
- trust relationships

This normalized model is the only input to control selection.

---

## 8. Security Controls (Abstract Layer)

A security control is an architectural requirement, not a tool or rule.

Each control defines:

- when it applies
- what secure means
- what risk exists if missing
- how it can be enforced per platform

Controls never reference Kubernetes, AWS, Docker, or any vendor.

That knowledge belongs to adapters.

---

## 9. Compliance Evaluation Model

Each control evaluation produces one of three results:

- COMPLIANT
- NON-COMPLIANT
- UNKNOWN

UNKNOWN is a valid and expected outcome.

UNKNOWN means:
- insufficient information
- unresolved variables
- abstraction boundaries (e.g., modules)

UNKNOWN is reported and explained, never ignored.

---

## 10. Phase 1 Adapter: Kubernetes via Terraform

Phase 1 implements a Kubernetes adapter, fully local.

Responsibilities:
- Translate abstract controls into Kubernetes enforcement mechanisms
- Generate Terraform artifacts using Kubernetes and Helm providers
- Install and configure enforcement infrastructure
- Attach traceability metadata

The adapter does not:
- decide which controls apply
- infer risk
- calculate overall compliance

---

## 11. Enforcement Mechanism: OPA Gatekeeper

OPA Gatekeeper is used strictly as a runtime policy evaluator.

OPA:
- evaluates individual Kubernetes objects
- returns allow / deny decisions
- logs violations

OPA does not:
- understand architecture
- classify risk
- select controls
- map standards
- generate remediation

OPA enforces decisions made by the engine.

---

## 12. Hard Boundary Between Engine and OPA

Rule:

- If a decision requires understanding why something matters → Engine
- If a decision is a binary check on a single object → OPA

Decisions that are never delegated to OPA:
- control applicability
- risk classification
- standards mapping
- exemption justification
- remediation planning
- platform translation

---

## 13. Metadata and Traceability

Every generated enforcement artifact includes metadata describing:

- control identifier
- severity
- standards references
- rationale
- engine version
- generation timestamp

Metadata is attached to policy artifacts, not application workloads.

Workloads are never required to carry engine metadata.

---

## 14. Exceptions and Exemptions

Exceptions are design decisions, not runtime heuristics.

The engine:
- decides whether an exception exists
- records justification
- embeds it explicitly into enforcement parameters

OPA:
- applies the exception mechanically
- never invents or infers exemptions

No self-service workload-level exemptions.

---

## 15. Reporting

The engine produces a report that answers:

- which controls apply
- which are enforced
- which are missing
- which are unknown
- why each decision was made
- which standards are partially or fully covered

OPA violation logs are inputs, not the report itself.

---

## 16. Inputs

- Terraform configuration directory (mandatory)
- Optional context profile (environment, data sensitivity)

The context profile exists to declare intent Terraform cannot express.

---

## 17. Outputs

- Terraform enforcement package (overlay, not rewrite)
- OPA Gatekeeper installation artifacts
- Constraint definitions with parameters
- Human-readable explanation
- Machine-readable report

All outputs are additive and non-destructive.

---

## 18. Validation Strategy

Phase 1 validation is local and static:

- Terraform syntax validation
- Terraform planning
- Optional local Kubernetes enforcement demonstration

Cloud credentials are not required.

---

## 19. Non-Goals (Explicit)

This project does not aim to:

- replace OPA
- replace Terraform
- replace cloud security platforms
- automate business decisions
- provide real-time monitoring

---

## 20. Why This Architecture Scales

- Controls remain unchanged
- New platforms require only adapters
- Reports stay consistent
- Enforcement evolves independently

This separation is intentional.

---

## 21. Phase 1 Success Criteria

Phase 1 is successful if:

- controls are written once
- Kubernetes enforcement is generated correctly
- decisions are explainable
- the engine works fully offline
- future adapters can be added without changing controls

---

## 22. Positioning

This project is not a scanner.  
It is not a rule engine.  
It is not a compliance checklist.

It is a **security policy engineering system**.
