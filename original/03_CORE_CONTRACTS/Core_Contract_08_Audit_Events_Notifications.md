# Core Contract #8 — Audit, Events & Notifications

## Status

**Draft for Core Design — based on the finalized PRD, Final Business Decision Register, and Core Contracts #1–#7**

This contract defines the platform-wide foundation for:

```text
Domain Action
    ↓
Domain Event
    ├── Audit
    ├── Notification
    ├── Background Job
    └── Other Domain Reactions
```

The goal is to prevent every module from building its own event, audit, and notification mechanism.

Core principle:

> **Business domains own their state. The shared Event/Audit/Notification infrastructure records, distributes, and reacts to state changes without taking ownership of domain logic.**

---

# 1. Scope

This contract covers:

- Event;
- Event Envelope;
- Event Bus;
- Event Handler;
- Event Idempotency;
- Event Ordering;
- Event Retry;
- Dead Letter Queue;
- Audit Log;
- Audit Event;
- Audit Integrity;
- Notification;
- Notification Preference;
- Notification Template;
- In-app Notification;
- Email Notification foundation;
- Notification Delivery;
- Read/Unread state;
- Background event processing;
- observability references.

It does not define:

- specific business domain rules;
- role/permission logic;
- payment state;
- product/entitlement state;
- storage state;
- provider implementation.

Those domains emit and consume events through this contract.

---

# 2. Source Principles

The finalized platform architecture treats Auth, Role & Permission, Storage, Billing, Provider infrastructure, and the three major engines as shared/connected infrastructure.

The PRD also establishes persistent work and cross-session continuity, making system events and audit records important for reliable state transitions. The platform must retain server-side results across refresh/logout/login rather than depending only on browser state. fileciteturn12file2L58-L66

Admin Godmode requires audit history for sensitive changes, and Finance/Support/Provider/Storage domains all require traceability.

Therefore:

```text
Audit
and
Event
```

must be platform-level infrastructure.

---

# 3. Event vs Audit vs Notification

These are three different concepts.

## Event

Answers:

> **"Something happened in the system."**

Example:

```text
PaymentPaid
```

## Audit

Answers:

> **"What happened, who caused it, to what target, and what changed?"**

Example:

```text
Admin A
changed Product Price
from 100.000
to 120.000
```

## Notification

Answers:

> **"Who should be informed about something?"**

Example:

```text
Your payment was successful.
```

A single action may produce all three:

```text
Payment Paid
   ↓
Event
   ├── Audit
   └── Notification
```

---

# 4. Event Envelope

Every domain event should use a common envelope.

Minimum:

| Field | Purpose |
|---|---|
| `event_id` | unique event identity |
| `event_type` | event name |
| `event_version` | schema version |
| `occurred_at` | business occurrence time |
| `published_at` | event publication time |
| `producer` | originating service/domain |
| `actor_user_id` | human/system actor if applicable |
| `subject_type` | affected entity type |
| `subject_id` | affected entity |
| `correlation_id` | cross-service workflow reference |
| `causation_id` | event that caused this event |
| `tenant_id` | future tenant context |
| `payload` | event data |
| `metadata` | additional context |

---

# 5. Event Naming

Use stable domain-oriented names.

Examples:

```text
UserCreated
SessionRevoked

RoleAssigned
PermissionChanged

ConfigurationChanged

ProductActivated
ProductArchived
EntitlementGranted
EntitlementConsumed

OrderCreated
PaymentPaid
PaymentFailed
RefundApproved

ProviderHealthChanged

StorageObjectAvailable
StorageObjectPurged

TicketCreated
TicketReplied
TicketResolved
TicketClosed

ReferralCommissionCreated
ReferralCommissionCancelled
ReferralMilestoneAchieved
```

Avoid UI-specific names such as:

```text
ClickedBuyButton
OpenedDashboardTab
```

unless analytics specifically requires them.

---

# 6. Event Versioning

Events must be versioned.

Example:

```text
PaymentPaid.v1
PaymentPaid.v2
```

Consumers should know which schema they are receiving.

