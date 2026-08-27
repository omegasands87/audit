# Final Vertical Slice Order — Platform Build Sequence

## Status

**Final Build Sequencing Draft — based on Core Architecture V1 and Core Contracts #1–#13**

This document defines the recommended **vertical build order** for the platform.

The principle is:

> **Build one vertical slice end-to-end, test it, verify it, then continue.**

A vertical slice is not:

```text
"finish one backend service"
```

It is:

```text
UI
↓
API / Application
↓
Domain Logic
↓
Persistence
↓
Events / Worker where needed
↓
Audit / Observability
↓
Automated + Manual Test
```

The order below follows dependency and risk, while preserving the platform's final architectural boundaries.

---

# 1. Build Strategy

The platform should be built in this order:

```text
FOUNDATION
    ↓
IDENTITY & ACCESS
    ↓
CONTROL PLANE
    ↓
COMMERCE
    ↓
SHARED INFRASTRUCTURE
    ↓
WORKSPACE / CONTENT CONTEXT
    ↓
RESEARCH
    ↓
PLANNER
    ↓
ANALYZER
    ↓
PRODUCTION BLUEPRINT
    ↓
ASSET PREPARATION
    ↓
EDITOR / EXPORT
    ↓
ANALYTICS
    ↓
SUPPORT
    ↓
REFERRAL
    ↓
ADMIN / ADVANCED OPERATIONS
    ↓
WHITE-LABEL FOUNDATION ACTIVATION
```

Important:

> **This is a dependency order, not a page-design order.**

Pages are built as part of the vertical slice that owns them.

---

# 2. Why Vertical Slice Instead of "Build All Core First"

The platform is too interconnected for:

```text
Build entire backend
→ build frontend later
```

That approach makes it difficult to verify:

- authentication;
- authorization;
- persistence;
- events;
- business rules;
- cross-domain integration;
- actual user workflow.

Instead:

```text
Slice 01
→ usable
→ test
→ verify

Slice 02
→ usable
→ test
→ verify
```

This allows architectural mistakes to be discovered while the system is still small.

---

# 3. Slice 00 — Architecture & Development Skeleton

## Goal

Create the minimal runnable platform structure without implementing business features.

### Build

```text
Web App
API App
Worker App
Database
Event / Job abstraction
Storage abstraction
Configuration loading
Environment handling
Logging
Error handling
Health checks
Testing framework
```

### Must already exist

```text
Domain module boundaries
Application layer
Repository/data boundary
API boundary
Event boundary
Worker boundary
```

### Do NOT build yet

```text
Business UI
Complex dashboards
Provider integrations
Full Godmode
```

### Acceptance

```text
Web runs
API runs
Worker runs
Database connects
Health endpoint works
Test suite runs
Basic logging works
```

### Exit Gate

> **The development environment can run the platform locally/staging without any business dependency being coupled directly to the UI.**

---

# 4. Slice 01 — Identity, Session & Single Login

## Goal

Produce the first real user journey:

```text
Register / Create Account
→ Login
→ Session
→ Logout
```

### Build

```text
User
Account Status
Authentication
Session
Login
Logout
Session Revocation
Single-login rule
```

### Critical rule

```text
User
→ max 1 Active Session
```

New device/login:

```text
Old Session
→ Revoked

New Session
→ Active
```

### UI

```text
Login
Logout
Current User
Basic Account
Session Error
```

### Test

```text
Login
Logout
Refresh
New login on second device
Old device denied
Suspended user denied
Blocked user denied
```

### Exit Gate

> **Identity and single-login are proven in a real browser flow.**

---

# 5. Slice 02 — Role, Permission & Authorization

## Goal

Make access control real before building sensitive domains.

### Build

```text
Role
Permission
Scope
Role Assignment
Authorization Check
```

### Required proof

Create:

```text
Admin
Support Staff
Custom Test Role
```

Test:

```text
Allowed action
Denied action
Own scope
Assigned scope
Workspace scope
All scope
```

### Godmode capability

Basic Role Builder:

```text
Create Role
Assign Permissions
Assign User
```

### Exit Gate

> **A user cannot perform a protected backend action without the required permission, even if the UI is manually bypassed.**

---

# 6. Slice 03 — Configuration Core + Basic Godmode Control Plane

## Goal

Make the platform configurable before business domains multiply.

