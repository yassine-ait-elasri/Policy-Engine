# Vision

## Purpose

Policy Engine exists to address a structural problem in how security is designed and applied in modern systems.

Infrastructure is described as code.  
Security requirements are described as documents.  
Enforcement happens through tools that operate without architectural context.

Between these layers, security decisions are often:
- implicit rather than explicit
- manually interpreted
- inconsistently applied
- poorly documented
- difficult to audit or explain

Policy Engine is designed to close this gap.

---

## What “Policy Engineering” Means Here

This project is not about writing rules or scanning configurations.

It is about **engineering security policy** as a first-class artifact.

Policy engineering, in this context, means:
- deriving security requirements from architecture and context
- expressing those requirements as explicit controls
- mapping controls to enforceable mechanisms
- preserving traceability from intent to enforcement

The goal is not to guess whether something is secure,  
but to explain **why** something must be secure and **how** that decision is enforced.

---

## The Core Belief

Security decisions should be:

- **Deterministic**  
  The same input produces the same decisions.

- **Explainable**  
  Every control can be justified in human terms.

- **Traceable**  
  Each enforcement artifact can be traced back to a control, a rationale, and a standard.

- **Separable**  
  Reasoning about security must be independent from enforcing it.

This project is built around enforcing those properties.

---

## What the Engine Decides vs What It Does Not

Policy Engine is responsible for **strategy**, not execution.

It decides:
- which security controls apply
- why they apply
- what risk they mitigate
- how they map to recognized standards
- what constitutes compliance or non-compliance
- when an exception is justified

It does not decide:
- how runtime tools evaluate objects
- whether an individual request is allowed
- how enforcement engines implement logic internally

Those responsibilities belong to enforcement systems.

---

## Platform Agnosticism by Design

Policy Engine is intentionally platform-agnostic at its core.

Controls are written once, in neutral language that does not reference:
- cloud providers
- container platforms
- orchestration tools
- specific products or services

Platform-specific knowledge is isolated in **adapters**.

This allows:
- the same controls to apply across environments
- enforcement to evolve independently
- new platforms to be added without rewriting policy logic

Platform agnosticism is not an afterthought.  
It is a foundational design constraint.

---

## Terraform as an Interface, Not a Dependency

Terraform is used as a **common description and delivery interface**, not as a security authority.

Terraform provides:
- a structured way to describe infrastructure
- a predictable configuration model
- a portable output format

Policy Engine does not rely on:
- Terraform state
- runtime values
- cloud credentials

This keeps the system reproducible, auditable, and testable offline.

---

## Separation of Strategy and Enforcement

A strict boundary exists between:

- **Policy Strategy**  
  Architectural reasoning, control selection, standards mapping.

- **Policy Enforcement**  
  Runtime allow/deny decisions on concrete objects.

Enforcement engines (such as OPA Gatekeeper) are treated as:
- execution backends
- not decision-makers
- not sources of truth

This separation is essential for correctness, explainability, and long-term maintainability.

---

## Why This Is Not “Another Policy Tool”

Policy Engine is not:
- a scanner that reports findings
- a rule engine that reacts to events
- a compliance checklist
- a deployment automation system

It is a system for **making security decisions explicit** and **turning them into enforceable reality**.

Its value lies in:
- the reasoning it performs
- the clarity it produces
- the structure it imposes on security thinking

---

## Long-Term Direction

While Phase 1 focuses on a single implementation adapter, the long-term vision is:

- multiple enforcement backends
- consistent control logic across platforms
- human-readable security posture explanations
- auditable policy evolution over time

The core engine is designed to remain stable as implementations change.

---

## Final Statement

Security should not be an emergent property of tools.  
It should be a consequence of deliberate architectural decisions.

Policy Engine exists to make those decisions explicit, enforceable, and explainable.