Do not silently change the meaning of an existing event version.

---

# 7. Event Immutability

Published events are immutable.

If a correction is necessary:

```text
Original Event
+
Compensating Event
```

Example:

```text
PaymentPaid
↓
RefundApproved
```

Do not rewrite the original `PaymentPaid` event.

---

# 8. Event Bus

Core uses an Event Bus abstraction.

```text
Domain Service
      ↓
Event Bus
      ↓
Subscribers / Handlers
```

Potential implementation:

```text
Synchronous
or
Asynchronous
```

Business-critical events that trigger durable operations should use reliable asynchronous delivery where appropriate.

---

# 9. Event Handler

A handler consumes a specific event type.

Example:

```text
PaymentPaid
   ↓
EntitlementGrantHandler
```

Another:

```text
TicketResolved
   ↓
NotificationHandler
```

Handlers must be isolated from the producing domain.

---

# 10. Handler Idempotency

A handler must be safe to execute more than once.

Example:

```text
PaymentPaid
event_id = EVT-123

Handler runs
→ entitlement granted

Same event retried
→ no duplicate entitlement
```

Use:

```text
event_id
handler_id
```

as an idempotency key.

---

# 11. Event Delivery Semantics

Core should target:

> **At-least-once delivery**

rather than relying on a fragile exactly-once assumption.

Therefore every important handler must be idempotent.

The architecture should not depend on:

```text
event will only ever arrive once
```

---

# 12. Event Ordering

Some domains require ordering.

Example:

```text
PaymentInitiated
→ PaymentPaid
→ OrderFulfilled
```

Events for the same aggregate/workflow should preserve logical ordering where required.

Example partition key:

```text
order_id
```

or:

```text
user_id
```

depending on domain.

Global event ordering is not required.

---

# 13. Transaction Boundary

A domain should not publish an event indicating success before its own transaction is durable.

Recommended pattern:

```text
Domain Transaction
    ↓
Persist State
    ↓
Persist Event Outbox
    ↓
Commit
    ↓
Publish Event
```

This prevents:

```text
Database = success
Event = lost
```

or:

```text
Event = published
Database = rolled back
```

---

# 14. Outbox Pattern

For critical events:

```text
Domain DB
├── Business Record
└── Outbox Event
```

After commit:

```text
Outbox Worker
→ Event Bus
```

This is especially important for:

- Payment;
- Entitlement;
- Referral;
- Storage;
- Support;
- Security.

---

# 15. Event Retry

When handler processing fails:

```text
Event
→ Handler Error
→ Retry
```

Use backoff.

Example:

```text
Retry 1
Retry 2
Retry 3
...
```

Maximum attempts are configurable.

---

# 16. Dead Letter Queue

After retry exhaustion:

```text
Event
→ Dead Letter Queue
```

Admin/Operations can inspect:

```text
event_id
event_type
handler
error
attempt_count
last_attempt
```

A safe replay mechanism may be provided.

---

# 17. Event Replay

Authorized Admin/Operations may replay an event where supported.

Replay must preserve:

```text
original_event_id
replay_id
replayed_by
replayed_at
reason
```

Replay must not bypass normal authorization or idempotency.

---

# 18. Correlation ID

Every major workflow should carry:

```text
correlation_id
```

Example:

```text
Order
→ Payment
→ Entitlement
→ Notification
```

All events can share:

```text
correlation_id = ORDER-1001
```

This enables end-to-end tracing.

---

# 19. Causation ID

Where one event causes another:

```text
PaymentPaid
    ↓
EntitlementGranted
```

the second event contains:

```text
causation_id = PaymentPaid.event_id
```

This creates an event causal chain.

---

# 20. Audit Log Entity

Conceptual:

```text
AuditLog
```

Minimum:

| Field | Purpose |
|---|---|
| `audit_id` | unique identity |
| `actor_type` | user / system / provider |
| `actor_id` | actor identity |
| `action` | action performed |
| `target_type` | affected resource |
| `target_id` | resource identity |
| `before` | previous state snapshot |
| `after` | new state snapshot |
| `reason` | reason/comment |
| `timestamp` | event time |
| `ip_address` | source where applicable |
| `session_id` | session |
| `correlation_id` | workflow |
| `metadata` | additional context |

