# Phase 2 Deep Audit — Additional Findings

## Status
**FINDINGS ONLY — NO CORRECTIVE EDITS TO `original/`**

These findings are appended to the audit record before any correction is attempted.

## CONFLICTS

### C-013 — Manual Transfer payment-state wording
**Severity:** 🔴 Critical

The Business Decision Register contains both `Payment Approved` wording and the later explicit rule `Admin Approve Ticket → Payment = Paid`, with no second payment approval required.

**Required resolution:** treat the later explicit flow as the candidate canonical rule, but reconcile the earlier wording in the Business Decision Register during the correction phase.

### C-014 — Role Configuration contains Membership/Product semantics
**Severity:** 🔴 Critical

Core Contract #2 uses Role Configuration examples involving `Growth` and included capability behavior, while Membership/Product/Entitlement are separately defined as commercial concepts.

**Risk:** role assignment could grant commercial entitlement.

**Required resolution:** Role configuration may define authorization and defaults/preferences only. Commercial capability must come from Product/Membership/Entitlement.

### C-015 — Content Slot ownership vs Planner command authority remains ambiguous
**Severity:** 🟠 High

Architecture and Contract #9 establish Content Context as the owner of Content Slot, while Contract #11 describes Planner creating/updating slots.

**Required resolution:** separate entity ownership from command authority. Planner requests Content Context to create/update Content Slots; Planner does not own the persistence model.

### C-016 — Planner Content Slot contains downstream-production references
**Severity:** 🟠 High

Content Slot includes references such as `script_id`, `asset_project_id`, and `editor_project_id`.

**Risk:** Content Context can become a downstream-production aggregation/God Entity.

**Required resolution:** classify each field as either true Content Slot context or a downstream reference. Remove ownership implication from fields whose lifecycle belongs to downstream domains.

### C-017 — Configuration precedence and Tenant security boundary are mixed
**Severity:** 🟠 High

Tenant appears both as a configuration scope and as a security/isolation boundary.

**Required resolution:** define separately:

```text
Configuration precedence
Tenant isolation
Authorization scope
```

A configuration override can never bypass tenant isolation or authorization.

## GAPS

### G-013 — Canonical Capability Registry
**Severity:** 🟠 High

No single registry currently defines capability code, owner, entitlement unit, provider requirements, preview/final behavior, version and lifecycle.

### G-014 — Canonical Permission Registry
**Severity:** 🟠 High

No complete authoritative permission catalog is clearly established independently from role assignments.

### G-015 — Canonical Configuration Key Registry
**Severity:** 🟠 High

Configuration keys, schema, owner, scope, default and precedence are not centralized.

### G-016 — Canonical State Machine Index
**Severity:** 🟠 High

State machines are distributed across contracts without one consolidated transition index.

### G-017 — Canonical Event Catalog
**Severity:** 🟠 High

A consolidated event registry with producer, consumer, payload, version, retry and idempotency semantics is missing.

### G-018 — Canonical API Contract Registry
**Severity:** 🟠 High

Commands/queries exist in domain contracts but are not consolidated into one implementation-facing API registry.

### G-019 — Canonical Entity Ownership Registry
**Severity:** 🟠 High

Ownership can be inferred from contracts but lacks one authoritative cross-domain registry.

### G-020 — Vertical Slice specification coverage
**Severity:** 🟡 Medium

The framework/template exists, but completed per-slice specifications are not yet fully represented.

### G-021 — Research historical-observation priority mapping
**Severity:** 🟡 Medium

Research documentation defers historical observation by priority level without a sufficiently explicit slice mapping.

### G-022 — Planner / Content Context command contract
**Severity:** 🟡 Medium

The ownership distinction is conceptual but the exact command-level interface and authorization rules are not sufficiently explicit.

### G-023 — Entitlement consumption failure/reversal matrix
**Severity:** 🟡 Medium

Atomic/idempotent consumption is defined, but provider failure, timeout, retry, cancellation, partial generation and reversal outcomes need a complete matrix.

## Audit Rule

All findings above are recorded before correction. Continue auditing the remaining documents. Do not edit `original/` until the complete audit and Source-of-Truth reconciliation are finished.
