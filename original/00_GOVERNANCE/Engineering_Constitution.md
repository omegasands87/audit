# Engineering Constitution

## Status

**Engineering Governance Baseline — Final**

This document defines the mandatory engineering rules for implementing the platform described by:

```text
PRD Final
Business Decision Register
Core Contracts #1–#13
Core Architecture V1
Final Vertical Slice Order
Implementation Roadmap P0/P1/P2
Implementation Specification per Vertical Slice
Environment & Deployment Strategy
```

This document is intended to prevent implementation drift.

The engineering principle is:

> **Implementation may evolve; architectural ownership, business rules, and source-of-truth boundaries must not drift silently.**

---

# 1. Purpose

The Engineering Constitution establishes rules for:

```text
Architecture
Code Structure
Domain Ownership
Data Ownership
API Boundaries
Events
Workers
Providers
Storage
Security
Configuration
Testing
Observability
Deployment
Change Management
Vendor Independence
```

It applies to:

```text
Human Developers
AI Coding Assistants
Automation
Reviewers
Future Engineering Teams
```

---

# 2. Source-of-Truth Hierarchy

When two sources appear to conflict, use this hierarchy:

```text
1. Final Business Decision Register
2. Final PRD
3. Core Contract
4. Core Architecture
5. Implementation Specification
6. Code / Current Implementation
```

Implementation code is **not** allowed to redefine a business rule silently.

If code conflicts with an approved contract:

```text
Stop
→ identify conflict
→ resolve at the appropriate document layer
→ update implementation
```

---

# 3. No Silent Business Decisions

Developers and AI assistants must not silently invent:

```text
pricing rules
billing behavior
commission rules
refund policy
entitlement rules
retention policy
permission semantics
tenant rules
provider billing semantics
product capabilities
```

If a decision is missing:

```text
OPEN DECISION
```

must be recorded.

---

# 4. One Domain — One Authoritative Owner

Every persistent business entity must have one owner.

Examples:

```text
User
→ Identity

Role / Permission
→ Authorization

Product / Price
→ Product

Entitlement
→ Entitlement

Order
→ Order

Payment
→ Payment

Research Opportunity
→ Research

Content Slot
→ Content Context / Planner boundary

Blueprint Variant
→ Blueprint

Storage Object
→ Storage

Support Ticket
→ Support

Referral Commission
→ Referral
```

Other domains may:

```text
read
reference
request
subscribe to events
```

but must not become a second owner.

---

# 5. No Direct Cross-Domain Database Writes

Prohibited:

```text
Domain A
→ direct SQL
→ Domain B tables
```

Required alternatives:

```text
Application Contract
Domain Service
Command
Event
Read Model
```

Example:

```text
Support
→ Payment Application Command
→ Payment changes payment state
```

not:

```text
Support
→ UPDATE payments
```

---

# 6. No Duplicate Source of Truth

Do not create duplicate domain models merely because another module needs similar information.

Examples:

```text
Research
≠ second Analyzer source model

Product
≠ second Entitlement product model

Workspace
≠ second Tenant identity

Storage
≠ business-project file table containing binaries
```

References should be used whenever possible:

```text
source_id
content_slot_id
product_id
entitlement_id
storage_object_id
```

---

# 7. Content Slot Is the Production Context Anchor

When content belongs to the production pipeline:

```text
content_slot_id
```

is the stable context anchor.

The intended chain is:

```text
Planner
→ Analyzer
→ Blueprint
→ Asset
→ Editor
→ Export
→ Analytics
```

Do not create an unrelated `project_id` in every module without an explicit architectural reason.

---

# 8. Research Canonical Model

Research must use the canonical PRD-aligned model from Core Contract #10:

```text
Research Workspace
→ Competitor
→ Competitor Snapshot
→ Content Observation
→ Topic
→ Hook Pattern
→ CTA Pattern
→ Trend Signal
→ Keyword / Cluster
→ Audience Signal
→ Research Evidence
→ Research Insight
→ Opportunity
→ Research Digest
```