---

# 21. Audit Sources

Audit actor types:

```text
USER
ADMIN
SYSTEM
PROVIDER
JOB
```

Example:

```text
actor_type = SYSTEM
action = ENTITLEMENT_GRANTED
```

---

# 22. Audit Before / After

For mutable state changes:

```text
Before:
price = 100000

After:
price = 120000
```

This is especially important for:

- Product pricing;
- Role permissions;
- Configuration;
- Payment provider settings;
- Security settings.

---

# 23. Audit Reason

Reason should be required for high-risk actions.

Examples:

```text
Refund
Role Change
Super Admin Assignment
Security Policy Change
Provider Credential Change
Manual Entitlement Adjustment
Manual Payment Approval
```

Example:

```text
reason:
"Customer support compensation"
```

---

# 24. Audit Immutability

Audit logs are append-only.

Normal Admin UI must not allow:

```text
Edit
Delete
Rewrite
```

of historical audit entries.

If a correction is needed:

> create a new compensating audit event.

---

# 25. Audit Integrity

Audit infrastructure should support integrity protection.

Possible mechanisms:

```text
append-only storage
hash chaining
write restrictions
separate retention policy
```

Exact implementation belongs to Security/Architecture.

Core invariant:

> Audit history must not be silently alterable by ordinary Admin actions.

---

# 26. Audit Retention

Retention for audit logs is not fixed by current business decisions.

Therefore:

```text
audit.retention
```

must be configurable by policy.

However, financial/security audit records should never be purged merely because a related business object was deleted.

---

# 27. Sensitive Audit Fields

Do not store secrets in audit:

```text
API Secret
Password
Private Key
Payment Secret
Full Card Data
```

Instead record:

```text
credential_changed = true
```

or:

```text
credential_ref
```

where appropriate.

---

# 28. Audit Categories

Suggested:

```text
AUTH
SECURITY
ROLE
CONFIGURATION
PRODUCT
ENTITLEMENT
ORDER
PAYMENT
REFUND
PROVIDER
STORAGE
SUPPORT
REFERRAL
ANALYTICS
TENANT
```

---

# 29. Notification Entity

Conceptual:

```text
Notification
```

Minimum:

| Field | Purpose |
|---|---|
| `notification_id` | unique |
| `recipient_user_id` | recipient |
| `notification_type` | event/notification category |
| `title` | title |
| `body` | message |
| `channel` | in_app / email / future |
| `status` | pending / sent / failed / read |
| `reference_type` | related object |
| `reference_id` | related object |
| `created_at` | timestamp |
| `sent_at` | delivery |
| `read_at` | read state |

---

# 30. Notification Channels

Minimum:

```text
In-App
```

Prepared:

```text
Email
```

Future:

```text
Push
SMS
WhatsApp
```

The current core does not need to activate every channel.

---

# 31. In-App Notification

In-app notifications should support:

```text
Unread
Read
```

Example:

```text
3 unread notifications
```

Read state belongs to the recipient, not to the source event.

---

# 32. Email Notification

Email should use a delivery adapter.

```text
Notification Service
      ↓
Email Adapter
      ↓
Email Provider
```

Provider selection is configurable.

---

# 33. Notification Template

Templates should be configurable.

Conceptual:

```text
NotificationTemplate
```

Fields:

```text
template_id
notification_type
channel
language
subject_template
body_template
version
status
```

Example variables:

```text
{{user_name}}
{{order_number}}
{{amount}}
{{currency}}
{{ticket_id}}
{{product_name}}
```

---

# 34. Localization

Notifications must respect:

```text
User Language
→ Market Default
→ System Fallback
```

Minimum language support:

```text
Indonesia
English
```

Additional languages can be added later.

Notification templates should be separated from business events.

---

# 35. Notification Preferences

User may control non-mandatory notifications.

Conceptual:

```text
NotificationPreference
```

Examples:

```text
billing
support
research
analytics
product
system
marketing
```

Possible settings:

```text
in_app = true
email = false
```

Mandatory security notifications cannot be disabled through normal user preferences.

---

# 36. Notification Categories

Suggested:

```text
Security
Billing
Payment
Support
Referral
Analytics
Research
System
Product
Marketing
```

---

# 37. Mandatory Notifications

Examples:

```text
Security incident
Account blocked
Password/security reset
Payment confirmation
Manual payment verification result
```

Exact mandatory list is configurable by policy, but security-critical notifications must bypass normal opt-out where required.

---

# 38. Notification Trigger

Notifications should be triggered by events.

Example:

```text
PaymentPaid
   ↓
Notification Handler
   ↓
"Payment successful"
```

Do not put notification delivery code directly inside the Payment domain transaction.

---

# 39. Notification Idempotency

A single business event should not send duplicate notifications unless explicitly intended.

Use:

```text
event_id
notification_type
recipient_id
channel
```

as a deduplication reference.

---

# 40. Notification Delivery State

Recommended:

```text
PENDING
PROCESSING
SENT
FAILED
READ
```

`READ` is primarily relevant for in-app.

Email may remain:

```text
SENT
```

with provider delivery metadata if available.

---

# 41. Notification Retry

If channel delivery fails:

```text
PENDING
→ PROCESSING
→ FAILED
→ RETRY
```

Retry policy is configurable.

Permanent failures move to:

```text
DEAD / FAILED_PERMANENT
```

with a delivery record.

---

# 42. Notification Delivery Record

Separate delivery attempts from the notification itself.

Conceptual:

```text
NotificationDelivery
```

Fields:

```text
delivery_id
notification_id
channel
provider
attempt
status
provider_reference
sent_at
failed_at
error
```

This allows one notification to have multiple delivery attempts.

---

# 43. Notification vs Event

Not every event needs a user notification.

Example:

```text
StoragePurged
→ Event

No member notification necessarily required.
```

But:

```text
PaymentPaid
→ Event
→ Audit
→ Notification
```

Notification policy decides whether an event is user-visible.

---

# 44. Notification Policy

Conceptual mapping:

```text
Event
   ↓
Notification Policy
   ↓
Recipients
   ↓
Channels
   ↓
Template
```

Example:

```text
TicketReplied
→ ticket owner
→ in-app
→ email if enabled
```

---

# 45. Recipient Resolution

Notification system must not hard-code recipients.

Recipient types:

```text
User
Role
Team
Admin
Owner
Assignee
Tenant
System
```

Example:

```text
TicketReplied
→ ticket.owner

ManualPaymentApproved
→ order.customer
```

---

# 46. System Events Without User Recipients

Some events are operational only:

```text
ProviderHealthChanged
StoragePurgeFailed
WebhookFailed
```

They may notify:

```text
Admin
Operations
```

without notifying normal members.

---

# 47. Notification Escalation

Future support/SLA use:

```text
Ticket approaching SLA
   ↓
Notify Assignee
   ↓
Escalate Manager
   ↓
Notify Admin
```

Escalation rules belong to Support policy, while Notification provides delivery.

---

# 48. Event-to-Notification Examples

### Payment

```text
PaymentPaid
→ Customer notification
```

### Manual Transfer

```text
ManualPaymentApproved
→ Customer notification
```

### Support

```text
TicketReplied
→ Member notification
```

### Referral

```text
CommissionCancelled
→ Upline notification
```

This reflects the finalized referral rule that an upline is informed when a pending commission is cancelled because of a valid refund.

---

# 49. Event-to-Audit Examples

### Role Change

```text
RoleAssigned
→ Audit
```

### Product Price Change

```text
ProductPriceChanged
→ Audit
```

### Payment Approval

```text
PaymentPaid
actor = Admin
→ Audit
```

### Storage Purge

```text
StoragePurged
actor = System
→ Audit
```

---

# 50. Event-to-Job Examples

### Payment

```text
PaymentPaid
→ Entitlement Grant Job
```

### Storage

