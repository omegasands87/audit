# Source of Truth — Audit Baseline

## Status
**AUDIT BASELINE — findings documented before correction**

`original/` remains immutable during audit.

## 1. Authority Hierarchy

```text
1. Final Business Decision Register
2. PRD Final
3. Approved Core Contracts
4. Approved Core Architecture
5. Approved Implementation / Slice Specifications
6. Code / Current Implementation
```

A lower-level document must not silently override a higher-level business or domain rule.

## 2. Business Authority

The Final Business Decision Register is authoritative for locked business decisions including billing, subscription, package, entitlement behavior, refund, referral, commission, manual transfer, support policy, security policy, localization, currency and White-label commercial rules.

## 3. Product Authority

The PRD is authoritative for product scope, feature requirements, user journeys, functional requirements and UX/product intent, unless superseded by a final business decision.

## 4. Domain Contract Authority

Each Core Contract is authoritative for its own domain entities, invariants, lifecycle, commands, queries and boundaries. One persistent business entity must have one authoritative owner.

## 5. Architecture Authority

Architecture is authoritative for domain boundaries, application boundaries, persistence boundaries, event boundaries, worker boundaries, provider boundaries, storage, security, configuration and tenant foundation.

## 6. Entity Ownership Baseline

| Concept | Owner |
|---|---|
| User / Session | Identity |
| Role / Permission | Authorization |
| Configuration / Feature Flag | Configuration |
| Product / Price | Product |
| Entitlement | Entitlement |
| Order | Order |
| Payment / Refund | Payment |
| Provider infrastructure | Provider Infrastructure |
| Storage Object | Storage |
| Event delivery | Event Infrastructure |
| Audit Record | Audit |
| Notification | Notification |
| Workspace | Workspace |
| Content Plan / Content Slot | Content Context |
| Research Workspace / Research Source / Evidence / Insight | Research |
| Planner decisions | Planner |
| Analysis Run / Interpretation | Analyzer |
| Blueprint / Variant | Blueprint |
| Support Ticket | Support |
| Referral Commission | Referral |
| Tenant foundation | Tenant / Platform boundary |

## 7. Critical Ownership Rules

### Content Slot
Content Context owns Content Slot identity, lifecycle and context. Planner owns planning decisions and requests Content Context changes through the approved contract.

### Research Evidence
Research owns canonical Source/Evidence/Research truth. Analyzer consumes and interprets it and may create derived analysis artifacts, but must not create a competing canonical research model.

### Product vs Entitlement
`Product = what can be sold.`
`Entitlement = what the user owns/can use.`

### Order vs Payment
`Order = what was purchased.`
`Payment = how it was settled.`

### Membership vs Role
`Membership/Product = commercial access.`
`Role = authorization.`

### Workspace vs Tenant
`Workspace = operational content context.`
`Tenant = organizational / White-label boundary.`

## 8. Cross-Domain Write Rule

A domain must not directly mutate another domain's tables. Cross-domain changes use application commands, approved contracts, events or read models.

## 9. Provider Rule

```text
Shared Provider Infrastructure
        ↓
Domain Adapter
        ↓
External Provider
```

Provider infrastructure owns generic routing/health/credential/adapter infrastructure. The consuming domain owns business semantics and canonical business state.

## 10. Configuration Rule

Configuration owns key/value/schema/scope/version/effective value. The consuming domain owns business meaning, validation, enforcement and state transitions. Configuration must not become a universal business-logic service.

## 11. Conflict Resolution

```text
Identify conflict
→ identify authority
→ apply hierarchy
→ record winning rule
→ record required correction
→ re-audit downstream references
```

If authority cannot be determined, mark the item `OPEN DECISION`; do not guess.

## 12. AI Builder Rule

Before implementing:

```text
Read source documents
→ resolve authority
→ confirm owner
→ confirm dependency direction
→ implement
→ test against source of truth
```

If a higher-authority conflict exists: **STOP and report it.**
