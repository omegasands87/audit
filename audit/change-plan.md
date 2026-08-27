# Change Plan — `original/` Consistency Audit

## Status

**AUDIT PLAN — DO NOT MODIFY `original/` YET**

This document converts identified inconsistencies into controlled changes.

---

# 1. Change Rules

1. `original/` remains untouched during audit.
2. Business rules follow `Final_Business_Decision_Register.md`.
3. Product requirements follow `PRD_Master_Platform_Konten_AI_FINAL.md` unless superseded by a later final business decision.
4. Domain ownership follows the relevant Core Contract.
5. Architecture must reflect the contracts; it must not silently redefine business rules.
6. Implementation documents must follow the accepted architecture.
7. Every change must trigger downstream cross-checking.

---

# 2. Priority Legend

- 🔴 Critical — can cause fundamentally incorrect implementation.
- 🟠 High — can cause different modules/AI agents to implement different behavior.
- 🟡 Medium — ambiguity or inconsistency that should be resolved before the affected slice.
- 🟢 Minor — terminology, status labels, wording, or documentation hygiene.

---

# 3. Change Register

| ID | Priority | Area | Affected Documents | Required Action |
|---|---|---|---|---|
| CP-001 | 🟠 High | Document status | Architecture, Core Contracts, Implementation | Normalize Draft / Baseline / Final status and explicitly distinguish authority from working draft. |
| CP-002 | 🟠 High | Content Slot ownership | PRD, Architecture, Contract #9, Contract #11, Contract #12, Contract #13 | Define Content Context as canonical Content Slot owner; Planner owns planning decisions and may mutate through the approved contract boundary. |
| CP-003 | 🔴 Critical | Research evidence ownership | PRD, Architecture, Contract #10, Contract #12 | Research remains canonical owner of Source/Evidence; Analyzer may reference and interpret, never create a competing canonical research-source model. |
| CP-004 | 🟠 High | Configuration boundary | PRD, Architecture, Contract #3, Contract #2, Product/Entitlement contract | Explicitly separate configurable values/defaults from business ownership and enforcement. Configuration must not become a business-logic God Service. |
| CP-005 | 🟠 High | Role vs Membership | PRD, Contract #2, Product/Entitlement contract, Architecture | Ensure role means authorization and membership/product means commercial entitlement. Role-specific defaults must not imply role grants entitlement. |
| CP-006 | 🟠 High | Provider boundary | Architecture, Contract #5, Contract #6, Operations | Define shared provider infrastructure versus domain-owned provider adapters and canonical business state. |
| CP-007 | 🟡 Medium | Workspace vs Research Workspace | PRD, Architecture, Contract #9, Contract #10 | Preserve separate entities and document their relationship explicitly. |
| CP-008 | 🟡 Medium | Payment state terminology | Business Decision Register, Payment contract, Implementation docs | Standardize canonical state to `Paid`; use approval only as an action/trigger where appropriate. |
| CP-009 | 🟡 Medium | Agency settlement balance | Business Decision Register, Product/Entitlement, Payment, Architecture | Explicitly namespace wholesale settlement balance as Agency/White-label infrastructure, not member wallet/PAYG/deposit. |
| CP-010 | 🟡 Medium | Retention ownership | Business Decision Register, Storage contract, Architecture, Operations | Storage owns binary lifecycle; domain policy determines retention. Preserve 48h export/editor and 90d support attachment rules. |
| CP-011 | 🟡 Medium | Engine vs Domain | PRD, Architecture, Vertical Slice Order | Define Engines as product/feature groupings; bounded domains remain ownership boundaries. |
| CP-012 | 🟡 Medium | Dependency graph | Architecture, Vertical Slice Order, Roadmap | Reconcile architectural dependencies with build sequence; avoid implying unnecessary runtime/domain dependency. |
| CP-013 | 🟢 Minor | Terminology | All documents | Normalize entity names, state names, capability/package/add-on/product terminology, and capitalization. |
| CP-014 | 🟡 Medium | Implementation status | Vertical Slice Order, Roadmap, Implementation Specification | Ensure build sequence, roadmap, and per-slice specification use the same slice IDs and status semantics. |
| CP-015 | 🟡 Medium | Manual transfer flow | Business Decision Register, Payment, Support, Vertical Slice docs | Use one canonical flow: ticket approval → Payment `Paid` → entitlement grant. |

---

# 4. Detailed Actions

## CP-001 — Document Status

### Problem
Some documents are marked `FINAL`, while Architecture is `Architecture Baseline Draft`, and several Core Contracts are Draft/Revised.

### Action
Introduce explicit metadata:

```text
Authority: Business / Product / Domain Contract / Architecture / Implementation
Status: FINAL / BASELINE / DRAFT
Supersedes: <document/version if applicable>
```

A document can be a baseline without being the highest authority.

### Acceptance
No document status can be interpreted as overriding the authority hierarchy.

---

## CP-002 — Content Slot Ownership

### Problem
Content Slot is described as the production anchor and Content Context owner, while Planner manages calendar/planning behavior.

### Action
Lock the distinction:

```text
Content Context
→ owns Content Slot identity/lifecycle/context

Planner
→ owns planning decisions, scheduling, allocation, candidate generation
```

Planner must use the Content Slot contract for changes to Content Slot state.

### Acceptance
No second Content Slot/project entity is introduced by Planner, Analyzer, Blueprint, or downstream modules.

---

## CP-003 — Research Evidence Ownership

### Problem
Analyzer contains source/evidence/claim concepts that could be interpreted as a second canonical research data model.

