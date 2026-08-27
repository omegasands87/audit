# Implementation Roadmap — P0 / P1 / P2
## Dependency Matrix + Acceptance Gates

## Status

**Implementation Roadmap based on Final Vertical Slice Order + Core Architecture V1 + Revised Core Contracts #1–#13**

This document translates the Final Vertical Slice Order into three implementation phases:

```text
P0 = Foundation + First End-to-End Product
P1 = Product Expansion + Monetization + Operations
P2 = Advanced Platform + Protection + White-label Readiness
```

The phases are **implementation sequencing**, not a replacement for the PRD's feature priority labels.

The governing development rule remains:

> **Build → Test → Verify → Lock → Continue**

A feature is not considered complete merely because its UI exists.

---

# 1. Phase Philosophy

## P0 — Make the Platform Real

P0 must prove that the architecture works in production-like conditions.

P0 should establish:

```text
Identity
Authorization
Configuration
Commerce
Storage
Events
Workspace
Research
Planner
Analyzer
Blueprint
Asset
Editor
Export
```

The desired P0 outcome is:

> **One authenticated member can take a Content Slot from planning through a real downloadable export, with entitlement, persistence, authorization, audit, event, and storage lifecycle working correctly.**

---

# 2. P1 — Make the Platform Commercially Complete

P1 adds:

```text
Advanced Intelligence
Support
Referral
Analytics Feedback
Finance
Full Godmode
```

The desired P1 outcome is:

> **The platform can operate as a real commercial SaaS product, not only as a Content Engine prototype.**

---

# 3. P2 — Make the Platform Expansion-Ready

P2 focuses on:

```text
Advanced Security
White-label Foundation Verification
Advanced Operations
Future-facing infrastructure readiness
```

The desired P2 outcome is:

> **The core can support future global/agency expansion without redesigning foundational domains.**

---

# 4. P0 — Foundation + First End-to-End Product

## P0 Slice Map

```text
P0.00 Architecture / Development Skeleton
P0.01 Identity / Session
P0.02 Role / Permission
P0.03 Configuration
P0.04 Market / Localization / Currency
P0.05 Product / Pricing / Entitlement
P0.06 Order / Payment Core
P0.07 Manual Transfer
P0.08 Storage
P0.09 Events / Audit / Notification
P0.10 Workspace / Content Slot
P0.11 Research Data Foundation
P0.12 Research Insight / Opportunity
P0.13 Planner Core
P0.14 Analyzer Default
P0.15 Blueprint / Variant Core
P0.16 Asset Preparation Core
P0.17 Editor Foundation
P0.18 Export / Storage Integration
```

---

# 5. P0.00 — Architecture / Development Skeleton

### Objective

Create the runnable technical foundation.

### Deliverables

```text
Web Application
API Application
Worker Application
Database
Module Structure
Application Layer
Repository Boundary
Event Abstraction
Job Abstraction
Storage Abstraction
Environment Configuration
Logging
Error Handling
Health Checks
Testing Harness
```

### Acceptance Gate

All must pass:

```text
[PASS] Web boots
[PASS] API boots
[PASS] Worker boots
[PASS] Database connection works
[PASS] Health endpoint works
[PASS] Test runner works
[PASS] Basic structured logging works
[PASS] Modules cannot directly access each other's repositories
```

### Hard Gate

> No business feature proceeds if module boundaries are already being bypassed.

---

# 6. P0.01 — Identity / Session

### Deliverables

```text
User
Account Status
Authentication
Session
Login
Logout
Single Login
Session Revocation
```

### Acceptance Gate

```text
[PASS] Login
[PASS] Logout
[PASS] Refresh
[PASS] Session persistence
[PASS] Second device login
[PASS] Old session revoked
[PASS] Suspended account blocked
[PASS] Blocked account blocked
```

### Security Gate

> Single-login must be enforced server-side, not only by the browser.

---

# 7. P0.02 — Role / Permission

### Deliverables

```text
Role
Permission
Scope
Role Assignment
Authorization Decision
```

### Acceptance Gate

```text
[PASS] Admin permission
[PASS] Support role
[PASS] Custom role
[PASS] Permission denied at API
[PASS] Scope enforcement
[PASS] Direct API bypass blocked
```

### Hard Gate

> UI hiding is never considered authorization.

---

# 8. P0.03 — Configuration

### Deliverables

```text
Typed Configuration
Schema
Scope
Version
Effective Value
Feature Flag
Configuration Audit
```

