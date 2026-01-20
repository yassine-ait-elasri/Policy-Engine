# Roadmap

This document describes the **planned evolution** of the Policy Engine beyond Phase 1.

The roadmap is structured to:
- preserve the architectural boundaries defined in Phase 1
- extend capabilities incrementally
- avoid redesigning core abstractions
- keep reasoning deterministic and explainable

Each phase builds on the previous one without invalidating it.

---

## Guiding Principles for the Roadmap

All future phases must respect the following principles:

- The **control model is stable**
- The **reasoning layer remains platform-agnostic**
- Enforcement mechanisms remain replaceable
- Decisions remain explicit and traceable
- Automation never replaces human judgment silently

Any feature that violates these principles is out of scope.

---

## Phase 1 — Policy Engineering Foundation (Current)

### Objective

Establish a correct, deterministic, and explainable policy engineering core.

### Capabilities

- Terraform as input (static parsing only)
- Normalized Infrastructure Model
- Platform-agnostic security controls
- Static compliance evaluation (COMPLIANT / NON-COMPLIANT / UNKNOWN)
- Kubernetes enforcement via Terraform
- OPA Gatekeeper integration
- Deterministic artifact generation
- Human- and machine-readable reporting
- Fully offline operation

### Outcome

A working system that proves:
- controls can be written once
- enforcement can be generated consistently
- reasoning remains explainable
- adapters can be added without refactoring

Phase 1 is the **architectural anchor**.  
Later phases must not weaken its guarantees.

---

## Phase 2 — Usability and Transparency

### Objective

Make the engine usable by non-authors while preserving correctness and traceability.

### Additions

- Command-line interface improvements
- Structured output formats (JSON, YAML) for reports
- Clear summaries of applied, missing, and unknown controls
- Improved exception visibility and documentation
- Optional project-level configuration profiles

### Explicit Exclusions

- No automated deployment
- No hidden defaults
- No decision inference

### Rationale

Phase 2 improves accessibility without altering the decision model.

---

## Phase 3 — User Interface and Reporting

### Objective

Expose the engine’s reasoning and outputs through a controlled user interface.

### Additions

- Web-based interface for:
  - uploading Terraform inputs
  - defining context profiles
  - reviewing control plans
- Visual representation of:
  - control applicability
  - enforcement coverage
  - standards mapping
- Exportable reports (PDF / structured formats)

### Constraints

- UI reflects engine decisions; it does not modify them
- All decisions remain reviewable and exportable
- No business logic is embedded in the UI

---

## Phase 4 — Multi-Platform Adapters

### Objective

Extend enforcement beyond Kubernetes without changing core logic.

### Additions

- Additional adapters, such as:
  - cloud network controls
  - identity and access platforms
  - infrastructure segmentation mechanisms
- Adapter-specific enforcement capabilities
- Explicit reporting of unsupported controls per platform

### Guarantees

- Existing controls remain unchanged
- Reports remain consistent across platforms
- Enforcement differences are explicit

---

## Phase 5 — Controlled Deployment Assistance

### Objective

Assist with deployment while preserving human control.

### Additions

- Optional Terraform execution support
- Plan review and approval workflows
- Deployment previews and diffs
- Explicit confirmation steps

### Constraints

- No automatic application without approval
- No hidden remediation
- No silent changes

Deployment remains a **human decision**.

---

## Phase 6 — Continuous Policy Awareness (Optional)

### Objective

Detect drift between declared policy intent and applied enforcement.

### Additions

- Periodic comparison between:
  - policy intent
  - enforcement artifacts
  - declared infrastructure
- Reporting of divergence
- No automatic correction

### Explicit Exclusions

- No runtime monitoring
- No behavioral analysis
- No autonomous remediation

---

## Long-Term Considerations

Potential future areas (non-committal):

- Additional input formats beyond Terraform
- Policy reasoning simulations
- Control impact analysis
- What-if analysis for architectural changes

These are exploratory and not guaranteed.

---

## What Will Never Change

Regardless of phase:

- Controls remain abstract and platform-agnostic
- Enforcement remains replaceable
- UNKNOWN remains a valid outcome
- Decisions remain explainable
- Human judgment remains central

These are non-negotiable design commitments.

---

## Roadmap Summary

The roadmap prioritizes:
- correctness before convenience
- clarity before automation
- stability before expansion

The Policy Engine evolves by **adding layers**, not by redefining its core.
