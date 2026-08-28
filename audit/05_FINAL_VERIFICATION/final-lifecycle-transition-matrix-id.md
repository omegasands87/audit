# Final Lifecycle / Cross-Domain Transition Matrix — Phase 6

## Status
**FINAL VERIFIED REFERENCE**

## Rules
- Domain owner is the only authority for its persistent state.
- Every transition has an initiating command/event, guard, actor/authority, side effect, emitted event, idempotency rule and failure/recovery behavior.
- Illegal transitions are rejected.

| Lifecycle | Canonical states | Transition | Owner | Failure / recovery |
|---|---|---|---|---|
| Subscription | ACTIVE, CANCELLED_PENDING_END, EXPIRED | cancel → pending; end-date → expired; approved renewal/reactivation → active according to business authority | Subscription | retry-safe, explicit effective date |
| Entitlement | AVAILABLE/RESERVED/COMMITTED/CONSUMED/RELEASED/REVERSED as applicable | reserve → commit; reserve → release; committed consumption → reversal when approved | Entitlement | idempotent operation key; reconciliation for mismatch |
| Payment | REQUESTED, PROVIDER_PENDING, PAID, FAILED, RECONCILIATION_REQUIRED | request → pending; provider result → paid/failed; ambiguous → reconciliation | Payment | never infer success from timeout; reconcile before irreversible grant |
| Order/Fulfillment | order lifecycle + fulfillment execution | order → payment → fulfillment; payment success does not imply fulfillment success | Order / Fulfillment | retry/recovery without duplicate entitlement grant |
| Refund | requested/approved/processed/failed as defined by payment authority | refund financial state remains Payment-owned | Payment | entitlement reversal is separate and auditable |
| Production | Content Slot → Blueprint → Asset Preparation/Generation → Editor → Export | each handoff occurs only after predecessor acceptance | owning production domain | failed job is retryable/idempotent; output version preserved |
| Event processing | delivered/retry/DLQ/replay | delivery → retry → DLQ → replay/resolution | Event infrastructure + producer domain semantics | idempotent handler; DLQ preserves event identity |
| Storage purge | REQUESTED, PROCESSING, PURGED, PURGE_FAILED | request → process → purged; failure → retry | Storage | retry without deleting business context |
| Workspace/Content Slot | draft/active/locked/archived as canonical contract defines | commands validated against revision/lock | Content Context | optimistic conflict returns VERSION_CONFLICT |
| Notification | delivery lifecycle + independent read state | delivery state never mutates read state implicitly | Notification | delivery retry independent from read receipt |
| Privacy deletion | requested/processing/completed/retained-exception as applicable | delete/anonymize subject to mandatory retention | Privacy/Security + owning domains | retained financial/audit records remain governed and access-controlled |

## Entitlement Remaining Rule
`remaining = granted - committed/consumed according to the authoritative entitlement contract`. Any cached projection is non-authoritative and reconciled against transaction history.

## Provider Ambiguity Rule
A timeout or ambiguous provider response must transition to `RECONCILIATION_REQUIRED`; no downstream irreversible effect is applied until the business outcome is resolved.

## Cross-Domain Refund Rule
Payment owns financial refund. Entitlement owns reversal. Referral owns commission consequence. The workflow links these outcomes through explicit command/event boundaries.

## Acceptance
A lifecycle is implementation-ready only when its state table, transition guards, owner, command/event, side effects, idempotency and recovery path are explicitly documented. This matrix closes the previously missing cross-domain lifecycle/recovery index; domain-specific semantics remain in their owners.