### Build

```text
Configuration
Typed Values
Schema Validation
Scope
Version
Audit
Feature Flag
Effective Configuration
```

### First configuration examples

```text
security.single_login.enabled
localization.default_language
currency.default_by_market
support.auto_close_days
feature.test_feature.enabled
```

### Godmode UI

```text
Configuration List
Configuration Detail
Edit
Validate
History
```

### Important boundary

Godmode does not directly manipulate arbitrary database fields.

```text
Godmode
→ Configuration API
→ Configuration Domain
```

### Exit Gate

> **A configuration value can be changed in Godmode, validated, persisted, audited, and consumed by a domain without code changes.**

---

# 7. Slice 04 — Market, Localization & Currency Foundation

## Goal

Prepare the platform for Indonesia first while making global expansion possible.

### Build

```text
Market
Language
Currency
Market Defaults
Fallback Language
```

Minimum:

```text
ID
EN
IDR
USD
```

### Prove

```text
Indonesia
→ ID
→ IDR

Global/Test Market
→ EN
→ USD
```

### Important

Product prices remain independent:

```text
IDR Price
USD Price
```

No forced exchange conversion.

### Exit Gate

> **The same application can render different language/currency defaults from market configuration.**

---

# 8. Slice 05 — Product, Pricing & Entitlement Foundation

## Goal

Implement the final monetization model before payment.

### Build

```text
Product
Product Version
Price
Currency
Market Availability
Entitlement Definition
Entitlement Grant
Entitlement Ledger
Consumption
Lock / Unlock
```

### Products to prove

Create test products:

```text
Membership
Image Package
Video Package
Add-on
Bundle
```

### Critical rules

```text
Product ≠ Entitlement
Product capability must already exist in Core
```

### Entitlement proof

```text
Grant
→ Consume
→ Remaining
→ Lock
→ Unlock
→ Audit
```

### Exit Gate

> **A test product can produce a correct entitlement lifecycle without any payment provider yet.**

---

# 9. Slice 06 — Order & Payment Core

## Goal

Connect commercial purchase to actual payment confirmation.

### Build

```text
Order
Order Item
Price Snapshot
Payment
Payment Attempt
Payment Status
Refund Request
```

### Provider adapters

First create:

```text
Mock Payment Provider
```

before real gateways.

Then validate:

```text
Xendit Adapter
Duitku Adapter
NOWPayments Adapter
Manual Transfer Adapter
```

as separate integration work.

### Critical flow

```text
Product
→ Order
→ Payment
→ PAID
→ Entitlement Grant
→ Fulfilled
```

### Critical tests

```text
Duplicate payment
Duplicate webhook
Failed payment
Retry payment
Amount mismatch
Currency mismatch
Fulfillment retry
```

### Exit Gate

> **A paid order can grant entitlement exactly once, even when payment confirmation is delivered multiple times.**

---

# 10. Slice 07 — Manual Transfer + Support Payment Verification

## Goal

Implement the special manual payment flow already finalized.

### Build

```text
Manual Payment
Support Ticket Reference
Payment Proof
Admin Approval
Payment Confirmation
Entitlement Grant
```

### Final flow

```text
Order
→ Manual Transfer
→ Payment Pending
→ Support Ticket
→ Proof Upload
→ Admin Approves
→ Payment Paid
→ Entitlement Granted
```

### Policy

```text
Attachment:
2 MB
PDF / PNG / JPG
```

### Exit Gate

> **Admin approving the correct manual-transfer ticket causes the payment to become Paid and entitlement to be granted automatically.**

---

# 11. Slice 08 — Storage Core & File Lifecycle

## Goal

Build durable binary storage before Research/Blueprint/Support depend on it.

### Build

```text
StorageObject
Upload Session
Commit
Download Authorization
Signed URL
Retention
Purge
Storage Provider Adapter
```

### First retention policies

```text
Export / Editor Media
→ 48 hours

Support Attachment
→ 90 days after Closed
```

### Prove

```text
Upload
→ Available
→ Download
→ Retention
→ Purge
```

### Critical test

```text
Purge file
≠
delete Content Slot
```

### Exit Gate

> **Binary lifecycle works independently from business/project lifecycle.**

---

# 12. Slice 09 — Event, Audit & Notification Infrastructure

## Goal