### Acceptance Gate

```text
[PASS] Admin edits config
[PASS] Validation rejects invalid value
[PASS] Value persists
[PASS] Domain consumes effective value
[PASS] Change audited
[PASS] Rollback/version history available
```

### Architecture Gate

> Configuration stores policy/value; it does not become domain business logic.

---

# 9. P0.04 — Market / Localization / Currency

### Deliverables

```text
Market
Language
Currency
Fallback
Market Defaults
```

Minimum:

```text
ID
EN
IDR
USD
```

### Acceptance Gate

```text
[PASS] Indonesia → ID + IDR
[PASS] Global/Test → EN + USD
[PASS] Fallback language works
[PASS] Currency is explicit
[PASS] Product prices can differ by currency
```

---

# 10. P0.05 — Product / Pricing / Entitlement

### Deliverables

```text
Product
Product Version
Price
Market Availability
Entitlement
Grant
Consumption
Lock
Unlock
Adjustment
```

### Test Products

```text
Membership
Image Package
Video Package
Add-on
Bundle
```

### Acceptance Gate

```text
[PASS] Product created
[PASS] IDR price
[PASS] USD price
[PASS] Product version
[PASS] Entitlement granted
[PASS] Entitlement consumed
[PASS] Entitlement locked
[PASS] Entitlement unlocked
[PASS] Historical grant remains traceable
```

### Hard Gate

> Product and Entitlement remain separate sources of truth.

---

# 11. P0.06 — Order / Payment Core

### Deliverables

```text
Order
Order Item
Commercial Snapshot
Payment
Payment Attempt
Payment State
Mock Provider
Refund Request
Webhook Idempotency
```

### Acceptance Gate

```text
[PASS] Product → Order
[PASS] Order → Payment
[PASS] Payment Paid
[PASS] Paid → Entitlement
[PASS] Duplicate webhook safe
[PASS] Duplicate payment attempt safe
[PASS] Amount mismatch blocked
[PASS] Currency mismatch blocked
[PASS] Failed payment safe
[PASS] Fulfillment retry safe
```

### Hard Gate

> No paid order can grant entitlement more than once.

---

# 12. P0.07 — Manual Transfer

### Deliverables

```text
Manual Payment Method
Support Ticket Reference
Proof Attachment
Admin Approval
Payment Confirmation
Entitlement Grant
```

### Acceptance Gate

```text
[PASS] Payment Pending
[PASS] Ticket linked to Payment
[PASS] Proof uploaded
[PASS] Invalid proof stays pending
[PASS] Admin approves
[PASS] Payment becomes Paid
[PASS] Entitlement granted once
```

Attachment policy:

```text
2 MB
PDF / PNG / JPG
```

---

# 13. P0.08 — Storage

### Deliverables

```text
StorageObject
Upload Session
Commit
Download Authorization
Signed URL
Retention
Purge
Storage Adapter
```

### Acceptance Gate

```text
[PASS] Upload
[PASS] Commit
[PASS] Download
[PASS] Ownership enforcement
[PASS] Retention deadline
[PASS] Purge
[PASS] Purge retry
[PASS] Project survives file purge
```

Current policies:

```text
Export / Editor Media → 48 hours
Support Attachment → 90 days after Closed
```

---

# 14. P0.09 — Events / Audit / Notification

### Deliverables

```text
Outbox
Event Bus
Handler
Retry
DLQ
Audit
In-App Notification
Email Foundation
```

### Minimum Events

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

### Acceptance Gate

```text
[PASS] Event persisted
[PASS] Event delivered
[PASS] Handler idempotent
[PASS] Failed handler retries
[PASS] DLQ works
[PASS] Audit created
[PASS] Notification created
[PASS] Notification failure does not corrupt business state
```

---

# 15. P0.10 — Workspace / Content Slot

### Deliverables

```text
Workspace
Workspace Membership
Content Plan
Content Slot
Revision
Lock
Archive
```

### Acceptance Gate

```text
[PASS] Create Workspace
[PASS] Create Content Slot
[PASS] Permission checks
[PASS] Autosave
[PASS] Refresh recovery
[PASS] Logout/Login recovery
[PASS] Revision conflict detection
[PASS] Lock enforcement
```

### Hard Gate

> `content_slot_id` becomes the production context anchor.

---

# 16. P0.11 — Research Data Foundation

Use the revised Core Contract #10 canonical model:

```text
Research Workspace
Competitor
Competitor Snapshot
Content Observation
Topic
Hook Pattern
CTA Pattern
Trend Signal
Keyword
Keyword Cluster
Audience Signal
Research Source
Provider Result
Research Run
```

### Implementation Strategy

Start with:

```text
Mock Research Provider
```

before real provider integration.

### Acceptance Gate

```text
[PASS] Research Workspace
[PASS] Competitor
[PASS] Snapshot
[PASS] Content Observation
[PASS] Topic
[PASS] Source provenance
[PASS] Freshness
[PASS] Observed vs Estimated
[PASS] Research Run
[PASS] Provider trace
```

---

# 17. P0.12 — Research Insight / Opportunity

### Deliverables

```text
Research Evidence
Research Insight
Opportunity
Confidence
Freshness
Opportunity Score
Recommendation
```

Standard recommendation:

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

### Acceptance Gate

```text
[PASS] Evidence-backed insight
[PASS] Confidence shown
[PASS] Freshness shown
[PASS] No invented metrics
[PASS] Opportunity created
[PASS] Save
[PASS] Send to Planner
[PASS] Send to Analyzer
```

---

# 18. P0.13 — Planner Core

### Deliverables

```text
Content Plan
Planning Period
Active Days
Frequency
Content Type
Pillar Allocation
Integer Allocation
Idea Pool
Candidate Slots
Calendar
Lock
Manual Edit
Drag & Drop
Conflict Detection
Autosave
```

### Acceptance Gate

```text
[PASS] Plan created
[PASS] Correct slot count
[PASS] Integer allocation
[PASS] Calendar generated
[PASS] Manual move
[PASS] Hard constraint enforced
[PASS] Lock
[PASS] Refresh persistence
[PASS] No silent overwrite
```

---

# 19. P0.14 — Analyzer Default

### Deliverables

```text
Analyzer Run
URL Input
Media Input
Raw Concept Input
Source Classification
Quality Gate
Duplicate/Re-use
Structured Extraction
Fact/Opinion/Interpretation
Evidence
Angle
Hook
Virality Potential
Content Readiness
```

### Acceptance Gate

```text
[PASS] Input accepted
[PASS] Source classified
[PASS] Quality gate
[PASS] Structured extraction
[PASS] Evidence
[PASS] Multiple angles
[PASS] Hook validation
[PASS] Score
[PASS] Confidence
[PASS] Readiness
[PASS] Persistence
```

---

# 20. P0.15 — Blueprint / Variant Core

### Deliverables

```text
Content Production Blueprint
Blueprint Variant
Angle × Content Type
Script
Slide
Shot
Evidence Binding
Structured Prompt
Asset Requirement
Editor Mapping
Production QA
```

### Required example

```text
Content Slot
├── Angle A × Carousel
├── Angle A × Video
└── Angle B × Video
```

### Acceptance Gate

```text
[PASS] Analyzer → Blueprint
[PASS] Selected angle preserved
[PASS] Variant created
[PASS] Script
[PASS] Slide/Shot
[PASS] Prompt
[PASS] Asset Requirement
[PASS] Editor Mapping
[PASS] Evidence Binding
[PASS] Production QA
[PASS] Versioning
```

---

# 21. P0.16 — Asset Preparation Core

### Deliverables

```text
Asset Project
Asset Requirement
Generation Job
User Upload
Preview
Final Generation
Provider Routing
Retry
Asset Version
Asset Approval
Entitlement Consumption
```

### Acceptance Gate

```text
[PASS] Approved Blueprint accepted
[PASS] Asset requirements resolved
[PASS] Entitlement checked
[PASS] Provider called
[PASS] Asset created
[PASS] Provider trace stored
[PASS] Retry safe
[PASS] Duplicate generation protected
[PASS] Usage consumed correctly
```

---

# 22. P0.17 — Editor Foundation

### Deliverables

```text
Canvas
Timeline
Layers/Tracks
Text
Image
Video
Audio
Basic Transform
Basic Trim
Blueprint Mapping
Asset Placement
```

### Acceptance Gate

```text
[PASS] Blueprint opens editor
[PASS] Assets mapped
[PASS] Text editable
[PASS] Media editable
[PASS] Timeline editable
[PASS] Save state
[PASS] Reload state
```

---

# 23. P0.18 — Export / Storage Integration

### Deliverables

```text
Export Job
Render
StorageObject
Download
48h Retention
Purge
```

### Acceptance Gate