Execution/integration concepts:

```text
Research Run
Provider Result
Correlation
```

must support this model, not replace it.

---

# 9. Research Evidence Integrity

Research results must preserve:

```text
source
provider
retrieval time
freshness
confidence
observed vs estimated
evidence reference
```

When data is insufficient:

```text
INSUFFICIENT_DATA
```

must be represented honestly.

Never invent:

```text
metrics
counts
quotes
sources
coverage
certainty
```

---

# 10. Analyzer Must Reuse Research Truth

Analyzer must use the canonical Research source/evidence model.

Analyzer must not silently create:

```text
second Source
second Evidence
second Research Workspace
```

Analyzer may enrich interpretation:

```text
claim
angle
readiness
risk
```

but source truth remains traceable to Research.

---

# 11. Blueprint Is a Production Layer

Blueprint is not a second Analyzer.

Analyzer owns:

```text
source understanding
evidence
claims
opportunity
angle
readiness
```

Blueprint owns:

```text
narrative
script
visual blueprint
structured prompts
asset requirements
editor mapping
production QA
```

---

# 12. Blueprint Variant Rule

Production is represented as:

```text
Angle × Content Type
```

Example:

```text
Content Slot
├── Angle A × Carousel
├── Angle A × Video
└── Angle B × Video
```

A Variant may own:

```text
script
slide/shot
prompt
asset requirements
editor mapping
QA
approval
version
```

Regenerating one Variant must not silently modify another Variant.

---

# 13. Explicit Member Decisions Have Highest Priority

When automation conflicts with a member decision:

```text
Explicit Member Decision
>
Approved Decision
>
Validated Domain Input
>
AI Recommendation
>
Default Heuristic
```

AI must never silently override:

```text
locked slot
selected angle
approved blueprint
manual pricing
manual payment approval
manual entitlement action
```

---

# 14. Hard Constraints vs Soft Constraints

Planning logic must distinguish:

```text
Hard Constraint
Soft Constraint
```

Hard constraints:

```text
cannot be violated
```

Soft constraints:

```text
may be optimized/traded off
```

Example:

```text
Locked Slot
→ hard

Preferred posting time
→ soft
```

Never solve a soft optimization problem by violating a hard constraint.

---

# 15. Product ≠ Entitlement

Product defines:

```text
what can be sold
```

Entitlement defines:

```text
what the user owns/can use
```

Do not merge them.

Example:

```text
Product:
Image Package 25

Entitlement:
member has 25 image generation units
```

---

# 16. No Top-Up / PAYG Wallet for Normal Member Billing

The finalized business model does not use a generic member:

```text
Wallet
Top Up
PAYG Balance
Deposit Balance
```

for ordinary feature usage.

The standard commercial flow is:

```text
Product
→ Order
→ Payment
→ Entitlement
→ Usage
```

Any deviation requires an explicit business decision.

---

# 17. Actual Consumption Happens at Usage Boundary

Preparation is not consumption.

Example:

```text
Blueprint
→ prepares Asset Requirement

Asset Generation
→ Entitlement Check
→ Consume
→ Provider Execution
```

Do not consume image/video entitlement merely because a prompt was created.

---

# 18. Order ≠ Payment

Order owns:

```text
what was purchased
```

Payment owns:

```text
how the order was settled
```

Never use a provider response as the canonical Order state.

---

# 19. Provider ≠ Business Domain

Provider infrastructure owns:

```text
provider selection
routing
credentials
health
adapter
```

Business domains own:

```text
payment state
research state
generation state
business result
```

Example:

```text
Xendit
→ Payment Adapter

Payment
→ owns Payment
```

---

# 20. Provider Adapter Rule

Provider-specific request/response structures must remain inside adapters.

Domain code should consume normalized contracts.

Avoid:

```text
Research Domain
→ raw YouTube API schema everywhere
```

Prefer:

```text
YouTube Adapter
→ Normalized Research Result
→ Research Domain
```

---

# 21. Provider Failure Must Be Isolated

A single provider failure should not automatically bring down unrelated domains.

Example:

```text
Image Provider Down
→ Image Generation unavailable

Workspace
Research
Planner
Support
```

should remain available where dependencies allow.

---

# 22. Configuration Is Control Plane, Not Business Logic

Configuration owns:

```text
value
type
scope
version
effective value
feature flag
```

Domain owns:

```text
business meaning
state
validation
enforcement
```

Do not build one universal:

```text
Configuration God Service
```

that performs all business logic.

---

# 23. Configuration vs Environment

Separate:

```text
Environment Configuration
```

from:

```text
Business Configuration
```

Example:

```text
DATABASE_URL
→ environment

support.auto_close_days
→ business configuration
```

---

# 24. Feature Flags Must Not Replace Authorization

A feature flag determines:

```text
is feature enabled?
```

Authorization determines:

```text
is this actor allowed?
```

Entitlement determines:

```text
does this user own the capability?
```

These are separate decisions.

---

# 25. Authorization Must Be Server-Side

Never rely on:

```text
hidden button
disabled UI
frontend route protection only
```

A protected operation must be rejected by the server when unauthorized.

---

# 26. Membership ≠ Role

Membership controls:

```text
commercial access / entitlement
```

Role controls:

```text
permission
```

Do not infer administrator privileges from:

```text
paid membership
```

or vice versa.

---

# 27. Tenant ≠ Workspace

Workspace:

```text
operational content context
```

Tenant:

```text
organizational / future white-label boundary
```

They must remain separate.

---

# 28. White-label Is Core-Ready, Not Full Product

Current implementation may prepare:

```text
tenant_id
tenant-aware authorization
tenant-scoped workspace
market
currency
domain reference
pricing policy reference
```

but must not prematurely build the full agency product.

---

# 29. Storage Abstraction Is Mandatory

Business code must use:

```text
Storage Contract
```

not direct R2 SDK calls scattered throughout the application.

Preferred:

```text
Storage Service
→ Storage Adapter
→ R2
```

This supports future:

```text
R2
→ self-hosted S3-compatible storage
```

---

# 30. File ≠ Business Entity

A file is:

```text
StorageObject
```

Business ownership is held by:

```text
Content Slot
Blueprint
Support Ticket
Export
```

through references.

Purge of a file must not silently delete the business record that referenced it.

---

# 31. Retention Must Be Explicit

Retention must be represented as policy/data.

Examples:

```text
Export / Editor Media
→ 48 hours

Support Attachment
→ 90 days after Ticket Closed
```

Do not scatter retention numbers across code.

---

# 32. Event ≠ Audit

Event:

```text
system communication
```

Audit:

```text
immutable historical record
```

An event may be retried.

An audit record should preserve the historical action.

---

# 33. Event ≠ Notification

Event:

```text
what happened
```

Notification:

```text
what the user should be told
```

Do not put notification delivery logic inside domain transactions.

---

# 34. Domain Events Must Be Versioned

Events should have:

```text
event_type
event_version
event_id
occurred_at
producer
aggregate_id
correlation_id
causation_id
payload
```

Consumers must not depend on unstable internal database structures.

---

# 35. Events Must Be Idempotent

Event handlers must safely tolerate duplicate delivery.

Example:

```text
PaymentPaid.v1
```

received twice:

```text
First
→ entitlement granted

Second
→ no duplicate grant
```

---

# 36. Transactional Outbox

Important domain transactions should use:

```text
Database Transaction
→ Domain State
→ Outbox
```

then:

```text
Outbox
→ Event Dispatcher
→ Consumers
```

This avoids losing events after a successful business transaction.

---

# 37. Cross-Domain Workflows Prefer Events

Example:

```text
Payment Paid
↓
PaymentPaid event
↓
Entitlement
↓
Referral
↓
Notification
```