Make the platform observable and reactive before engines become complex.

### Build

```text
Outbox
Event Bus
Event Handler
Retry
Dead Letter
Audit
Notification
In-app Notification
Email foundation
```

### First real events

```text
UserCreated
LoginSucceeded
RoleAssigned
ConfigurationChanged
OrderCreated
PaymentPaid
EntitlementGranted
StoragePurged
```

### Exit Gate

> **A domain action creates a durable event/audit trail and can trigger a notification without coupling notification code into the domain transaction.**

---

# 13. Slice 10 — Workspace & Content Slot

## Goal

Create the production context that will bind all Content Engine modules.

### Build

```text
Workspace
Workspace Membership
Content Plan
Content Slot
Content Slot Lifecycle
Revision
Lock
Archive
```

### Required identity

```text
content_slot_id
```

### Prove

```text
Create Content Slot
→ Save
→ Refresh
→ Logout
→ Login
→ Reopen
```

### Exit Gate

> **Content Slot state survives session changes and becomes the stable context anchor for downstream engines.**

---

# 14. Slice 11 — Research Workspace & Core Research Data

## Goal

Build the first major business engine.

Use the revised PRD-aligned Research model:

```text
Research Workspace
→ Competitor
→ Competitor Snapshot
→ Content Observation
→ Topic
→ Hook Pattern
→ CTA Pattern
→ Trend Signal
→ Keyword
→ Audience Signal
```

### First working Research features

P0-oriented:

```text
Research Overview
Competitor
Content Observation
Topic
Opportunity foundation
Own Content Intelligence foundation
```

### Provider

Use:

```text
Mock Research Provider
```

first.

Then connect real providers.

### Exit Gate

> **Research data can be collected, normalized, persisted, refreshed, and traced back to its source/provider.**

---

# 15. Slice 12 — Research Insight & Opportunity Engine

## Goal

Convert Research data into decision-ready outputs.

### Build

```text
Research Evidence
Research Insight
Opportunity
Confidence
Freshness
Opportunity Score
Recommendation Format
```

### Standard output

```text
What
Why Now
For Whom
Angle
Hook
Format
CTA
Evidence
Opportunity Score
Confidence
Action
```

### Actions

```text
Save
Send to Planner
Send to Analyzer
Dismiss
```

### Exit Gate

> **The platform can turn real research data into an evidence-backed opportunity that a member can act on.**

---

# 16. Slice 13 — Planner Core

## Goal

Turn opportunities/ideas into a usable content calendar.

### Build P0

```text
Content Plan
Planning Period
Active Days
Frequency
Content Type
Pillar Allocation
Integer Allocation
Candidate Slots
Calendar
Lock
Drag & Drop
Conflict Detection
Manual Edit
Autosave
```

### First generation flow

```text
Research Opportunity
→ Idea
→ Plan
→ Generate Calendar
→ Review
→ Lock
```

### Exit Gate

> **Member can generate, edit, lock, save, refresh, and reopen a real content calendar without losing manual decisions.**

---

# 17. Slice 14 — Planner Intelligence & Optimization

## Goal

Add the intelligence that makes Planner more than a calendar.

### Build

```text
Smart Time Recommendation
Anti-Repetition
Trend-Aware Scheduling
Production Workload
Calendar Health
Rebalance
Regenerate Selected
Regenerate From Date
Change Preview
Version History
Undo
```

### Critical rule

```text
Locked Slot
→ never overridden automatically
```

### Exit Gate

> **Planner can optimize unlocked slots while preserving explicit member decisions.**

---

# 18. Slice 15 — Analyzer Default Intelligence

## Goal

Implement the default Analyzer before any premium intelligence extensions.

### Build

```text
Analyzer Run
Source Ingestion
Source Classification
Quality Gate
Duplicate/Re-use
Structured Extraction
Fact/Opinion/Interpretation
Evidence
Angle Generation
Hook Validation
Virality Potential
Content Readiness
```

### Inputs

```text
URL
Media
Raw Concept
```

### Exit Gate

> **A selected Content Slot can receive a complete default analysis with evidence, angles, hooks, score, confidence, and readiness.**

---

# 19. Slice 16 — Analyzer Add-ons

## Goal

Implement official Analyzer add-ons one by one.

### Add-on A

```text
Deep Source Intelligence
```

