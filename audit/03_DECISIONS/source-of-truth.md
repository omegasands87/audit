# Source of Truth — Final Phase 4 Reconciliation

## Status
**FINAL — Phase 4 Source-of-Truth Reconciliation complete.**

This document records the verified authority rules resulting from the audit. It does not change the website concept or business decisions already locked in the Final Business Decision Register.

`original/` remains immutable.

## 1. Authority Hierarchy

```text
1. Final Business Decision Register
2. PRD Final
3. Approved Core Contracts
4. Approved Core Architecture
5. Approved Implementation / Slice Specifications
6. Code / Current Implementation
```

A lower-level document must not silently override a higher-level rule. Where wording conflicts, the higher authority wins and downstream documentation must be corrected.

## 2. Business Authority

The Final Business Decision Register remains authoritative for locked business decisions including billing, subscription/package behavior, entitlement behavior, refund, referral, commission, manual transfer, support policy, security policy, localization, currency and White-label commercial rules.

## 3. Product Authority

The PRD is authoritative for product scope, feature requirements, user journeys, functional requirements and UX/product intent, subject to final business decisions.

Feature access requires both:

```text
Required commercial entitlement
AND
Required authorization/permission
```

Role is never an independent commercial entitlement source.

## 4. Domain Contract Authority

Each Core Contract owns its own entities, invariants, lifecycle, commands, queries and domain semantics. A persistent business entity has one authoritative owner.

## 5. Architecture Authority

Architecture owns system/domain boundaries, application boundaries, persistence boundaries, event boundaries, worker boundaries, provider boundaries, storage, security, configuration and tenant foundation.

## 6. Canonical Entity Ownership

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
| Research Workspace / Source / Evidence / Insight | Research |
| Planner decisions | Planner |
| Analysis Run / Interpretation | Analyzer |
| Blueprint / Variant | Blueprint |
| Support Ticket | Support |
| Referral Commission | Referral |
| Tenant foundation | Tenant / Platform boundary |
| Subscription | Subscription authority |

## 7. Critical Boundary Rules

### Membership / Role / Entitlement

```text
Membership / Product / Entitlement → commercial access
Role / Permission → authorization
```

### Agency Mode

`Agency Mode` is a commercial membership/product mode. Authorization is provided by separate Agency roles and permissions.

### Research / Analyzer

```text
Research → canonical source/evidence truth
Analyzer → analysis run + derived interpretation/output
```

Analyzer must not create a competing canonical research model. Raw Analyzer input is persisted as a Research-owned input/source so provenance remains singular.

### Product / Entitlement

`Product = what can be sold.`
`Entitlement = what the user owns/can use.`

### Order / Payment / Fulfillment

```text
Order = purchase intent/order state
Payment = financial settlement state
Fulfillment = delivery/grant execution state
```

Payment success does not imply fulfillment success.

### Content Context / Planner

Content Context owns Content Slot identity, state and context. Planner owns planning decisions and requests changes through the approved application boundary.

### Workspace / Tenant

`Workspace = operational content context.`
`Tenant = organizational / White-label boundary.`

## 8. Subscription Authority

Subscription is an authoritative domain entity with lifecycle:

```text
ACTIVE
  ↓ cancel
CANCELLED_PENDING_END
  ↓ end date
EXPIRED
```

Renewal/reactivation transitions must be explicit. Product/package definition remains owned by Product/Membership authority.

## 9. Entitlement Transaction Rule

Consumption follows:

```text
RESERVATION → COMMIT
             ↘ RELEASE / REVERSAL
```

All retryable operations require idempotency. `remaining` is derived from granted minus consumed; a persisted projection is only an optimization and not an independent authority.

## 10. Payment / Refund / Referral

- Payment owns financial refund.
- Entitlement owns entitlement reversal.
- Referral owns commission consequence.
- Refund after fulfillment must execute the corresponding entitlement reversal workflow.

## 11. Provider Rule

```text
Shared Provider Infrastructure
        ↓
Domain Adapter
        ↓
External Provider
```

Provider infrastructure owns generic routing, health, credentials and adapter infrastructure. Consuming domains own business semantics and canonical business state.

Provider timeout/ambiguous success must enter an explicit reconciliation path and must not cause double entitlement consumption.

## 12. Configuration / Security Rule

Configuration owns key/value/schema/scope/version/effective value. Consuming domains own business meaning, validation, enforcement and state transitions.

Configuration cannot override authorization, tenant isolation or security boundaries.

Security-sensitive configuration requires explicit approval/actor separation.

## 13. Event / API / Registry Rule

Central registries are discovery/governance indexes. They do not replace semantic ownership.

- Domain contracts own event semantics; event catalog indexes them.
- Domain contracts own API semantics; API registry indexes operations/errors/idempotency.
- Contract #2 owns permission semantics; permission registry indexes permissions.
- Contract #3 owns configuration semantics; configuration registry indexes keys.
- Domain contracts own state machines; state index maps cross-domain transitions.
- Architecture/contracts own entity ownership; ownership registry provides cross-domain visibility.

## 14. Production Pipeline Rule

The production chain must remain explicit:

```text
Content Slot
→ Blueprint
→ Asset Preparation / Generation
→ Editor
→ Export
```

Each stage requires explicit state, owner, retry/failure behavior and handoff contract before implementation.

## 15. Operations Rule

- Platform clock: UTC.
- User/market timezone: presentation/business-calendar context.
- Delivery state and read state are separate notification dimensions.
- Privacy deletion/anonymization is distinct from financial/audit retention.
- Backup/DR requires measurable RPO/RTO and restore acceptance tests.
- Observability has a platform minimum standard plus domain-specific metrics.

## 16. Security & Content Protection

A dedicated technical specification is required to document controls, ownership, threat assumptions, limitations and acceptance criteria. The existing final business policy remains unchanged: protected content defaults ON and protection is deterrence rather than an absolute OS-level guarantee.

## 17. Controlled Correction Rules

1. `original/` is never edited.
2. Working/final documents are maintained outside `original/`.
3. Corrections must trace to an audit finding or final business decision.
4. Cross-references must be synchronized after correction.
5. No downstream document may introduce a rule that contradicts this Source of Truth.

## 18. Phase 4 Closure

Phase 4 is complete. The verified findings now have an explicit disposition, authority hierarchy, canonical ownership rules and deterministic correction requirements.

Next phase: **Phase 5 — Controlled Corrections**.