```text
[PASS] Editor → Export
[PASS] Export completed
[PASS] StorageObject created
[PASS] Download works
[PASS] Retention starts correctly
[PASS] Purge works
[PASS] Content Slot remains
```

### P0 Master Gate

P0 is complete only when this journey works:

```text
Register
→ Login
→ Permission
→ Workspace
→ Research
→ Opportunity
→ Planner
→ Analyzer
→ Blueprint
→ Asset
→ Editor
→ Export
→ Download
```

and the following are proven:

```text
Authorization
Persistence
Entitlement
Event
Audit
Provider Trace
Storage Lifecycle
```

---

# 24. P0 Exit Criteria

P0 is officially complete only if:

```text
1. Core architecture rules are respected.
2. No domain directly writes another domain's data.
3. Single-login works.
4. Authorization works server-side.
5. Configuration works.
6. IDR/USD + ID/EN foundation works.
7. Product → Entitlement works.
8. Order → Payment → Entitlement works.
9. Storage lifecycle works.
10. Event/Audit works.
11. Content Slot persists.
12. Research → Opportunity works.
13. Opportunity → Planner works.
14. Planner → Analyzer works.
15. Analyzer → Blueprint works.
16. Blueprint → Asset works.
17. Asset → Editor works.
18. Editor → Export works.
19. Export → Storage works.
20. End-to-end flagship Content Engine flow works.
```

---

# 25. P1 — Product Expansion + Commercial Operations

## P1 Slice Map

```text
P1.01 Planner Intelligence
P1.02 Analyzer Add-ons
P1.03 Blueprint Add-ons
P1.04 Analytics Foundation
P1.05 Analytics Intelligence / Feedback
P1.06 Support Center
P1.07 Referral & Milestones
P1.08 Finance / Reconciliation
P1.09 Full Admin Godmode
```

---

# 26. P1.01 — Planner Intelligence

### Deliverables

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

### Acceptance Gate

```text
[PASS] Locked slots never move
[PASS] Hard constraints never break
[PASS] Soft optimization improves calendar
[PASS] Change preview works
[PASS] Rebalance works
[PASS] Regenerate selected works
[PASS] Regenerate from date preserves earlier slots
[PASS] Undo creates valid new state
```

---

# 27. P1.02 — Analyzer Add-ons

Implement independently:

```text
Deep Source Intelligence
Media Intelligence
Multi-AI
Cross-Source Analysis
```

### Acceptance Gate

Each add-on:

```text
Authorization
→ Entitlement
→ Capability
→ Execution
→ Result
→ Usage
→ Audit/Event
```

Additional:

```text
[PASS] No duplicate Research source model
[PASS] AI disagreement preserved
[PASS] Confidence reflects disagreement
[PASS] Partial failure handled
```

---

# 28. P1.03 — Blueprint Add-ons

Implement:

```text
Visual Continuity Engine
Advanced Prompt Studio
Provider Optimization
One-Click Auto-Fix
```

### Acceptance Gate

```text
[PASS] Continuity group
[PASS] Locked attributes
[PASS] Inheritance
[PASS] Conflict detection
[PASS] Advanced prompt editing
[PASS] Provider optimization
[PASS] Auto-Fix
[PASS] Before/After preview
[PASS] Version created
[PASS] Evidence meaning preserved
```

---

# 29. P1.04 — Analytics Foundation

### Deliverables

```text
Performance Record
Platform Metrics
Baseline
Median
Content Attribution
Content Slot Attribution
Historical Performance
```

### Acceptance Gate

```text
[PASS] Published content mapped
[PASS] Content Slot attribution
[PASS] Metrics stored
[PASS] Median/baseline available
[PASS] Data provenance retained
```

---

# 30. P1.05 — Analytics Intelligence & Feedback

### Deliverables

```text
Performance Insights
Pattern Detection
Recommendations
Strategy Suggestions
Research Feedback
Planner Recommendations
```

### Critical Rule

```text
Recommendation
→ Member Review
→ Apply
```

never:

```text
Analytics
→ silent mutation
```

### Acceptance Gate

```text
[PASS] Recommendation explains why
[PASS] Confidence/source shown where applicable
[PASS] No automatic strategy mutation
[PASS] Research can consume signal
[PASS] Planner can consume recommendation
[PASS] Historical plans remain unchanged
```

---

# 31. P1.06 — Support Center

### Deliverables