Do not make Payment directly call every downstream system synchronously unless the contract explicitly requires it.

---

# 38. Long-running Work Goes to Workers

Workers should handle:

```text
AI Generation
Video Processing
Research Sync
Large Analysis
Storage Purge
Notification Delivery
Analytics Processing
Digest Generation
Webhook Retry
```

Do not keep a browser/API request open unnecessarily for long-running work.

---

# 39. UI Is Never the Business Authority

UI may:

```text
display
request
validate for convenience
```

but not decide final business truth.

Examples:

```text
UI says payment is Paid
≠ payment is actually Paid

UI says user has entitlement
≠ entitlement is actually granted
```

Server/domain is authoritative.

---

# 40. No Business Logic in UI

Avoid:

```text
commission calculation in React
entitlement rules in frontend
payment state transitions in frontend
retention calculation only in frontend
```

UI consumes server decisions.

---

# 41. API Is an Application Boundary

API handlers should:

```text
authenticate
authorize
validate input
call application use case
return result
```

They should not contain large business algorithms.

---

# 42. Domain Services Own Business Rules

Business rules belong in:

```text
Domain Service
Aggregate / Entity Logic
Application Use Case
```

not:

```text
Controller
Component
Repository
Provider Adapter
```

---

# 43. Repository Is Data Access, Not Business Logic

Repositories should handle:

```text
read
write
query
persistence
```

They should not decide:

```text
commission eligibility
entitlement eligibility
payment lifecycle
research scoring
```

---

# 44. No God Module

Avoid modules that become:

```text
UserService
→ Product
→ Payment
→ Research
→ Support
→ Analytics
→ Everything
```

Each domain should remain bounded.

---

# 45. No God Database Access Layer

Avoid one giant repository API that knows every table.

Prefer domain-specific persistence boundaries.

---

# 46. State Changes Must Be Explicit

Do not change lifecycle state through arbitrary field updates.

Bad:

```text
UPDATE status = 'PAID'
```

without domain validation.

Prefer:

```text
PaymentService.markPaid(...)
```

with:

```text
transition validation
audit/event
idempotency
```

---

# 47. Financial State Must Be Highly Controlled

The following must be protected:

```text
Order
Payment
Refund
Entitlement
Commission
Payout
```

Changes require:

```text
authorization
validation
audit
idempotency
```

---

# 48. Historical Financial Records

Do not silently mutate historical commercial meaning.

Examples:

```text
Price Snapshot
Payment Amount
Currency
Commission Basis
```

must remain traceable to what was actually transacted.

---

# 49. Referral Calculation Rule

Referral must use the finalized business rules.

Examples:

```text
Attribution Window:
90 days default

Commission:
10% of actual amount paid after discount

Minimum Withdrawal:
20,000 IDR
```

The implementation must not invent alternate commission bases.

---

# 50. Active Downline Rule

Only valid active downline counts.

Excluded:

```text
Trial
Free
Suspended
Failed Payment
```

Included:

```text
Paid Subscription
Cancelled but still active
```

Milestone calculation must use current active downline, not lifetime recruited users.

---

# 51. Refund / Commission Boundary

If a downline purchase is refunded while commission is Pending:

```text
Commission
→ cancelled
```

If a commercial rule requires clawback:

```text
Future Commission
```

may absorb it according to the finalized business decision.

---

# 52. Support Is a Domain, Not a Generic Admin Tool

Support owns:

```text
Ticket
Reply
Priority
Assignment
SLA
Resolution
Auto-close
Reopen
```

Payment verification uses Support as workflow, but Payment remains the owner of payment state.

---

# 53. Support Attachment Rule

Support attachments must respect:

```text
2 MB
PDF
PNG
JPG
```

Retention:

```text
90 days after Closed
```

Storage owns the binary lifecycle.

---

# 54. Closed Ticket Reopen Rule

Closed ticket reopening is:

```text
Admin only
```

