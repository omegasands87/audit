# Final Lifecycle / State Definitions — Working Correction Set

Status: **PHASE 5 CONTROLLED WORKING DOCUMENT**

## Subscription
ACTIVE → CANCELLED_PENDING_END → EXPIRED. Renewal/reactivation must be explicit.

## Entitlement
AVAILABLE → RESERVED → CONSUMED, with RESERVED → AVAILABLE (release) and approved reversal → REVERSED. Retryable transitions are idempotent.

## Payment
REQUESTED → PROVIDER_PENDING → PAID / FAILED / RECONCILIATION_REQUIRED. Provider timeout is not automatically failure.

## Order / Fulfillment
Order lifecycle and fulfillment lifecycle remain separate. Payment success does not equal fulfillment success.

## Storage
ACTIVE → DELETION_REQUESTED → DELETION_PROCESSING → PURGED / PURGE_FAILED.

## Production
Content Slot → Blueprint → Asset Preparation/Generation → Editor → Export. Each stage has owner, state and recovery behavior.

## Notification
Delivery state and read state are independent.