### Action
Define:

```text
Research
→ canonical Source / Evidence / Research Claim truth

Analyzer
→ analysis run / interpretation / derived output
```

Analyzer may store references and derived analysis artifacts, but cannot redefine Research source identity.

### Acceptance
One canonical source/evidence identity exists across Research and Analyzer.

---

## CP-004 — Configuration Boundary

### Problem
Configuration supports broad scopes including Product, Membership, Role, User, Tenant, Workspace. Without explicit boundary rules, business logic may migrate into configuration.

### Action
Configuration owns values/policies/schema/scope/version. Domain modules own meaning and enforcement.

Example:

```text
Configuration
→ support.auto_close_days = 7

Support
→ owns ticket state and applies 7-day rule
```

### Acceptance
No domain's core transaction/state machine is implemented as arbitrary configuration data.

---

## CP-005 — Role vs Membership

### Problem
The PRD supports role-specific Analyzer defaults while business decisions explicitly separate role from membership/entitlement.

### Action
Represent separately:

```text
Role
→ authorization

Membership/Product/Entitlement
→ commercial capability

Configuration
→ permitted defaults/preferences
```

If a role has UI/default settings, those settings must not be interpreted as entitlement grants.

### Acceptance
Changing membership does not silently change permissions; changing role does not silently grant commercial entitlement.

---

## CP-006 — Provider Boundary

### Problem
Shared Provider Infrastructure and domain-specific adapters can appear to have overlapping ownership.

### Action
Use:

```text
Provider Infrastructure
→ registry, pool, routing, credentials, adapter infrastructure

Consuming Domain
→ business operation, business state, result semantics
```

Payment remains owner of Payment state. Research remains owner of normalized Research data. AI generation remains owned by its production domain.

### Acceptance
Provider schemas never become canonical domain entities.

---

## CP-007 — Workspace vs Research Workspace

### Action
Keep:

```text
Workspace
→ general operational/content boundary

Research Workspace
→ research-specific working context
```

Document explicit relationship without merging identities.

---

## CP-008 — Payment State Terminology

### Action
Canonical business state:

```text
Pending → Paid → ...
```

`Approved` is an action/decision where applicable, not an alternative canonical final payment state.

For manual transfer:

```text
Admin Approves Ticket
→ Payment = Paid
```

---

## CP-009 — Agency Settlement Balance

### Action
Explicitly distinguish:

```text
Member billing
→ subscription + packages + add-ons + entitlements

Agency wholesale settlement
→ separate tenant/agency settlement balance
```

No member wallet/PAYG/deposit behavior should be inferred from the Agency mechanism.

---

## CP-010 — Retention

### Action
Preserve finalized business policies:

```text
Export / Editor Media
→ 48 hours

Support Attachment
→ 90 days after ticket Closed
```

Storage executes lifecycle/purge; business domains define policy context.

---

## CP-011 — Engine vs Domain

### Action
Define:

```text
Engine
→ product capability grouping / orchestration concept

Domain / Bounded Context
→ authoritative ownership boundary
```

No engine grouping may imply shared database ownership or cross-domain mutation.

---

## CP-012 — Dependency Graph

### Action
Separate:

```text
Build dependency
Runtime dependency
Data reference
Event subscription
```

The vertical slice sequence may require an earlier slice to be completed without implying that the domains become tightly coupled.

---

## CP-013 — Terminology

Create a canonical glossary during the next audit phase.

Priority terms:

```text
Membership
Product
Package
Add-on
Capability
Entitlement
Role
Permission
Content Slot
Content Plan
Workspace
Research Workspace
Source
Evidence
Insight
Opportunity
Blueprint
Blueprint Variant
Payment
Order
Provider
Settlement Balance
```

---

## CP-014 — Implementation Sequence

Reconcile these three documents:

```text
Final_Vertical_Slice_Order.md
Implementation_Roadmap_P0_P1_P2_Dependency_Acceptance_Gates.md
Implementation_Specification_Per_Vertical_Slice.md
```

They must use the same slice identifiers, phases, dependencies, and acceptance terminology.

---

## CP-015 — Manual Transfer

Canonical flow:

```text
Order
→ Manual Transfer
→ Payment Pending
→ Support Ticket
→ Proof Upload
→ Admin Approval
→ Payment Paid
→ Entitlement Granted
```

There must not be a second independent payment-approval workflow after ticket approval.

---

# 5. Execution Order

Changes should be applied in this order:

```text
1. Governance / authority metadata
2. Business/Product terminology
3. Core Contract ownership boundaries
4. Architecture
5. Implementation sequencing
6. UI/UX
7. Operations
8. Final cross-document audit
```

Do not reverse this order for foundational conflicts.

---

# 6. Required Final Outputs

After the decisions are approved and changes are applied:

```text
audit/
├── audit-checklist.md
├── consistency-report.md
├── source-of-truth.md
└── change-plan.md
```

Then create the corrected document set separately from `original/`.

---

# 7. Lock Condition

The corrected documentation set is ready for AI Builder only when:

```text
[ ] Business decisions have one canonical source
[ ] Product requirements are reconciled
[ ] Every entity has one owner
[ ] No duplicate canonical models exist
[ ] Cross-domain writes are prohibited
[ ] State machines are unambiguous
[ ] Provider boundaries are clear
[ ] Configuration boundaries are clear
[ ] Build sequence matches dependencies
[ ] UI matches contracts
[ ] Operations matches implementation
[ ] Final cross-document audit = PASS
```