Do not implement member-side direct reopen if the finalized business rule says Admin controls it.

---

# 55. Single Login Rule

The system must enforce:

```text
one user
→ one active session
```

New login:

```text
revoke previous active session
→ establish new session
```

Do not implement this only as a frontend warning.

---

# 56. Security Protection Is Not DRM

Anti-screenshot/browser controls are deterrence.

Do not claim:

```text
absolute screen capture prevention
```

Implementation may include:

```text
protected content
Print Screen deterrence
F12/right-click restriction
focus-loss Auto-Blur
```

but normal product functionality must remain usable.

---

# 57. Data Privacy

Never log or expose:

```text
passwords
auth tokens
API keys
payment secrets
private provider credentials
signed storage URLs beyond intended scope
```

Logs and support diagnostics must redact secrets.

---

# 58. Least Privilege

Every component should receive only the permissions it needs.

Examples:

```text
Research Worker
→ research provider credentials

Storage Worker
→ storage purge access

Payment Worker
→ payment webhook/process access
```

Do not use one universal credential for every component.

---

# 59. Environment Isolation

Never mix:

```text
Development
Staging
Production
```

credentials or critical data.

Production payment/provider credentials must never be used casually in local development.

---

# 60. Migration Safety

Database changes must use:

```text
versioned migrations
```

Avoid manual production schema editing.

For risky changes prefer:

```text
Expand
→ Migrate
→ Verify
→ Switch
→ Contract
```

---

# 61. Deployability

Every slice must remain deployable through the approved environment strategy:

```text
Vercel
+
Supabase
+
R2
```

without introducing hidden vendor dependencies into the domain layer.

---

# 62. VPS Migration Rule

Future migration to VPS should mostly change:

```text
hosting
runtime
database deployment
secrets
DNS
TLS
workers
monitoring
```

not:

```text
domain logic
business rules
core contracts
API semantics
data ownership
```

---

# 63. Testing Constitution

No slice is complete without relevant automated tests.

Minimum expectation:

```text
Domain Rules
API
Persistence
Authorization
Failure Path
Concurrency / Idempotency where applicable
```

Critical financial and security paths require stronger coverage.

---

# 64. End-to-End Testing

End-to-end tests must validate real workflows.

Examples:

```text
Login
→ Workspace
→ Research
→ Planner
→ Analyzer
→ Blueprint
→ Asset
→ Editor
→ Export
```

and:

```text
Product
→ Order
→ Payment
→ Entitlement
```

---

# 65. Regression Testing

Every completed slice becomes part of the regression suite.

Before locking the next slice:

```text
Current Slice Tests
+
Previous Slice Tests
```

must pass.

---

# 66. Acceptance Gate Is Mandatory

Every slice uses:

```text
Gate A Functional
Gate B Failure
Gate C Persistence
Gate D Observability
```

A slice is not complete because:

```text
developer says done
```

It is complete when:

```text
acceptance gate passes
```

---

# 67. No "Temporary" Production Shortcuts

Prohibited as permanent implementation:

```text
direct SQL from UI
hard-coded admin user bypass
manual entitlement writes
fake payment success
hard-coded production secrets
provider calls directly from frontend
temporary duplicate tables
```

If a shortcut is needed for a prototype:

```text
label it explicitly
track it
schedule replacement
```

before production lock.

---

# 68. AI Coding Assistant Rules

AI coding assistants must:

```text
read relevant source documents
respect current contracts
avoid invented business rules
avoid duplicate models
avoid bypassing authorization
avoid direct cross-domain writes
write tests with implementation
```

When uncertain:

```text
mark Open Decision
```

instead of guessing.

---

# 69. AI Must Not "Improve" Final Business Rules

An AI assistant must not replace a finalized business rule because another rule seems more conventional.

Examples:

```text
Do not convert 10% gross commission to net commission
Do not add wallet/PAYG
Do not change attribution window
Do not change minimum withdrawal
Do not allow member reopen if Admin-only was finalized
```

