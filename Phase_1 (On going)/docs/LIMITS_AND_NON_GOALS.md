# Limits and Non-Goals — Phase 1

This document defines the **explicit limits** and **permanent non-goals** of the Policy Engine in Phase 1.

Its purpose is to prevent incorrect expectations, over-interpretation of results, and accidental expansion beyond the intended scope.

The constraints described here are **intentional design decisions**, not temporary shortcomings.

---

## 1. Why Limits Are Explicitly Defined

Security tooling often fails not because it is weak, but because it is misunderstood.

This document exists to ensure that:
- outputs are interpreted correctly
- decisions remain defensible
- future extensions do not violate core assumptions

Clear limits are a sign of architectural maturity.

---

## 2. Limits of Phase 1

### 2.1 Static Analysis Only

Phase 1 operates exclusively on **static input**.

It does not observe:
- runtime behavior
- live infrastructure state
- traffic patterns
- operational drift

All reasoning is based on declared configuration and explicit context.

---

### 2.2 No Runtime Guarantees

Phase 1 does not guarantee that:
- enforcement will prevent all misconfigurations
- runtime behavior matches expectations
- deployed systems remain compliant over time

Enforcement artifacts reduce risk; they do not eliminate it.

---

### 2.3 Partial Information Is Expected

Terraform configurations may be:
- parameterized
- modular
- incomplete by design

Phase 1 accepts that:
- some information is unavailable
- some evaluations result in UNKNOWN
- completeness cannot be assumed

UNKNOWN is surfaced, not resolved.

---

### 2.4 Limited Control Coverage

Phase 1 implements a **representative subset** of security controls.

It does not aim to:
- cover entire CIS benchmarks
- fully implement NIST frameworks
- satisfy regulatory audits

The focus is on correctness of reasoning, not breadth of coverage.

---

### 2.5 Single Enforcement Backend

Phase 1 supports:
- Kubernetes enforcement via OPA Gatekeeper

It does not include:
- cloud-native enforcement backends
- operating system-level enforcement
- application-layer enforcement

Additional backends require future phases.

---

### 2.6 No Autonomous Decision-Making

Phase 1 does not:
- accept or reject risk automatically
- infer business priorities
- override human judgment

All high-level decisions remain external to the system.

---

## 3. Explicit Non-Goals (Permanent)

The following are **not goals** of this project, in Phase 1 or later.

---

### 3.1 Not a Vulnerability Scanner

The Policy Engine does not:
- scan for known CVEs
- analyze software dependencies
- inspect container images
- perform dynamic testing

It reasons about **architecture and policy**, not vulnerabilities.

---

### 3.2 Not a Runtime Security Platform

The project does not aim to:
- monitor runtime behavior
- detect active attacks
- provide alerts or dashboards
- integrate with SIEM or SOC tooling

OPA enforcement is limited to admission control.

---

### 3.3 Not a Compliance Automation Tool

The project does not:
- certify compliance
- produce audit-ready attestations
- replace compliance teams
- calculate regulatory scores

Standards references are informational, not authoritative.

---

### 3.4 Not an Infrastructure Management System

The Policy Engine does not:
- manage infrastructure lifecycle
- orchestrate deployments
- handle rollbacks
- detect drift

Terraform execution remains a separate concern.

---

### 3.5 Not a Policy Language or DSL

The project does not introduce:
- a new policy language
- a custom rule DSL
- an alternative to Rego

Controls are modeled conceptually, not syntactically.

---

### 3.6 Not a Replacement for OPA or Terraform

The project does not aim to:
- replace OPA Gatekeeper
- replace Terraform
- abstract away these tools entirely

It integrates them deliberately and transparently.

---

## 4. Security and Trust Assumptions

Phase 1 assumes:
- Terraform input is provided in good faith
- enforcement artifacts are reviewed before application
- users understand the limits of static analysis

The system does not defend against malicious input authors.

---

## 5. Consequences of These Limits

These limits imply that:
- results must be interpreted by humans
- outputs are advisory and enforceable, not authoritative
- false confidence is actively avoided

The system prioritizes **honesty over optimism**.

---

## 6. Why These Non-Goals Are Important

By explicitly rejecting certain goals, the project ensures that:

- responsibilities remain clear
- complexity is controlled
- correctness is preserved
- future growth remains sustainable

Attempting to solve all security problems at once would undermine the core objective.

---

## 7. Summary

Phase 1 of the Policy Engine is intentionally constrained.

Its role is to:
- engineer security policy deterministically
- generate enforceable artifacts responsibly
- explain decisions clearly
- surface uncertainty explicitly

Anything beyond that belongs to future phases or to human judgment.