### Add-on B

```text
Media Intelligence
```

### Add-on C

```text
Multi-AI
```

### Add-on D

```text
Cross-Source Analysis
```

### Rule

Each add-on must prove:

```text
Entitlement Check
→ Capability Available
→ Execution
→ Usage/Consumption
→ Result
```

### Exit Gate

> **Each add-on works independently and cannot corrupt or duplicate the canonical Research model.**

---

# 20. Slice 17 — Content Production Blueprint Core

## Goal

Convert Analyzer output into production instructions.

### Build

```text
Content Production Blueprint
Blueprint Variant
Angle × Content Type
Script
Slide
Shot
Structured Prompt
Asset Requirement
Editor Mapping
Evidence Binding
Production QA
```

### Critical model

```text
Content Slot
├── Angle A × Carousel
├── Angle A × Video
└── Angle B × Video
```

### Exit Gate

> **An approved Blueprint Variant fully describes what needs to be produced without requiring the Asset/Editor layers to rediscover creative intent.**

---

# 21. Slice 18 — Visual Continuity & Prompt Studio Add-ons

## Goal

Implement official Module 3 add-ons.

### Visual Continuity Engine

```text
Continuity Group
Locked Attributes
Inheritance
Consistency Validation
```

### Advanced Prompt Studio

```text
Advanced Prompt Editor
Provider Optimization
One-Click Auto-Fix
Prompt Validation
```

### Critical rule

```text
Canonical Structured Prompt
≠
Provider-specific Prompt
```

### Exit Gate

> **Add-ons modify production instructions without changing source truth, selected angle, or evidence meaning.**

---

# 22. Slice 19 — Asset Preparation Core

## Goal

Turn approved Blueprint Variants into actual production assets.

### Build

```text
Asset Project
Asset Requirement
Generation Job
User Upload
Preview
Final Generation
Asset Version
Asset Approval
Retry
Provider Routing
Entitlement Consumption
```

### Critical flow

```text
Approved Blueprint
→ Asset Requirement
→ Entitlement Check
→ Generation
→ Provider
→ Asset
→ Approval
```

### Preview vs Final

```text
Preview
→ not final billable generation unless policy says otherwise

Final
→ actual entitlement consumption
```

### Exit Gate

> **An approved Blueprint can produce real assets with correct entitlement consumption and provider traceability.**

---

# 23. Slice 20 — Editor Foundation

## Goal

Open a prepared Content Slot in an actual editor.

### Build

```text
Canvas / Timeline
Layers / Tracks
Asset Placement
Text
Image
Video
Audio
Basic Transform
Basic Trim
Blueprint Mapping
```

### Critical rule

Editor receives:

```text
Blueprint Mapping
+
Assets
```

rather than starting from zero.

### Exit Gate

> **A generated Blueprint plus assets opens as a usable initial editing project.**

---

# 24. Slice 21 — Export & Storage Lifecycle Integration

## Goal

Complete the production path.

### Build

```text
Export Job
Render
StorageObject
48h Retention
Download
Purge
```

### Final flow

```text
Planner
→ Analyzer
→ Blueprint
→ Asset
→ Editor
→ Export
→ Storage
→ Download
→ Purge
```

### Exit Gate

> **A member can take one Content Slot all the way from planning to a downloadable export.**

This is the first major **end-to-end Content Engine milestone**.

---

# 25. Slice 22 — Analytics Foundation

## Goal

Close the production feedback loop.

### Build

```text
Performance Record
Platform Metrics
Baseline
Median
Pattern Detection
Content Attribution
Content Slot Attribution
```

### Required relationship

```text
Published Content
→ Content Slot
→ Analytics
```

### Exit Gate

> **Performance data can be associated with the original production context.**

---

# 26. Slice 23 — Analytics Intelligence & Research Feedback

## Goal

Make Analytics useful for future planning/research.

### Build

```text
Performance Insights
Recommendations
Pattern Detection
Strategy Suggestions
Research Feedback
Planner Recommendations
```

### Critical rule

Analytics recommendation:

```text
Recommendation
→ Member Review
→ Apply
```

not:

```text
Analytics
→ silently mutate strategy
```

### Exit Gate

> **Analytics can generate a recommendation that Research/Planner can consume without silently changing existing plans.**

---