Existing decisions are authoritative.

---

# 70. AI Must Preserve Existing Architecture

When modifying an existing slice:

```text
first inspect current boundary
then change the smallest responsible layer
```

Do not solve a local issue by creating:

```text
new global service
new duplicate repository
new duplicate data model
```

unless architecture is formally updated.

---

# 71. Code Review Rule

Every implementation review should ask:

```text
1. Is ownership correct?
2. Is authorization correct?
3. Is the source of truth correct?
4. Does this create duplicate business state?
5. Are external providers isolated?
6. Are retries/idempotency safe?
7. Are events/audit correct?
8. Is persistence correct?
9. Is the UI merely presentation?
10. Is migration/VPS portability preserved?
```

---

# 72. Naming Rule

Names should follow domain terminology from the approved contracts.

Prefer:

```text
content_slot_id
blueprint_variant_id
opportunity_id
entitlement_id
storage_object_id
```

Avoid introducing competing synonyms such as:

```text
project_item_id
content_job_id
media_project_id
```

unless explicitly defined as different concepts.

---

# 73. API Naming Rule

API names should reflect domain actions.

Prefer:

```text
POST /blueprint-variants/:id/approve
POST /payments/:id/confirm
POST /content-slots/:id/lock
```

rather than generic:

```text
POST /update
POST /process
POST /do
```

---

# 74. Event Naming Rule

Use past-tense business facts.

Prefer:

```text
PaymentPaid
ContentSlotCreated
BlueprintVariantApproved
```

Avoid:

```text
DoPayment
RunStuff
UpdateData
```

---

# 75. Versioning Rule

Version:

```text
API Contracts
Events
Scoring Models
Blueprint Versions
Prompts where meaningful
Database Migrations
```

Historical results must retain the version that produced them.

---

# 76. Performance Rule

Do not prematurely optimize architecture.

First ensure:

```text
correctness
integrity
observability
testability
```

Then optimize:

```text
latency
throughput
cost
```

Performance optimizations must not break domain boundaries.

---

# 77. Caching Rule

Cache:

```text
derived / safe-to-cache data
```

not authoritative mutable business truth unless invalidation is explicit.

Never use stale cache as the final source for:

```text
payment state
entitlement validity
authorization
```

---

# 78. Retry Rule

Retry only operations that are safe to retry.

Before implementing a retry, define:

```text
Can this operation run twice safely?
```

If not:

```text
add idempotency
or
do not retry automatically
```

---

# 79. External API Rule

All external APIs must be isolated behind adapters.

Required:

```text
timeout
error mapping
retry policy
credential isolation
observability
rate-limit handling where applicable
```

---

# 80. No Provider Logic in UI

Never:

```text
frontend
→ provider API key
→ provider directly
```

Use:

```text
frontend
→ application
→ provider adapter
```

---

# 81. Storage Security Rule

Never expose:

```text
R2 access key
R2 secret
```

to the browser.

Use:

```text
authorized signed upload/download
```

---

# 82. Data Migration Rule

When a data model changes:

```text
Old Data
→ migration
→ validation
→ new model
```

Do not rely on one-time scripts that cannot be rerun or audited.

---

# 83. Documentation Sync Rule

When implementation changes an agreed contract:

```text
update code
AND
update relevant documentation
```

Documentation drift is considered a defect when it changes system meaning.

---

# 84. Contract Change Rule

If a Core Contract must change:

```text
Identify Impact
→ Update Contract
→ Update Architecture if needed
→ Update Slice Specification
→ Update Implementation
→ Update Tests
```

Never change implementation first and document it later without recording the change.

---

# 85. Vertical Slice Discipline

Development proceeds:

```text
P0.00
→ PASS
→ P0.01
→ PASS
→ P0.02
→ PASS
...
```

A slice may be developed in parallel by different people only when its dependencies and ownership boundaries are already stable.

---

# 86. UI Review Rule

When a page is built, test it immediately.