```text
Ticket
Priority
Assignment
Reply
Attachment
SLA
Resolution
Auto-close
Admin Reopen
Notification
```

### Final policies

```text
Attachment:
2 MB

Formats:
PDF / PNG / JPG

Auto-close:
7 days without user reply

Reopen:
Admin only
```

### SLA

```text
First Response:
Urgent → 4h working
High → 8h working
Normal → 1 working day
Low → 2 working days

Resolution:
Urgent → 1 working day
High → 2 working days
Normal → 5 working days
Low → 10 working days
```

### Acceptance Gate

```text
[PASS] Ticket lifecycle
[PASS] SLA timer
[PASS] Attachment policy
[PASS] Auto-close
[PASS] User reply reopens activity
[PASS] Admin-only closed reopen
[PASS] Notifications
[PASS] Manual payment integration
```

---

# 32. P1.07 — Referral & Milestones

### Deliverables

```text
Attribution
Active Downline
Commission
Pending
Available
Milestone
Withdrawal
Payout
Clawback
```

### Final Business Rules

```text
Attribution:
90 days default

Commission:
10% of amount actually paid after discount

Pending:
Monthly = 1 month
Annual = 3 months

Minimum Withdrawal:
20,000 IDR
```

### Acceptance Gate

```text
[PASS] Paid subscription creates commission
[PASS] Trial excluded
[PASS] Free account excluded
[PASS] Suspended excluded
[PASS] Failed payment excluded
[PASS] Cancelled but active included
[PASS] Refund while Pending cancels commission
[PASS] Upline notification
[PASS] Milestone uses Active Downline
[PASS] Withdrawal threshold
```

---

# 33. P1.08 — Finance / Reconciliation

### Deliverables

```text
Transaction Ledger
Payment Gateway Management
Refund Management
Commission Monitoring
Payout Monitoring
Provider Cost
Reconciliation
```

### Acceptance Gate

```text
[PASS] Order traced
[PASS] Payment traced
[PASS] Refund traced
[PASS] Entitlement traced
[PASS] Referral traced
[PASS] Provider cost trace
[PASS] Manual transfer trace
```

---

# 34. P1.09 — Full Admin Godmode

### Deliverables

```text
User Management
Role Builder
Permission Matrix
Configuration
Product Builder
Pricing
Entitlements
Provider Administration
Payment Gateway
Storage
Support
Referral
Analytics
Feature Flags
Audit Viewer
Event/Job Monitoring
```

### Acceptance Gate

```text
[PASS] Admin UI uses domain APIs
[PASS] No direct database administration
[PASS] Permission checks enforced
[PASS] High-risk actions audited
[PASS] Configuration versioned
[PASS] Provider secrets hidden
[PASS] Product changes versioned
[PASS] Manual entitlement actions auditable
```

---

# 35. P1 Exit Criteria

P1 is complete when:

```text
1. Planner intelligence works.
2. Analyzer add-ons work.
3. Blueprint add-ons work.
4. Analytics closes the feedback loop.
5. Support operates independently.
6. Manual payment verification is production-ready.
7. Referral operates from real payments.
8. Finance can reconcile commercial flows.
9. Godmode can administer supported capabilities.
```

---

# 36. P2 — Advanced Platform + Expansion Readiness

## P2 Slice Map

```text
P2.01 Advanced Security / Content Protection
P2.02 White-label Core Verification
P2.03 Advanced Operational Hardening
P2.04 Full Platform Regression
```

---

# 37. P2.01 — Advanced Security / Content Protection

### Deliverables

```text
Protected Content
Anti-Screenshot Measures
Print Screen Restriction
F12 Restriction
Right-click Restriction
Focus-loss Detection
Auto-Blur
Protected Area Policy
Security Events
```

### Final Defaults

```text
Content Protection:
ON for protected content

Auto-Blur:
protected area only
```

### Acceptance Gate

```text
[PASS] Configuration toggle works
[PASS] Protected content activates
[PASS] Focus loss blurs protected area
[PASS] Recovery after refocus
[PASS] Normal content unaffected
[PASS] Mobile behavior tested
[PASS] Security events observable where required
```

The system must treat browser-level protections as deterrence, not absolute DRM.

---

# 38. P2.02 — White-label Core Verification

## Goal

Verify readiness only.

### Build/Verify

```text
tenant_id
Tenant-aware Authorization
Tenant-scoped Workspace
Tenant-aware Product
Market
Currency
Domain Reference
Pricing Policy Reference
API Boundary
```