# 27. Slice 24 — Support Center

## Goal

Build the complete Support Ticket domain after the underlying Core and Storage are proven.

### Build

```text
Ticket
Priority
Assignment
Reply
Attachment
SLA
Resolution
Auto-close
Reopen
Notifications
```

### Final policies

```text
Attachment:
2 MB

Formats:
PDF / PNG / JPG

Auto-close:
7 days without user reply

Closed Ticket Reopen:
Admin only

SLA First Response:
Urgent 4h
High 8h
Normal 1 working day
Low 2 working days

Resolution:
Urgent 1 day
High 2 days
Normal 5 days
Low 10 days
```

### Exit Gate

> **Support works independently and can also participate in Manual Transfer payment verification.**

---

# 28. Slice 25 — Referral & Milestones

## Goal

Build referral only after Order/Payment and Support flows are proven.

### Build

```text
Attribution
Downline
Active Downline
Commission
Milestone
Commission Pending
Commission Available
Withdrawal
Payout
Clawback
```

### Final rules

```text
Attribution:
90 days default

Active Downline:
paid + valid
trial/free = excluded
suspended/failed payment = excluded
cancelled but still active = included

Commission:
10% of actual amount paid after discount

Pending:
Monthly → 1 month
Annual → 3 months

Minimum Withdrawal:
20,000 IDR
```

### Exit Gate

> **A real paid subscription can create referral commission with correct lifecycle and milestone calculation.**

---

# 29. Slice 26 — Finance & Operational Reconciliation

## Goal

Build operational visibility across:

```text
Order
Payment
Refund
Entitlement
Referral
Provider Cost
```

### Build

```text
Transaction Ledger
Payment Gateway Management
Refund Management
Commission Monitoring
Payout Monitoring
Provider Cost View
Reconciliation
```

### Exit Gate

> **Admin can trace a commercial transaction end-to-end from Order → Payment → Entitlement → Referral where applicable.**

---

# 30. Slice 27 — Full Admin Godmode

## Goal

Complete the administrative control plane after domain boundaries have been proven through real functionality.

### Build

```text
User Management
Role Builder
Permission Matrix
Configuration Center
Product Builder
Pricing
Entitlement Management
Provider Administration
Payment Gateway Configuration
Storage Management
Support Management
Referral Management
Analytics Administration
Feature Flags
Audit Viewer
Event / Job Monitoring
```

### Principle

Godmode calls domain contracts.

It does not become a second business backend.

### Exit Gate

> **Admin can manage all supported platform capabilities through the intended authorization boundaries without direct database manipulation.**

---

# 31. Slice 28 — Advanced Security & Content Protection

## Goal

Activate finalized security features after core content flow works.

### Build

```text
Content Protection
Anti-screenshot controls
Print Screen restriction
F12 / right-click restriction
Auto-Blur
Focus-loss detection
Protected content mode
Security indicators/events where required
```

### Final defaults

```text
Content Protection:
ON for protected content

Auto-Blur:
protected area only
```

The controls remain imperfect browser-level protections and should be treated as deterrence rather than absolute DRM.

### Exit Gate

> **Protection can be enabled/disabled through configuration and does not break legitimate core workflows.**

---

# 32. Slice 29 — White-label Foundation Verification

## Goal

Do NOT build the full White-label product.

Only verify that the core foundation works.

### Build/verify

```text
tenant_id
tenant-aware authorization
tenant-scoped workspace
tenant-aware product references
market
currency
domain reference
pricing policy reference
API boundary
```

### Do NOT build

```text
Full Agency Website
Full Agency Admin
Custom Domain UI
Complete Agency Onboarding
Agency Product UI
```

### Exit Gate

> **The Core can theoretically serve a future tenant/agency without redesigning Identity, Authorization, Product, Order, Storage, or Content Context.**

---

# 33. Slice 30 — End-to-End Platform Validation

This is the final integration milestone.

Test complete journeys.

## Journey A — Member Content

```text
Register
→ Login
→ Planner
→ Research
→ Opportunity
→ Analyzer
→ Blueprint
→ Asset
→ Editor
→ Export
→ Download
→ Analytics
```

## Journey B — Paid Package

```text
Product
→ Order
→ Payment
→ Entitlement
→ Generation
→ Consumption
```

## Journey C — Manual Transfer

