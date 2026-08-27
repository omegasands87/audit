# Terminology Audit — Preliminary Findings

**Status:** PRELIMINARY — NEEDS FURTHER VERIFICATION

This document records terminology findings identified during the initial dedicated terminology sweep. These are not final conflicts and do not authorize any source-document correction.

`original/` remains immutable.

## Preliminary Findings

### TERM-001 — Membership vs Subscription

**Status:** Needs further verification

Both terms appear in commercial-access and lifecycle contexts. The audit must determine whether Subscription is the lifecycle entity for a Membership/Product purchase, or whether the two are intentionally separate entities.

**Required verification:** canonical definitions, ownership, lifecycle, billing-cycle relationship, and references across PRD, Business Decision Register, Product/Entitlement and Payment contracts.

### TERM-002 — Product / Membership Product / Package / Add-on

**Status:** Needs further verification

The documents distinguish Product, Membership/Product benefits, Package and Add-on in different contexts. The vocabulary needs an explicit canonical hierarchy so implementation does not treat these terms as interchangeable.

**Required verification:** entity definitions, commercial role, purchasability, entitlement mapping and UI terminology.

### TERM-003 — Workspace Membership vs System Role

**Status:** Needs further verification

Workspace membership and system/global role represent different authorization scopes, but the terminology could be interpreted as one generic membership/role concept.

**Required verification:** scope, actor identity, permission assignment, workspace membership relation and global-role relation.

### TERM-004 — Content Plan vs Project Context

**Status:** Needs further verification

Content Plan is used as a business/planning concept while Project Context is a technical/context object. The audit must ensure they are not treated as aliases or competing entities.

**Required verification:** definitions, ownership, lifecycle, UI naming and cross-domain references.

### TERM-005 — Engine vs Module vs Domain

**Status:** Needs further verification

The PRD uses Engine and Module as product-organization concepts, while architecture uses Domain/Bounded Context as ownership boundaries. These must be explicitly distinguished so implementation does not infer that the three terms represent the same boundary.

**Required verification:** canonical architectural vocabulary and usage across PRD, Architecture, Contracts and Vertical Slice documents.

### TERM-006 — Capability vs Feature vs Entitlement

**Status:** Needs further verification

Capability and Entitlement are already distinguished in core commercial/authorization concepts, but the PRD also uses Feature/Feature Access/Feature Package language. A canonical vocabulary hierarchy is needed to prevent `Feature` from becoming an ambiguous synonym for either capability or commercial entitlement.

**Required verification:** definitions, authorization semantics, commercial semantics and implementation references.

## Rules

- Do not classify these preliminary items as final CONFLICT findings yet.
- Do not change `original/` based on these findings.
- Verify each term across all relevant source documents before reconciliation.
- Record evidence and exact source locations when a finding is promoted or reclassified.
- Any final terminology decision belongs in the Source-of-Truth Reconciliation phase after project-owner review.