Required cycle:

```text
Build
→ Open UI
→ Use Real Flow
→ Test Empty
→ Test Loading
→ Test Error
→ Test Permission
→ Refresh
→ Logout/Login
→ Fix
→ Acceptance
```

Do not wait until the entire product is finished to discover UI/domain problems.

---

# 87. Production Readiness Rule

Before a feature is exposed to real users, verify:

```text
Functional
Security
Data Integrity
Observability
Failure Handling
Rollback
```

---

# 88. Release Rule

A release should identify:

```text
Version
Commit
Migration
Environment
Changes
Known Risks
Rollback Plan
```

---

# 89. Emergency Fix Rule

Emergency fixes may be expedited but must still:

```text
be scoped
be reviewed
be tested
be logged
be back-ported/merged correctly
```

Afterward:

```text
update documentation/contracts if semantics changed
```

---

# 90. Constitution Enforcement

Violations should be treated as engineering defects.

Examples:

```text
cross-domain direct DB write
unapproved business rule
secret in frontend
entitlement bypass
payment state mutation outside Payment
duplicate source of truth
silent AI override of user decision
missing acceptance gate
```

---

# 91. Engineering Priority Order

When trade-offs occur, prefer:

```text
1. Security
2. Data Integrity
3. Business Rule Correctness
4. Authorization
5. Reliability
6. Observability
7. Maintainability
8. Performance
9. Convenience
```

Do not sacrifice financial/data/security correctness for implementation speed.

---

# 92. Final Non-Negotiable Rules

```text
1. Final business decisions are authoritative.

2. One domain owns one source of truth.

3. No direct cross-domain DB writes.

4. No duplicate source-of-truth models.

5. Content Slot is the production context anchor.

6. Research remains the canonical research model.

7. Analyzer does not duplicate Research.

8. Blueprint is a production layer.

9. Blueprint Variant represents Angle × Content Type.

10. Product ≠ Entitlement.

11. Order ≠ Payment.

12. Provider ≠ Business Domain.

13. Storage ≠ Business Entity.

14. Event ≠ Audit.

15. Event ≠ Notification.

16. Authorization is server-side.

17. Membership ≠ Role.

18. Tenant ≠ Workspace.

19. Normal member billing does not use Top Up/PAYG Wallet.

20. Actual consumption occurs at the usage boundary.

21. Financial state changes are auditable and idempotent.

22. Provider credentials never enter frontend code.

23. Long-running operations use workers.

24. Important events use an outbox/idempotent processing strategy.

25. UI never becomes the business authority.

26. Configuration is not business logic.

27. Every vertical slice must pass its acceptance gate.

28. Every completed slice joins the regression suite.

29. AI assistants must not invent business rules.

30. AI assistants must not silently change architecture.

31. Vendor infrastructure is replaceable; domain logic is durable.

32. Documentation must remain synchronized with accepted system behavior.
```

---

# 93. Final Engineering Principle

The platform should be engineered as:

```text
DEFINED
   ↓
CONTRACTED
   ↓
ARCHITECTED
   ↓
IMPLEMENTED
   ↓
TESTED
   ↓
VERIFIED
   ↓
LOCKED
```

not:

```text
CODE FIRST
→ PATCH
→ GUESS
→ INTEGRATE LATER
```

The purpose of this Constitution is simple:

> **Every engineer or AI assistant should be able to modify the codebase without accidentally changing the platform's underlying business truth or architecture.**

---

# NON-PROGRAMMER ENGINEERING GOVERNANCE ADDENDUM

## Purpose

The Engineering Constitution now includes explicit rules for working with a non-programmer project owner/operator.

The platform must remain:

```text
engineering-safe
business-safe
operator-friendly
```

## 1. Operator Is Not the Debugger

The project owner/operator is not responsible for:

```text
debugging code
diagnosing stack traces
editing architecture
choosing database fixes
repairing migrations
changing provider integrations
```

The operator's role is:

```text
Run
Observe
Validate
Report
```

## 2. One-Step Operational Instructions

When giving instructions to a non-programmer operator:

```text
one action
→ expected result
→ next action
```

Avoid long batches of unrelated commands without verification between steps.

## 3. No Guessing

If a result differs from expected:

```text
STOP
→ capture result
→ report
```

The operator should not guess which fix to apply.

## 4. No Secret Sharing

Engineering and AI assistants must never instruct the operator to paste into chat:

```text
API Secret
Database Password
OAuth Secret
Webhook Secret
Private Key
R2 Secret
Production Credential
```

Instead provide:

```text
where to enter it
```

not:

```text
send it to me
```

## 5. No Unsafe Commands Without Explicit Warning

Any destructive command must be clearly classified:

```text
🔴 DESTRUCTIVE
```

Examples:

```text
DROP DATABASE
TRUNCATE
DELETE ALL
rm -rf
production reset
```

These should normally remain an engineering responsibility.

## 6. Production Protection

The project owner must not be casually instructed to alter Production.

Production actions require:

```text
Purpose
Risk
Backup requirement
Exact environment
Expected result
Rollback plan
```

## 7. AI / Developer Instruction Quality

Every operational instruction intended for the project owner should state:

```text
WHERE
WHAT
WHY
EXPECTED RESULT
WHAT TO DO IF FAILS
```

## 8. No Silent Technical Assumptions

Do not assume the operator knows:

```text
which terminal
which folder
which environment
which project
which account
which command
```

These must be explicitly named.

## 9. Error Reporting Over Error Repair

If the operator encounters an error:

```text
Collect:
command
environment
error
screenshot/log

Then:
report
```

Engineering decides the repair.

## 10. Acceptance Gate Responsibility

Operator verifies visible/operational behavior:

```text
page loads
button works
data appears
data persists
theme works
responsive works
expected workflow works
```

Engineering verifies:

```text
architecture
database
logs
events
security
performance
idempotency
code quality
```

Both are required.

## 11. UI Must Be Tested Immediately

When a vertical slice produces UI:

```text
Build
→ open UI
→ use it
→ report UX/functional issues
→ fix
→ re-test
```

Do not wait until the whole platform is complete.

## 12. Documentation Must Be Operator-Readable

When a slice requires operator action, its Implementation Specification must contain:

```text
Operator Verification
Operator Safety
Expected Result
Failure Handling
```

## 13. No Responsibility Transfer by Documentation

This is not acceptable:

```text
"Run this command and fix any error"
```

For a non-programmer operator, use:

```text
"Run this command.
If result is X → continue.
If result is Y/error → stop and send it."
```

## 14. Technical Decisions Remain Engineering-Owned

If the issue is:

```text
database indexing
ORM choice
queue technology
API architecture
worker design
provider adapter
```

engineering decides within the approved architecture.

The project owner does not need to become a technical architect to continue development.

## 15. "PASS" Does Not Equal "LOCKED"

A successful operator check is evidence.

The Engineering Acceptance Gate combines:

```text
Operator Validation
+
Engineering Validation
```

Only then:

```text
LOCK
```

## 16. Vertical Slice Governance

The standard sequence remains:

```text
Specification
→ Implementation
→ Operator Verification
→ Engineering Test
→ Fix
→ Acceptance Gate
→ LOCK
```

## 17. Final Non-Programmer Operational Rules

```text
1. One operational step at a time.
2. No secret sharing.
3. No guessing.
4. No random code changes by the operator.
5. Environment must be explicit.
6. Destructive commands must be flagged.
7. Production changes require controlled procedures.
8. Errors are reported, not guessed at.
9. UI is tested immediately.
10. Acceptance requires both operator and engineering verification.
```

## Final Principle

> **Engineering complexity belongs behind the system boundary; operational instructions presented to the project owner must remain clear, safe, and executable without requiring programming expertise.**

This addendum is now part of the Engineering Constitution.