```text
Order
→ Manual Transfer
→ Support Ticket
→ Proof
→ Admin Approval
→ Payment Paid
→ Entitlement
```

## Journey D — Referral

```text
Downline Purchase
→ Paid
→ Commission Pending
→ Commission Available
→ Milestone
→ Withdrawal
```

## Journey E — Storage Lifecycle

```text
Export
→ 48h
→ Purge
```

## Journey F — Support Attachment

```text
Ticket
→ Attachment
→ Closed
→ 90 days
→ Purge
```

---

# 34. Recommended Slice Dependencies

The compact dependency order is:

```text
00 Architecture Skeleton
        ↓
01 Identity
        ↓
02 Authorization
        ↓
03 Configuration
        ↓
04 Market / i18n / Currency
        ↓
05 Product / Entitlement
        ↓
06 Order / Payment
        ↓
07 Manual Transfer
        ↓
08 Storage
        ↓
09 Events / Audit / Notifications
        ↓
10 Workspace / Content Slot
        ↓
11 Research Data
        ↓
12 Research Insight / Opportunity
        ↓
13 Planner Core
        ↓
14 Planner Intelligence
        ↓
15 Analyzer Core
        ↓
16 Analyzer Add-ons
        ↓
17 Blueprint Core
        ↓
18 Blueprint Add-ons
        ↓
19 Asset Preparation
        ↓
20 Editor
        ↓
21 Export
        ↓
22 Analytics
        ↓
23 Analytics Feedback Loop
        ↓
24 Support
        ↓
25 Referral
        ↓
26 Finance / Reconciliation
        ↓
27 Admin Godmode
        ↓
28 Security Protection
        ↓
29 White-label Foundation
        ↓
30 End-to-End Validation
```

---

# 35. Why Payment Comes Before Research/Planner

Commercial infrastructure is foundational because:

```text
Product
→ Entitlement
→ Capability Access
```

is needed by:

```text
Analyzer Add-ons
Blueprint Add-ons
Asset Generation
Storage Packages
```

The platform should therefore validate monetization/entitlement early rather than building all feature engines first and retrofitting billing later.

---

# 36. Why Storage Comes Before Content Engine

Research and Content Engine may produce:

```text
documents
media
uploads
exports
attachments
raw provider objects
```

Storage lifecycle therefore needs to be proven early.

Otherwise later modules may implement temporary/retention logic independently.

---

# 37. Why Events Come Before the Engines

The platform contains many asynchronous workflows:

```text
Payment
Research
AI Generation
Storage Purge
Notifications
Analytics
Referral
```

If each engine builds its own event mechanism, the architecture becomes fragmented.

Therefore Event/Audit/Notification infrastructure should be proven before the major domain engines.

---

# 38. Why Research Comes Before Planner

The PRD positions Research as an upstream decision layer:

```text
Research
→ Opportunity
→ Planner
```

Planner can still accept manual ideas, but the full intended workflow depends on Research.

---

# 39. Why Planner Comes Before Analyzer

Analyzer is a production intelligence step connected to a `content_slot_id`.

Planner creates the planning context:

```text
Content Slot
```

that Analyzer consumes.

Analyzer may also run outside Planner in some workflows, but the primary Content Engine path is:

```text
Planner
→ Analyzer
```

---

# 40. Why Analyzer Comes Before Blueprint

Blueprint needs:

```text
validated angle
evidence
opportunity
audience
readiness
```

from Analyzer.

Therefore:

```text
Blueprint
```

should never invent its own research/evidence model.

---

# 41. Why Blueprint Comes Before Asset Preparation

Blueprint defines:

```text
what needs to be produced
```

Asset Preparation defines:

```text
how it is generated
```

This boundary protects:

```text
entitlement
provider usage
generation cost
```

from accidental consumption during planning.

---

# 42. Why Asset Comes Before Editor

Editor should not be responsible for discovering/generating missing assets.

Flow:

```text
Blueprint
→ Asset Requirements
→ Assets
→ Editor
```

This makes Editor simpler and testable.

---

# 43. Why Analytics Comes After Export

Analytics needs actual produced/published output.

Therefore:

```text
Production
→ Export / Publish
→ Performance Data
→ Analytics
```

rather than attempting to optimize content before enough actual performance exists.

---

# 44. Why Referral Comes After Payment

