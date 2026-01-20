# Terraform Integration — Phase 1

This document defines **how Terraform is used** by the Policy Engine in Phase 1.

It clarifies assumptions, constraints, and design decisions to ensure Terraform is used as a
**deterministic interface**, not as a source of implicit behavior.

Terraform is central to the system, but its role is intentionally limited.

---

## 1. Purpose of Terraform Integration

Terraform serves two roles in Phase 1:

1. **Input**: a declarative description of infrastructure intent
2. **Output**: a deterministic, reproducible description of enforcement artifacts

Terraform is not used as:
- a runtime truth source
- a compliance oracle
- an execution engine for decisions

All security reasoning occurs outside Terraform.

---

## 2. Terraform as an Input Language

### 2.1 What Is Consumed

The Policy Engine consumes:
- Terraform configuration files
- declared resources
- declared attributes
- references and relationships
- provider declarations

Terraform is treated as **static text with structure**, not as an evaluated program.

---

### 2.2 What Is Explicitly Ignored

The engine does not consume:
- Terraform state
- provider APIs
- runtime values
- apply-time outputs

This ensures:
- reproducibility
- offline operation
- consistent reasoning

---

### 2.3 Trust Model

Only **explicitly declared configuration** is trusted.

Any value that depends on:
- variables
- locals
- module outputs
- expressions
- conditionals

is treated as **UNKNOWN**.

UNKNOWN values are preserved and surfaced, never resolved heuristically.

---

## 3. Static Parsing Constraints

Terraform configurations may contain:
- variables
- locals
- modules
- dynamic blocks
- for_each / count
- expressions

Phase 1 parsing:
- extracts structure
- records declared attributes
- tracks references

Phase 1 parsing does not:
- evaluate expressions
- expand modules
- resolve variables
- infer defaults

These constraints are intentional.

---

## 4. Normalization Boundary

Terraform resource types are **not used directly** for security reasoning.

All Terraform input is converted into a **Normalized Infrastructure Model (NIM)** before any control logic executes.

The normalization boundary:
- isolates Terraform-specific complexity
- stabilizes the reasoning model
- enables future input formats

No control logic runs on raw Terraform data.

---

## 5. UNKNOWN as a First-Class Result

UNKNOWN is a core concept in Terraform integration.

UNKNOWN arises when:
- values are parameterized
- module internals are hidden
- expressions cannot be resolved statically

Rules:
- UNKNOWN never implies compliance
- UNKNOWN never implies non-compliance
- UNKNOWN is reported explicitly

This prevents false confidence.

---

## 6. Terraform as an Output Interface

### 6.1 Output Philosophy

The Policy Engine **never rewrites user Terraform**.

All generated Terraform is:
- additive
- isolated
- optional to apply

Generated artifacts form a **security baseline overlay**.

---

### 6.2 Overlay Strategy

The overlay strategy ensures:
- user code remains unchanged
- enforcement can be reviewed independently
- rollback is trivial
- integration is explicit

The overlay may be:
- a separate Terraform module
- a separate directory
- an explicitly referenced package

---

## 7. Provider Usage in Phase 1

Phase 1 uses Terraform providers only to express enforcement artifacts.

Providers may include:
- Kubernetes provider
- Helm provider
- local or utility providers if required

Providers are never used to:
- query live infrastructure
- infer runtime configuration
- validate architectural assumptions

---

## 8. Terraform Generation Rules

Generated Terraform must satisfy the following rules:

- deterministic output for identical inputs
- no side effects
- no dependency on execution order
- explicit parameters for all variable behavior
- clear separation from user configuration

Implicit defaults are avoided wherever possible.

---

## 9. Error Handling

Terraform-related errors are classified as:

- **Parsing errors**: invalid or unsupported syntax
- **Normalization errors**: inconsistent or incomplete models
- **Generation errors**: missing parameters or unsupported mappings

Errors are:
- surfaced explicitly
- never hidden
- never auto-corrected

---

## 10. Validation Strategy

Terraform validation is used to verify:
- syntactic correctness
- internal consistency

Validation includes:
- Terraform syntax validation
- Terraform planning

Validation does not include:
- applying changes automatically
- validating runtime effects

---

## 11. Why Terraform State Is Excluded

Terraform state is excluded because it:
- contains secrets
- is environment-specific
- breaks reproducibility
- introduces non-determinism

Phase 1 reasoning must be portable and auditable.

---

## 12. Future Evolution

Future phases may:
- support additional input formats
- support additional providers
- introduce optional state-aware modes

None of these are required for Phase 1.

The Terraform integration contract defined here must remain stable.

---

## 13. Summary

Terraform integration in Phase 1 is:

- static
- deterministic
- additive
- explainable
- platform-neutral at the reasoning level

Terraform is the **interface**, not the authority.

Security decisions remain explicit and external to Terraform.