### Do Not Build

```text
Full Agency Website
Full Agency Admin
Custom Domain UI
Agency Onboarding
Agency Product UI
```

### Acceptance Gate

```text
[PASS] Tenant isolation
[PASS] Tenant-aware authorization
[PASS] Product visibility scope
[PASS] Market scope
[PASS] Currency scope
[PASS] API boundary
[PASS] Existing master platform unaffected
```

---

# 39. P2.03 — Advanced Operational Hardening

### Deliverables

```text
Observability
Alerting
Retry Policies
Circuit Breaker
Dead Letter Operations
Provider Health Dashboard
Queue Monitoring
Storage Purge Monitoring
Audit Integrity
Reconciliation Monitoring
```

### Acceptance Gate

```text
[PASS] Provider outage detected
[PASS] Worker retry
[PASS] DLQ inspection
[PASS] Critical event replay
[PASS] Storage purge failures visible
[PASS] Payment webhook failures visible
[PASS] Correlation trace available
```

---

# 40. P2.04 — Full Platform Regression

Run complete test suites across:

```text
Identity
Authorization
Configuration
Commerce
Storage
Events
Workspace
Research
Planner
Analyzer
Blueprint
Asset
Editor
Export
Analytics
Support
Referral
Finance
Godmode
Security
Tenant Foundation
```

---

# 41. Dependency Matrix

Legend:

```text
A = mandatory dependency
B = beneficial / shared dependency
C = optional / future relation
```

| Slice | ID | Auth | Config | Commerce | Storage | Events | Workspace | Research | Planner | Analyzer | Blueprint | Asset | Editor | Analytics | Support | Referral | Finance | Tenant |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 00 Architecture | A | B | B | C | B | B | C | C | C | C | C | C | C | C | C | C | C | C |
| 01 Identity | A | - | B | B | C | B | C | C | C | C | C | C | C | C | C | C | C | C |
| 02 Authorization | B | A | B | A | A | A | A | A | A | A | A | A | A | A | A | A | A | A |
| 03 Configuration | B | B | - | A | A | A | A | A | A | A | A | A | A | A | A | A | A | A |
| 04 Market/i18n | B | B | A | A | C | B | B | B | B | B | B | B | B | B | B | B | B | A |
| 05 Product/Entitlement | B | B | A | - | B | A | C | C | A | A | A | A | C | C | C | A | A | C |
| 06 Order/Payment | B | B | A | A | C | A | C | C | C | C | C | A | C | C | B | A | A | C |
| 07 Manual Transfer | B | B | A | A | A | A | C | C | C | C | C | A | C | C | A | C | A | C |
| 08 Storage | B | B | A | C | - | A | A | A | B | A | A | A | A | C | A | C | C | A |
| 09 Events/Audit | B | A | A | A | A | - | A | A | A | A | A | A | A | A | A | A | A | A |
| 10 Workspace/Slot | B | A | A | C | A | A | - | A | A | A | A | A | A | A | C | C | C | A |
| 11 Research Data | B | A | A | C | A | A | A | - | A | A | C | C | C | A | C | C | C | A |
| 12 Research Opportunity | B | A | A | C | B | A | A | A | A | A | C | C | C | A | C | C | C | A |
| 13 Planner Core | B | A | A | C | C | A | A | A | - | A | A | C | C | B | C | C | C | A |
| 14 Planner Intelligence | B | A | A | C | C | A | A | A | A | A | A | C | C | A | C | C | C | A |
| 15 Analyzer | B | A | A | A | A | A | A | A | A | - | A | A | C | B | C | C | C | A |
| 16 Analyzer Add-ons | B | A | A | A | A | A | A | A | A | A | B | A | C | B | C | C | C | A |
| 17 Blueprint Core | B | A | A | A | A | A | A | A | A | A | - | A | A | C | C | C | C | A |
| 18 Blueprint Add-ons | B | A | A | A | A | A | A | C | A | A | A | A | A | C | C | C | C | A |
| 19 Asset Prep | B | A | A | A | A | A | A | C | C | A | A | - | A | C | C | C | C | A |
| 20 Editor | B | A | A | C | A | A | A | C | C | C | A | A | - | C | C | C | C | A |
| 21 Export | B | A | A | C | A | A | A | C | C | C | A | A | A | C | C | C | C | A |
| 22 Analytics | B | A | A | C | C | A | A | A | A | B | B | C | A | - | C | C | B | A |
| 23 Analytics Feedback | B | A | A | C | C | A | A | A | A | B | B | C | C | A | C | C | C | A |
| 24 Support | B | A | A | A | A | A | C | C | C | C | C | C | C | C | - | A | A | A |
| 25 Referral | B | A | A | A | C | A | C | C | C | C | C | C | C | C | A | - | A | A |
| 26 Finance | B | A | A | A | C | A | C | C | C | C | C | C | C | A | A | A | - | A |
| 27 Godmode | B | A | A | A | A | A | A | A | A | A | A | A | A | A | A | A | A | A |
| 28 Security | B | A | A | A | A | A | A | A | A | A | A | A | A | A | A | A | A | A |
| 29 White-label | B | A | A | A | A | A | A | C | C | C | C | C | C | C | C | C | C | - |
| 30 E2E Validation | B | A | A | A | A | A | A | A | A | A | A | A | A | A | A | A | A | A |

