# Consistency Audit Report

## Terminology Audit Update — TERM-001 through TERM-006

### Verified Results

| ID | Term | Status |
|---|---|---|
| TERM-001 | Membership ↔ Subscription | AMBIGUITY / GAP |
| TERM-002 | Product / Membership Product / Package / Add-on | CONSISTENT |
| TERM-003 | Workspace Membership ↔ System Role | CONSISTENT |
| TERM-004 | Content Plan ↔ Project Context | CONSISTENT |
| TERM-005 | Engine ↔ Module ↔ Domain | CONSISTENT BY LAYER |
| TERM-006 | Capability ↔ Feature ↔ Entitlement | VERIFIED CONFLICT |

### TERM-001
Membership and Subscription terminology is related but not fully canonicalized. The commercial Membership Product must be distinguished from the user's subscription/membership lifecycle record/state.

### TERM-002
No remaining terminology conflict verified. Core Contract #4 explicitly distinguishes Membership, Feature Package, Add-on, and Bundle.

### TERM-003
No remaining terminology conflict verified. Workspace Membership is distinct from global/System Role.

### TERM-004
No remaining terminology conflict verified. Content Plan is planning context; ProjectContext is a resolved context object.

### TERM-005
No remaining terminology conflict verified when terms are used at their architectural layers. Engine, Module, Domain, Bounded Context, Service, Worker, and Adapter must not be treated as interchangeable ownership terms.

### TERM-006
**Verified conflict.** Core Contract #2 Section 30 describes Role as containing both Permissions and "Default Entitlement / Feature Configuration" and classifies Analyzer feature availability as entitlement/configuration properties. This conflicts with the established separation:

```text
Membership → Entitlement / Product Benefit
Role       → Permission / Authorization
```

Correction required: Role may define permission or configurable defaults/preferences, but must not own or become the source of Entitlement. Entitlement remains under the Entitlement domain.

### Audit Integrity

This terminology update does not modify the original source documents. It records the verified audit result for downstream remediation.
