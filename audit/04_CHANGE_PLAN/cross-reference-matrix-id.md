# Phase 5 — Cross-Reference Synchronization Matrix

## Status
**SYNCHRONIZED**

| Authority | Downstream references | Required rule |
|---|---|---|
| Final Business Decision Register | PRD, contracts, architecture, implementation | locked business decisions are preserved |
| Phase 4 Source-of-Truth | all controlled documents | canonical ownership/authority |
| Role/Permission Contract #2 | UI, API, implementation | authorization only |
| Product/Pricing/Entitlement Contract #4 | PRD, billing UI, API | commercial access and entitlement |
| Order/Payment Contract #5 | fulfillment, API, operations | financial state separate from fulfillment |
| Research Contract #10 | Analyzer, Planner, UI | Research owns source/evidence |
| Planner Contract #11 | Content Context, UI | planning decisions do not take Content Slot ownership |
| Subscription Addendum | entitlement, billing, API, implementation | explicit subscription lifecycle |
| Security Addendum | architecture, UI, operations | security boundary and content protection controls |
| Canonical Registries | implementation/API/events | discovery only; domain remains semantic authority |
| UI Synchronization | design/implementation | state-driven UI and terminology |
| Operations Synchronization | deployment/worker/provider/storage | operational behavior follows architecture/contracts |

## Cross-Domain Trace

```text
Business Decision
 → Source of Truth
 → Domain Owner
 → Contract
 → Lifecycle/State
 → API/Event
 → UI
 → Operations
 → Acceptance Criteria
```

Any downstream document that contradicts a row above is a Phase 6 verification failure.