---

# 42. Critical Dependency Chains

## Identity Chain

```text
01 Identity
→ 02 Authorization
→ every protected domain
```

## Commerce Chain

```text
03 Configuration
→ 05 Product/Entitlement
→ 06 Order/Payment
→ 07 Manual Transfer
→ 25 Referral
→ 26 Finance
```

## Content Chain

```text
10 Workspace/Slot
→ 11 Research
→ 12 Opportunity
→ 13 Planner
→ 15 Analyzer
→ 17 Blueprint
→ 19 Asset
→ 20 Editor
→ 21 Export
→ 22 Analytics
```

## Intelligence Feedback

```text
22 Analytics
→ 23 Analytics Feedback
→ Research
→ Planner
→ Future Analyzer/Blueprint optimization
```

## Storage Chain

```text
08 Storage
→ 07 Support Attachment
→ 11 Research raw/reference data
→ 19 Asset
→ 21 Export
```

---

# 43. Acceptance Gate Framework

Every slice must satisfy:

## Gate A — Contract

```text
[ ] Core Contract respected
[ ] No scope violation
[ ] No duplicate source of truth
```

## Gate B — Functional

```text
[ ] Happy path works
[ ] Main UI works
[ ] API works
[ ] Persistence works
```

## Gate C — Failure

```text
[ ] Invalid input
[ ] Unauthorized action
[ ] Concurrency conflict
[ ] External failure
[ ] Retry
[ ] Recovery
```

## Gate D — Data Integrity

```text
[ ] Ownership correct
[ ] Historical data immutable where required
[ ] Idempotency
[ ] Audit/reference integrity
```

## Gate E — Observability

```text
[ ] Logs
[ ] Correlation ID
[ ] Events
[ ] Audit
[ ] Error trace
```

## Gate F — Regression

```text
[ ] Existing slices still pass
```

---

# 44. Phase Acceptance Gates

## P0 Gate

```text
P0 = PASS only if:

Authenticated member
→ creates Content Slot
→ researches
→ gets Opportunity
→ plans
→ analyzes
→ creates Blueprint
→ generates Asset
→ edits
→ exports
→ downloads
```

plus:

```text
Authorization
Entitlement
Events
Audit
Storage
```

all work.

---

## P1 Gate

```text
P1 = PASS only if:

P0 remains stable

+
Advanced intelligence works
+
Analytics feedback works
+
Support works
+
Referral works
+
Finance reconciliation works
+
Godmode controls all supported domains
```

---

## P2 Gate

```text
P2 = PASS only if:

P0 stable
+
P1 stable
+
Security protection verified
+
Tenant foundation verified
+
Operational hardening verified
+
Full regression passed
```

---

# 45. Release Readiness Matrix

| Area | P0 | P1 | P2 |
|---|---|---|---|
| Identity | Required | Harden | Hardened |
| Authorization | Required | Expand | Tenant-aware |
| Configuration | Required | Expand | Harden |
| Market/i18n/Currency | Required | Expand | Global-ready |
| Commerce | Core | Full | Harden |
| Storage | Core | Expand | Harden |
| Events/Audit | Core | Operational | Hardened |
| Workspace | Core | Expand | Tenant-aware |
| Research | Core | Intelligence | Advanced |
| Planner | Core | Intelligence | Advanced optimization |
| Analyzer | Default | Add-ons | Learning-ready |
| Blueprint | Core | Add-ons | Optimization-ready |
| Asset | Core | Expand | Advanced |
| Editor | Foundation | Expand | Harden |
| Export | Core | Expand | Harden |
| Analytics | Foundation | Feedback loop | Advanced |
| Support | - | Required | Harden |
| Referral | - | Required | Harden |
| Finance | - | Required | Harden |
| Godmode | Basic | Full | Operational |
| Security | Basic | Standard | Advanced |
| White-label | Core-ready | Verify | Foundation verified |