```text
StorageObjectPurgePending
→ Purge Job
```

### Analytics

```text
PerformanceDataUpdated
→ Learning Job
```

### Research

```text
ResearchSyncRequested
→ Provider Job
```

---

# 51. Event Categories

Core categories:

```text
Identity
Authorization
Configuration
Product
Entitlement
Order
Payment
Refund
Provider
Storage
Support
Referral
Analytics
Notification
Tenant
```

---

# 52. Event Payload Rules

Event payload should contain enough information for the consumer to act without querying unrelated UI state.

But avoid embedding huge binary payloads.

Use references:

```text
storage_object_id
user_id
order_id
payment_id
ticket_id
```

instead of binary file contents.

---

# 53. Event Payload Versioning

When schema changes:

```text
event_version = 2
```

Consumers can migrate independently.

Avoid breaking all consumers when one domain evolves.

---

# 54. Event Retention

Not all events need the same retention.

Suggested policy:

```text
Critical financial/security events
→ long retention

Operational events
→ configurable shorter retention

High-volume telemetry
→ separate analytics retention
```

Exact retention is not currently fixed by business decisions.

---

# 55. Audit vs Event Retention

Do not assume:

```text
Event deleted
→ Audit deleted
```

Audit and event retention are independent.

Critical audit may need to remain after the original event has expired from the event stream.

---

# 56. Notification Storage Retention

Notifications are user-facing state.

Policy should support:

```text
notification.retention_days
```

but no final business value is currently defined.

Read notifications may be cleaned according to policy.

Mandatory compliance records are separate.

---

# 57. Event Bus Failure

If Event Bus is temporarily unavailable:

```text
Domain Transaction
→ Outbox retained
→ Retry publication
```

Business state should not be lost because the event infrastructure is temporarily down.

---

# 58. Notification Service Failure

If Notification Service is unavailable:

```text
Event
→ Event remains durable
→ Notification handler retries
```

A failed notification must not roll back a successful payment, order, or entitlement transaction.

---

# 59. Audit Service Failure

For high-risk actions:

> audit persistence is part of the action's reliability boundary.

If audit is mandatory and unavailable:

```text
High-risk action
→ may be blocked
```

depending on security policy.

For low-risk events, asynchronous audit may be acceptable.

---

# 60. Observability

Event/Audit/Notification infrastructure should expose:

```text
Event throughput
Event failure rate
Handler latency
Retry count
Dead letter count
Notification delivery rate
Notification failure rate
Audit write failures
```

This is operational infrastructure.

---

# 61. Traceability

A complete workflow should be traceable:

```text
correlation_id
    ↓
Order
    ↓
Payment
    ↓
Entitlement
    ↓
Notification
```

For Support:

```text
ticket_id
    ↓
Ticket Event
    ↓
Audit
    ↓
Notification
```

For Storage:

```text
storage_object_id
    ↓
Purge Event
    ↓
Audit
```

---

# 62. API / Internal Contract

Conceptual internal APIs:

```text
EventBus.publish(event)
EventBus.subscribe(event_type, handler)

Audit.record(entry)

Notification.create(notification)
Notification.send(notification)

NotificationPreference.get(user_id)
NotificationPreference.update(user_id, preference)
```

The implementation may use queues, brokers, or internal service calls.

---

# 63. Domain Boundary Rules

Domains should:

```text
Own:
Business State

Emit:
Domain Events

Call:
Core Services only through contracts
```

Domains should not:

```text
write directly to notification tables
write directly to audit tables
implement provider notification delivery
```

except through the shared infrastructure interface.

---

# 64. Example End-to-End — Paid Product

```text
Customer
   ↓
Order Created
   ↓
Payment Paid
   ↓
Order Paid
   ↓
Entitlement Grant
   ↓
Order Fulfilled
```

Events:

```text
OrderCreated
PaymentPaid
EntitlementGranted
OrderFulfilled
```

Audit:

```text
Order Created
Payment Confirmed
Entitlement Granted
```

Notification:

```text
Payment Successful
Product Ready
```

All may share:

```text
correlation_id
```

