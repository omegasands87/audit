# Final Lifecycle / State Definitions — Working Correction Set

## Status
CONTROLLED WORKING DOCUMENT — PHASE 5

## Canonical lifecycles
- Subscription: ACTIVE → CANCELLED_PENDING_END → EXPIRED.
- Entitlement: RESERVED → COMMITTED/CONSUMED or RELEASED; reversal is explicit and auditable.
- Payment: REQUESTED → PROVIDER_PENDING → PAID / FAILED / RECONCILIATION_REQUIRED.
- Production: Content Slot → Blueprint → Asset Preparation/Generation → Editor → Export.
- Storage deletion: REQUESTED → PROCESSING → PURGED / PURGE_FAILED.
- Event processing: delivery → retry → DLQ → replay/resolution.

All retryable transitions are idempotent.
