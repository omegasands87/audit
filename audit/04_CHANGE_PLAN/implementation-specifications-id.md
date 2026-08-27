# Phase 5 — Implementation Specification Synchronization

## Status
**UPDATED / CONTROLLED**

The existing implementation documents under `original/04_IMPLEMENTATION/` remain immutable. This document is the controlled synchronization layer for implementation.

## Mandatory Build Rules

1. Build from the approved authority hierarchy; never from an isolated document.
2. Do not create a duplicate entity/service for an existing authoritative owner.
3. Role/Permission controls authorization; commercial access is evaluated through Product/Membership/Entitlement rules.
4. Agency Mode is not a System Role.
5. Research owns canonical source/evidence; Analyzer owns derived interpretation.
6. Subscription lifecycle is explicit and authoritative.
7. Order, Payment and Fulfillment remain separate state boundaries.
8. Payment refund cannot directly mutate Entitlement state; use the approved boundary/event workflow.
9. Retryable side effects require idempotency; ambiguous provider outcomes require reconciliation.
10. Event replay must be safe; exhausted processing uses DLQ.
11. State machines must match the canonical state/lifecycle definitions.
12. Cross-domain writes require an approved command/event boundary.
13. Configuration cannot override authorization, tenant isolation or security boundaries.
14. Content Slot → Blueprint → Asset → Editor → Export must preserve ownership and state handoffs.
15. UI success is shown only after authoritative backend confirmation.
16. Operational behavior must define failure, retry, recovery and observability paths.
17. UTC is the canonical persisted/system clock.
18. Privacy deletion must respect mandatory financial/audit retention.

## Vertical Slice Gate

Before implementation of a slice, its specification must identify:

```text
Business Decision
PRD requirement
Domain owner
Contract
Entities
Invariants
State/lifecycle
API commands/queries
Events
UI states
Operational behavior
Acceptance criteria
```

A missing authority or unresolved contradiction blocks implementation of that boundary; the builder must not invent behavior.

## Reference Implementation Sources

- `original/04_IMPLEMENTATION/Final_Vertical_Slice_Order.md`
- `original/04_IMPLEMENTATION/Implementation_Roadmap_P0_P1_P2_Dependency_Acceptance_Gates.md`
- `original/04_IMPLEMENTATION/Implementation_Specification_Per_Vertical_Slice.md`

These remain baseline sources. This synchronization layer applies the Phase 4/5 authority rules to their implementation.

## Non-Changes
No product concept, monetization, pricing, or locked business decision is changed.