---

# 46. Rule for Moving a Slice Between Phases

A slice should move forward only when:

```text
Functional
+
Business Rule
+
Data Integrity
+
Security
+
Observability
+
Regression
```

are all acceptable.

Do not move a slice to "done" merely because:

```text
UI = complete
```

---

# 47. Dependency Exception Policy

Some slices may be developed partially in parallel when dependencies are already defined.

Example:

```text
Storage
+
Event infrastructure
+
Workspace
```

can be developed in parallel after their foundational prerequisites are stable.

However:

> **Parallel development must not bypass dependency contracts.**

---

# 48. Recommended Parallel Workstreams

After P0 foundation stabilizes, development can split:

## Workstream A

```text
Research
→ Opportunity
→ Planner
```

## Workstream B

```text
Analyzer
→ Blueprint
→ Asset
```

## Workstream C

```text
Commerce
→ Payment
→ Referral
→ Finance
```

## Workstream D

```text
Support
→ Notification
→ Manual Transfer
```

## Workstream E

```text
Godmode
→ Admin Operations
```

Shared contracts remain the synchronization mechanism.

---

# 49. No Cross-Workstream Shortcut

Example:

```text
Research Team
```

must not create:

```text
research_users
```

outside Identity.

Or:

```text
Asset Team
```

must not create:

```text
asset_wallet
```

outside Entitlement.

Or:

```text
Support Team
```

must not directly mark:

```text
payment = paid
```

outside Payment domain.

---

# 50. Implementation Freeze Points

At the end of each phase, freeze:

```text
Schema Contract
API Contract
Event Contract
Data Ownership
```

Only changes that are:

```text
required
reviewed
backward-compatible
```

should proceed into the next phase.

---

# 51. P0 Freeze Point

At P0 completion:

```text
Core Foundation
+
Content Engine MVP
```

becomes a stable baseline.

Any later feature should consume the stable P0 contracts rather than rewrite them casually.

---

# 52. P1 Freeze Point

At P1 completion:

```text
Commercial SaaS
+
Operations
```

becomes a stable baseline.

This is the point where:

```text
Billing
Support
Referral
Finance
Admin
Analytics
```

are all operationally integrated.

---

# 53. P2 Freeze Point

At P2 completion:

```text
Expansion-Ready Platform
```

becomes the baseline for future:

```text
Global Markets
Agency
White-label
Advanced AI
Advanced Security
```

---

# 54. Final Implementation Roadmap

```text
                 FINAL PLATFORM ROADMAP

P0
│
├── Foundation
├── Identity
├── Authorization
├── Configuration
├── Market / i18n / Currency
├── Commerce
├── Storage
├── Events / Audit
├── Workspace / Content Slot
├── Research
├── Opportunity
├── Planner Core
├── Analyzer Default
├── Blueprint Core
├── Asset Preparation
├── Editor
└── Export
        ↓
     P0 GATE
        ↓
P1
│
├── Planner Intelligence
├── Analyzer Add-ons
├── Blueprint Add-ons
├── Analytics
├── Feedback Loop
├── Support
├── Referral
├── Finance
└── Full Godmode
        ↓
     P1 GATE
        ↓
P2
│
├── Advanced Security
├── White-label Verification
├── Operational Hardening
└── Full Regression
        ↓
     P2 GATE
        ↓
 FINAL PLATFORM BASELINE
```

---

# 55. Final Rule

The platform should never be developed as:

```text
Page 1
→ Page 2
→ Page 3
→ Page 4
```

It should be developed as:

```text
Business Capability
→ Domain
→ API
→ Data
→ UI
→ Event
→ Worker
→ Test
→ Acceptance
```

The **Final Vertical Slice Order** determines **what comes next**.

This roadmap determines:

```text
what belongs in P0
what belongs in P1
what belongs in P2
what depends on what
and
what must pass before the next phase is accepted
```

The result is a build plan that remains aligned with:

```text
PRD Final
+
Business Decisions
+
Core Contracts #1–#13
+
Core Architecture V1
```

without requiring the entire platform to be completed before the first end-to-end workflow can be tested.