Referral depends on:

```text
actual paid transaction
```

and finalized rules around:

```text
Pending
Available
Refund
Clawback
Milestone
```

Therefore Order/Payment must be stable first.

---

# 45. Slice Gate Standard

Every vertical slice must pass four gates.

## Gate A — Functional

```text
Happy Path works
```

## Gate B — Failure

```text
Invalid Input
Permission Denied
Timeout
Provider Failure
Conflict
Retry
```

## Gate C — Persistence

```text
Refresh
Logout
Login
Reload
```

state remains correct.

## Gate D — Observability

```text
Audit
Event
Logs
Correlation ID
Error trace
```

exists where required.

A slice is not complete until all four gates pass.

---

# 46. Definition of "Done"

A vertical slice is considered complete only when:

```text
UI works
+
API works
+
Domain works
+
Persistence works
+
Permission works
+
Errors handled
+
Tests pass
+
Events/audit integrated
+
Observability works
+
Manual acceptance passed
```

---

# 47. Recommended Development Pattern Inside Each Slice

Use:

```text
1. Contract
2. Data model
3. Domain service
4. API
5. UI
6. Worker if required
7. Events
8. Audit
9. Tests
10. Manual acceptance
```

Do not build:

```text
all UI first
all backend first
all integrations first
```

---

# 48. First Real Milestone

The first meaningful milestone is not:

> "Login page completed."

It is:

```text
Identity
+
Authorization
+
Configuration
+
Persistent Workspace
+
Content Slot
```

working together.

Then the system has a real authenticated, permission-aware, persistent application foundation.

---

# 49. First Major Product Milestone

The first major product milestone is:

```text
Research
→ Opportunity
→ Planner
→ Analyzer
→ Blueprint
→ Asset
→ Editor
→ Export
```

with one Content Slot flowing through the entire Content Engine.

This should be treated as the primary flagship vertical workflow.

---

# 50. Second Major Product Milestone

The next major milestone is monetization:

```text
Product
→ Order
→ Payment
→ Entitlement
→ Feature Usage
```

including:

```text
Manual Transfer
Add-ons
Usage Consumption
Refund
```

---

# 51. Third Major Product Milestone

Then close the learning loop:

```text
Published Content
→ Analytics
→ Insight
→ Research
→ Planner
```

This turns the platform from a static content tool into a continuously learning system.

---

# 52. Architecture-to-Implementation Rule

No new vertical slice should introduce an architecture exception without first updating:

```text
Core Contract
or
Core Architecture
```

The system should not accumulate:

```text
temporary direct DB access
temporary cross-domain writes
temporary duplicated models
temporary provider calls from UI
```

and then leave them as permanent architecture.

---

# 53. Final Vertical Slice Order

The final recommended order is:

```text
00  Architecture / Dev Skeleton
01  Identity / Session
02  Role / Permission
03  Configuration / Godmode Control Plane
04  Market / Localization / Currency
05  Product / Pricing / Entitlement
06  Order / Payment
07  Manual Transfer / Payment Verification
08  Storage / File Lifecycle
09  Events / Audit / Notifications
10  Workspace / Content Slot
11  Research Data Foundation
12  Research Insight / Opportunity
13  Planner Core
14  Planner Intelligence
15  Analyzer Default
16  Analyzer Add-ons
17  Blueprint / Variant Core
18  Blueprint Add-ons
19  Asset Preparation / Generation
20  Editor
21  Export / Storage Integration
22  Analytics Foundation
23  Analytics Feedback Loop
24  Support
25  Referral / Milestones
26  Finance / Reconciliation
27  Admin Godmode Complete
28  Security / Content Protection
29  White-label Core Verification
30  End-to-End Platform Validation
```

---

# 54. Final Principle

The platform should be developed according to:

```text
DEFINE
  ↓
CONTRACT
  ↓
ARCHITECT
  ↓
VERTICAL SLICE
  ↓
BUILD
  ↓
TEST
  ↓
VERIFY
  ↓
LOCK
  ↓
NEXT SLICE
```

Never:

```text
Build everything
→ integrate later
→ discover architecture problems at the end
```

The objective is not simply to finish pages.

The objective is to progressively produce:

> **a working platform whose architecture, business rules, data ownership, and user workflow remain consistent from the first vertical slice to the final system.**
