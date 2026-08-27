# Source of Truth — Audit Baseline

## Status

**AUDIT BASELINE — derived from `original/`**

This document defines which document wins when multiple documents describe the same rule, and which domain owns each business concept.

> `original/` remains immutable during audit.

---

# 1. Document Authority

When two documents conflict, use this order:

```text
1. Final Business Decision Register
2. PRD Final
3. Core Contract #1–#13
4. Core Architecture V1
5. Implementation Specification / Roadmap / Vertical Slice Order
6. Current implementation/code
```

This hierarchy is explicitly established by the Engineering Constitution.

Implementation must never silently redefine a business rule.

---

# 2. Business Decision Authority

The **Final Business Decision Register** is authoritative for locked business decisions, including:

```text
Billing
Subscription
Package
Entitlement behavior
Refund
Referral
Commission
Manual Transfer
Support policy
Security policy
Localization
Currency
White-label commercial rules
```

Technical documents may explain implementation, but may not change these rules without a new formal business decision.

---

# 3. Product Requirement Authority

The **PRD Final** is authoritative for:

```text
Product scope
Feature requirements
User journeys
Functional requirements
Product capabilities
Module requirements
UX intent
Business requirement detail not separately overridden by a Final Business Decision
```

If PRD wording conflicts with a later locked Business Decision, the Business Decision wins and the PRD should eventually be reconciled.

---

# 4. Domain Contract Authority

Core Contracts #1–#13 are authoritative for their respective domain responsibilities, entities, invariants, lifecycle rules, and application boundaries.

They must remain consistent with:

```text
Final Business Decision Register
PRD Final
```

A Core Contract must not take ownership of an entity belonging to another Core Contract.

---

# 5. Architecture Authority

Core Architecture V1 is authoritative for:

```text
Domain boundaries
Ownership boundaries
Application boundaries
Persistence boundaries
Event boundaries
Worker boundaries
Provider boundaries
Storage boundaries
Security boundaries
Configuration boundaries
Tenant foundation
```

Architecture may decide **how** an approved requirement is implemented, but may not silently change the business rule itself.

---

# 6. Implementation Authority

Implementation documents define:

```text
Build sequence
Dependencies
Implementation scope
Acceptance gates
Testing requirements
Operational implementation detail
```

They do not override PRD, Business Decisions, Core Contracts, or Architecture.

---

# 7. Entity Ownership Matrix

| Concept | Authoritative Owner | Other domains may |
|---|---|---|
| User | Identity | read/reference |
| Session | Identity | validate/reference |
| Role | Authorization | reference |
| Permission | Authorization | reference |
| Configuration | Configuration | consume |
| Feature Flag | Configuration | evaluate |
| Product | Product | reference |
| Price | Product | snapshot/reference |
| Entitlement | Entitlement | request/check/consume |
| Order | Order | reference |
| Payment | Payment | request/consume events |
| Refund | Payment | request |
| Provider | Provider Infrastructure / relevant adapter boundary | consume |
| Storage Object | Storage | reference |
| Event delivery | Event Infrastructure | publish/consume |
| Audit Record | Audit | append through contract |
| Notification | Notification | request/consume events |
| Workspace | Workspace | reference |
| Content Plan | Content Context / Planner boundary | reference |
| Content Slot | Content Context | reference |
| Research Workspace | Research | reference |
| Competitor | Research | reference |
| Content Observation | Research | reference |
| Topic | Research | reference |
| Hook Pattern | Research | reference |
| CTA Pattern | Research | reference |
| Trend Signal | Research | reference |
| Keyword / Cluster | Research | reference |
| Audience Signal | Research | reference |
| Research Evidence | Research | reference |
| Research Insight | Research | reference |
| Opportunity | Research | reference |
| Planner decision | Planner | consume/provide context |
| Analyzer Run / analysis | Analyzer | consume Research truth |
| Blueprint Variant | Blueprint | consume Analyzer output |
| Asset Requirement | Production / Asset boundary | reference |
| Support Ticket | Support | reference |
| Referral Commission | Referral | consume payment events |
| Tenant foundation | Tenant/Platform boundary | reference |

---

# 8. Critical Ownership Rules

## Content Slot

`content_slot_id` is the stable production context anchor.

Downstream domains must reference it rather than inventing another project identity.

```text
Planner
→ Analyzer
→ Blueprint
→ Asset
→ Editor
→ Export
→ Analytics
```

## Research Evidence

Research remains the source of research evidence and source truth.

Analyzer may interpret and enrich it, but must not create a competing canonical Research/Evidence model.

## Product vs Entitlement

```text
Product = what can be sold
Entitlement = what the user owns/can use
```

They must remain separate.

## Order vs Payment

```text
Order = what was purchased
Payment = how it was settled
```

A provider response is never the canonical Order state.

## Membership vs Role

```text
Membership = commercial access / entitlement
Role = permission / authorization
```

Neither may silently become the other.

## Workspace vs Tenant

```text
Workspace = operational content context
Tenant = organizational / white-label boundary
```

They remain separate concepts.

---

# 9. Cross-Domain Write Rule

A domain must not directly mutate another domain's tables.

Forbidden:

```text
Domain A
→ direct SQL
→ Domain B tables
```

Preferred:

```text
Application Command
Domain Service
Event
Read Model
```

---

# 10. Event Ownership

The domain that owns the business action owns the meaning of its domain event.

Example:

```text
Payment Domain
→ PaymentPaid
```

Event Infrastructure owns:

```text
delivery
retry
routing
DLQ
replay
```

---

# 11. Provider Ownership

Providers are infrastructure dependencies, not replacements for business-domain ownership.

```text
Provider Adapter
→ external vendor

Business Domain
→ canonical business state
```

Provider-specific schemas must not leak throughout domain code.

---

# 12. Storage Ownership

Storage owns binary lifecycle.

Business domains own the meaning and relationship of the file.

```text
StorageObject
← referenced by business entity
```

Purging a StorageObject must not silently delete its business record.

---

# 13. Configuration Ownership

Configuration owns:

```text
key
value
schema
scope
version
effective value
```

The consuming domain owns:

```text
business meaning
validation
enforcement
state transition
```

Configuration must not become a universal business-logic service.

---

# 14. Conflict Resolution Procedure

When an inconsistency is found:

```text
1. Identify the exact conflicting statements.
2. Identify each document's authority level.
3. Apply the Source of Truth hierarchy.
4. Record the winning rule.
5. Mark the losing statement for correction.
6. Do not edit `original/` during audit.
7. Add the required change to `change-plan.md`.
8. Re-audit all downstream references after correction.
```

---

# 15. No Silent Resolution

AI Builder, developer, or reviewer must **not** resolve a conflict by choosing whichever statement appears easier to implement.

If authority cannot be determined:

```text
OPEN DECISION
```

must be recorded.

---

# 16. Final Rule for AI Builder

Before implementing a requirement:

```text
Read applicable source documents
↓
Resolve authority
↓
Confirm domain owner
↓
Confirm allowed dependency direction
↓
Implement
↓
Test against source-of-truth
```

If implementation conflicts with a higher-authority document:

```text
STOP
→ report conflict
→ do not invent a workaround
```
