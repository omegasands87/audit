# Lifecycle / State Audit

**Status:** PARTIAL — dedicated lifecycle/state sweep completed; cross-domain state closure remains open.

## Scope

Reviewed lifecycle/state definitions across the available Core Contracts relevant to identity, commercial access, transactions, storage, events, workspace/content context, planning, analysis, and production.

## Verified Existing Lifecycle Definitions

| Domain | Verified states / lifecycle | Result |
|---|---|---|
| User / Account | Created → Active → Suspended/Blocked → Reactivated | Sufficient at contract level |
| Session | Active → Expired / Revoked | Sufficient at contract level |
| Product | Draft → Active → Inactive / Archived | Sufficient at contract level |
| Order | Draft → Pending Payment → Paid → Fulfilled; exception states include Expired, Cancelled, Payment Failed, Refunded, Partially Refunded | Defined, but cross-payment/fulfillment matrix incomplete |
| Storage Object | Created → Uploading → Available → Expiring → Purge Pending → Purged; Purge Failed | Defined, recovery policy incomplete |
| Analyzer Run | Queued → Running → Partial / Completed / Failed / Cancelled / Superseded | Sufficient at contract level |
| Blueprint | Draft → Generating → Ready for Review → Needs Revision → Approved / Superseded | Sufficient at contract level |
| Content Slot | Production lifecycle exists and Content Slot is the stable context anchor | Cross-domain transition authority incomplete |

## Verified Lifecycle Gaps

### LIFECYCLE-001 — Subscription lifecycle

**Severity:** High

Membership/Product subscription semantics exist, but there is no single canonical Subscription state machine defining states, transitions, triggers, actors, dates, renewal/cancellation/expiry behavior, and terminal states.

**Required:** canonical Subscription lifecycle artifact/contract.

### LIFECYCLE-002 — Entitlement failure / reversal

**Severity:** High

Entitlement grant/consumption concepts exist, but reservation/commit/release, failed consumption, retry, cancellation, reversal, refund-after-fulfillment and recovery outcomes are not consolidated into one state/transition model.

**Required:** canonical Entitlement transition matrix.

### LIFECYCLE-003 — Order → Payment → Fulfillment transition matrix

**Severity:** High

Order and payment states are defined, but all combinations of failed attempts, retries, approval, expiry, refund, partial refund and fulfillment failure are not expressed in one authoritative transition matrix.

**Required:** cross-domain transaction state matrix.

### LIFECYCLE-004 — Content Slot → Blueprint → Asset → Editor → Export

**Severity:** High

Individual lifecycle concepts exist, but ownership and legal cross-domain transitions are not fully specified as one production lifecycle matrix.

**Required:** production pipeline transition matrix with owner, command, event, precondition and resulting state.

### LIFECYCLE-005 — Event retry / DLQ / replay / resolution

**Severity:** Medium

Event versioning, retry and dead-letter concepts exist, but operational transitions from retry to DLQ, replay, successful resolution and terminal failure are not fully canonicalized.

**Required:** event-processing state matrix.

### LIFECYCLE-006 — Storage PURGE_FAILED recovery

**Severity:** Medium

`PURGE_FAILED` exists as an exception state, but maximum retry, backoff, manual resolution, alerting and terminal handling are not fully specified.

**Required:** storage purge recovery policy.

### LIFECYCLE-007 — Workspace / Content Plan / Content Slot authority

**Severity:** Medium

Workspace, Content Plan and Content Slot are separately described, but transition authority and cross-boundary commands are not fully consolidated.

**Required:** context lifecycle ownership/command matrix.

## Closure Rule

This audit cannot be marked COMPLETE until every identified lifecycle gap has either:

1. an authoritative source contract already present and explicitly referenced; or
2. a documented decision that the lifecycle is intentionally out of scope.

No source document has been modified by this audit.

## Source Documents Reviewed

- Core Contract #1 — Auth & Session
- Core Contract #2 — Role & Permission
- Core Contract #4 — Product/Pricing/Entitlement
- Core Contract #5 — Order & Payment
- Core Contract #7 — Storage & File Lifecycle
- Core Contract #8 — Audit, Events & Notifications
- Core Contract #9 — Workspace, Content Slot & Project Context
- Core Contract #11 — Planner, Content Idea & Calendar
- Core Contract #12 — Analyzer & Multi-Source Content Intelligence
- Core Contract #13 — Content Production Blueprint / Script Review