---

# 65. Example End-to-End — Manual Transfer

```text
Order
 ↓
Manual Payment Pending
 ↓
Support Ticket
 ↓
Proof Uploaded
 ↓
Admin Approves Ticket
 ↓
Payment Paid
 ↓
Entitlement Granted
```

Events:

```text
TicketCreated
AttachmentAvailable
ManualPaymentApproved
PaymentPaid
EntitlementGranted
```

Audit:

```text
Admin approved manual transfer
```

Notification:

```text
Payment approved
```

---

# 66. Example End-to-End — Referral Refund

```text
Downline Purchases
   ↓
Payment Paid
   ↓
Commission Pending
   ↓
Refund within 3 days
   ↓
Refund Approved
   ↓
Commission Cancelled
```

Events:

```text
PaymentPaid
RefundApproved
ReferralCommissionCancelled
```

Audit:

```text
Refund approved
Commission cancelled
```

Notification:

```text
Upline informed
```

---

# 67. Example End-to-End — Storage Purge

```text
Export Available
   ↓
48h Retention
   ↓
Purge Eligible
   ↓
Purge Worker
   ↓
Binary Deleted
```

Events:

```text
StorageObjectExpiring
StorageObjectPurgePending
StorageObjectPurged
```

Audit:

```text
System purged object
```

User notification is optional and policy-driven.

---

# 68. Core Invariants

```text
1. Domain events are immutable.

2. Event delivery is treated as at-least-once.

3. Critical handlers are idempotent.

4. Audit is append-only.

5. Audit does not store secrets.

6. Notifications are reactions to events, not part of domain transactions.

7. Notification failure does not roll back successful business transactions.

8. Event failure does not erase committed domain state.

9. Critical state transitions use durable event publication patterns.

10. correlation_id enables workflow tracing.

11. causation_id enables event causality.

12. Historical audit cannot be silently rewritten.

13. Security/financial high-risk actions have stronger audit requirements.

14. User notification preferences cannot disable mandatory security notifications.

15. Provider/external callbacks are normalized before entering the domain event system.
```

---

# 69. Definition of Done

Core Contract #8 is complete when:

1. Shared Event Envelope exists.
2. Event versioning exists.
3. Event Bus abstraction exists.
4. At-least-once delivery is supported.
5. Handler idempotency exists.
6. Retry exists.
7. Dead Letter Queue exists.
8. Outbox pattern is supported for critical domain events.
9. Correlation ID exists.
10. Causation ID exists.
11. Append-only Audit Log exists.
12. Audit before/after snapshots can be recorded.
13. High-risk actions require audit.
14. Notification entity exists.
15. In-app notifications exist.
16. Email notification foundation exists.
17. Notification templates support localization.
18. Notification preferences exist.
19. Notification delivery attempts are tracked.
20. Notification retry exists.
21. Event-to-notification policy exists.
22. Event-to-audit policy exists.
23. Operational monitoring exists.
24. Identity, Billing, Storage, Provider, Support, Referral and future Tenant domains can use the same event/audit/notification infrastructure.
25. Domain services do not need to implement their own notification/audit framework.

---

# 70. Dependencies

Depends on:

```text
Core Contract #1
Identity / Session

Core Contract #2
Role / Permission

Core Contract #3
Configuration
```

Integrates with:

```text
Core Contract #4
Product / Entitlement

Core Contract #5
Order / Payment

Core Contract #6
Provider

Core Contract #7
Storage
```

Used by:

```text
Research
Planner
Analyzer
Script
Asset
Editor
Analytics
Support
Referral
Admin Godmode
Future Tenant / White-label
```

---

# 71. Next Contract

The next recommended contract is:

> **Core Contract #9 — Notification, Localization & Communication** only if a more detailed communication layer is needed.

Otherwise, because Core Contract #8 already establishes the notification foundation, the next major domain contract should move back to the platform's business engine:

> **Core Contract #9 — Workspace, Content Slot & Project Context**

This should define the ownership/context layer used by Research → Planner → Analyzer → Script → Asset → Editor → Analytics.
