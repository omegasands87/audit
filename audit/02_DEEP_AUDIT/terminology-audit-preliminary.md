# Terminology Audit — Full Verification Pass

## Status

**VERIFIED — TERM-001 through TERM-006**

This document records the full terminology sweep against the available source-of-truth documents in the repository.

## Final Verification Matrix

| ID | Term | Result | Finding |
|---|---|---|---|
| TERM-001 | Membership ↔ Subscription | AMBIGUITY / GAP | Relationship/entity vocabulary is not sufficiently locked; no direct business-rule contradiction verified. |
| TERM-002 | Product / Membership Product / Package / Add-on | CONSISTENT | Contract #4 explicitly distinguishes Membership, Feature Package, Add-on, and Bundle. |
| TERM-003 | Workspace Membership ↔ System Role | CONSISTENT | Workspace Membership is explicitly distinct from global/System Role. |
| TERM-004 | Content Plan ↔ Project Context | CONSISTENT | Content Plan is planning-level grouping; ProjectContext is a standardized context object. |
| TERM-005 | Engine ↔ Module ↔ Domain | CONSISTENT BY LAYER | Architecture distinguishes product/engine grouping from logical modules, bounded contexts, services, and workers. |
| TERM-006 | Capability ↔ Feature ↔ Entitlement | VERIFIED CONFLICT | Core Contract #2 mixes Role configuration with Entitlement / Feature Configuration, conflicting with the invariant Role → Permission and Membership → Entitlement. |

## Detailed Findings

### TERM-001 — Membership ↔ Subscription

**Result: AMBIGUITY / GAP**

The Business Decision Register uses subscription/membership lifecycle language (`active`, `inactive`, `expired`, cancellation), while Core Contract #4 defines Membership as a product with subscription semantics. The concepts are related, but the exact canonical entity relationship should be explicitly locked before implementation.

**Required clarification:** distinguish the commercial Product (`Membership Product`) from the user's subscription/membership state/record.

### TERM-002 — Product / Membership Product / Package / Add-on

**Result: CONSISTENT**

Core Contract #4 explicitly defines the product types:

```text
Membership
Feature Package
Add-on
Bundle
```

Membership is a product with subscription semantics; Feature Packages are separately purchased packages; Add-ons provide additional capability/usage. No terminology conflict remains.

### TERM-003 — Workspace Membership ↔ System Role

**Result: CONSISTENT**

Core Contract #9 explicitly keeps Workspace Membership separate from global System Role. Workspace membership controls participation in a workspace; System Role participates in global authorization. These must not be merged.

### TERM-004 — Content Plan ↔ Project Context

**Result: CONSISTENT**

Content Plan is the planning-level grouping containing Content Slots. `ProjectContext` is a standardized context object used by services to resolve user/workspace/plan/slot/tenant context. They are different concepts and can coexist without duplicate ownership.

### TERM-005 — Engine ↔ Module ↔ Domain

**Result: CONSISTENT BY LAYER**

The architecture uses Engines as higher-level product/functional groupings while separately defining domains, modules, bounded contexts, application services, workers, and adapters. This is acceptable provided implementation does not treat these terms as interchangeable ownership boundaries.

### TERM-006 — Capability ↔ Feature ↔ Entitlement

**Result: VERIFIED CONFLICT**

The architecture and Contract #4 establish:

```text
Capability
→ domain-defined ability

Product
→ commercial definition

Entitlement
→ user's granted right/capacity

Feature
→ user-facing/system capability concept
```

However, Core Contract #2 Section 30 introduces:

```text
Role
├── Permissions
└── Default Entitlement / Feature Configuration
```

and explicitly labels Analyzer capabilities such as Deep Source Intelligence, Media Intelligence, and Cross-Source Analysis as entitlement/configuration properties.

This creates an ownership/terminology collision with the established invariant:

```text
Membership
→ Entitlement / Product Benefit

Role
→ Permission / Authorization
```

**Required correction:** Role may have configurable defaults/preferences that influence UX or authorization behavior, but it must not become an owner/source of entitlement. Entitlement remains owned by the Entitlement domain and is derived from valid commercial/admin grants according to the finalized business rules.

## Source-of-Truth Rule

The Engineering Constitution requires one authoritative owner per persistent business entity and prohibits duplicate sources of truth. It also explicitly separates Membership from Role and Product from Entitlement.

Therefore TERM-006 is a verified architecture/terminology finding requiring correction before implementation drift occurs.

## Scope Note

This pass verifies TERM-001 through TERM-006 against the repository's governance, business-decision, core-contract, and architecture documents available in the audit set. Original source documents remain unchanged by this audit pass.
