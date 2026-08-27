# Core Architecture V1 — Platform Foundation & Domain Boundaries

## Status

**Architecture Baseline Draft — based on the finalized PRD, Final Business Decision Register, and Revised Core Contracts #1–#13**

This document translates the finalized product requirements and Core Contracts into an implementable **architecture boundary map**.

It defines:

```text
Domain Boundary
Service / Module Boundary
Data Ownership
API Boundary
Event Boundary
Worker Boundary
Provider Boundary
Storage Boundary
Security Boundary
Transaction Boundary
Configuration Boundary
Tenant / White-label Foundation
```

It does **not** yet define:

- exact cloud vendor;
- exact framework;
- exact programming language;
- exact database technology;
- exact queue technology;
- deployment topology;
- CI/CD implementation;
- infrastructure-as-code.

Those are implementation/infrastructure decisions that come after the logical architecture is accepted.

---

# 1. Architecture Goals

The architecture must preserve the decisions already finalized:

```text
PRD Final
+
Business Decisions Final
+
Core Contracts #1–#13
```

The architecture must therefore be:

- modular;
- domain-oriented;
- provider-agnostic;
- configuration-driven;
- auditable;
- event-capable;
- persistent;
- tenant-ready;
- incrementally buildable;
- testable through vertical slices.

Most importantly:

> **One Core Contract does not automatically equal one microservice.**

The Core Contracts define **domain responsibilities**.

Architecture determines whether a responsibility is implemented as:

```text
Module
Bounded Context
Application Service
Worker
Infrastructure Adapter
or
Separate Deployable Service
```

---

# 2. Architectural Principle

Recommended initial architecture:

> **Modular Monolith + Worker Architecture, with clear bounded contexts and adapter boundaries.**

The first implementation should not require microservices.

Conceptually:

```text
                       WEB / API
                           │
                  Application Boundary
                           │
        ┌──────────────────┼──────────────────┐
        ↓                  ↓                  ↓
   Core Domains       Engine Domains      Admin/Godmode
        │                  │                  │
        └──────────────────┼──────────────────┘
                           ↓
                    Shared Infrastructure
             ┌─────────────┼──────────────┐
             ↓             ↓              ↓
         Database       Event Bus       Storage
             │             │              │
             └─────────────┼──────────────┘
                           ↓
                         Workers
                           ↓
                     External Providers
```

This structure allows:

- rapid development;
- local transactional consistency;
- strong module boundaries;
- independent workers;
- later extraction into services if scale requires it.

---

# 3. High-Level Domain Map

The platform is divided into:

## A. Identity & Access Core

```text
Identity
Session
Role
Permission
Authorization
```

## B. Platform Control Core

```text
Configuration
Feature Flags
Market
Localization Policy
```

## C. Commercial Core

```text
Product
Pricing
Entitlement
Order
Payment
Refund
Referral
```

## D. Integration Core

```text
Provider Infrastructure
Provider Pool
Provider Adapters
Credential References
```

## E. Storage & System Infrastructure

```text
Storage
Event
Audit
Notification
Job / Worker
```

## F. Context Core

```text
Workspace
Research Workspace
Content Plan
Content Slot
```

## G. Research Intelligence

```text
Research
Competitor
Observation
Topic
Trend
Keyword
Audience
Insight
Opportunity
Digest
```

## H. Content Intelligence / Production

```text
Planner
Analyzer
Blueprint
Blueprint Variant
Continuity
Prompt Studio
```

## I. Later Production Domains

Prepared by this architecture:

```text
Asset Preparation
Editor
Export
Analytics
Support
Referral
Tenant / White-label
```

---

# 4. Boundary Rule — Domain Ownership

Every persistent business entity must have one authoritative owner.

Example:

```text
Order
→ owned by Order Domain

Payment
→ owned by Payment Domain

Entitlement
→ owned by Entitlement Domain

Research Opportunity
→ owned by Research Domain

Content Slot
→ owned by Context/Content Planning Domain
```

Other domains may:

```text
reference
read through contract
subscribe to events
```

but must not directly mutate another domain's data tables.

---

# 5. Domain Boundary Matrix

| Area | Primary Owner | Main Responsibility |
|---|---|---|
| Identity | Identity Module | User identity and account state |
| Session | Identity Module | Authentication sessions |
| Authorization | Access Module | Role/Permission decisions |
| Configuration | Configuration Module | System policies/settings |
| Product | Product Module | Product catalog |
| Pricing | Product Module | Price definitions |
| Entitlement | Entitlement Module | User rights/usage |
| Order | Order Module | Commercial purchase |
| Payment | Payment Module | Financial settlement |
| Provider | Provider Infrastructure | External integrations |
| Storage | Storage Module | Binary lifecycle |
| Event | Event Infrastructure | Domain event transport |
| Audit | Audit Module | Immutable history |
| Notification | Notification Module | User communication |
| Workspace | Workspace Module | Workspace boundary |
| Content Slot | Content Context Module | Production identity |
| Research | Research Module | Research intelligence |
| Planner | Planner Module | Planning/calendar |
| Analyzer | Analyzer Module | Content intelligence |
| Blueprint | Production Blueprint Module | Production specification |

---

# 6. Identity Architecture

## 6.1 Identity Boundary

Identity owns:

```text
User
User Status
Credential References
Session
Login
Logout
Session Revocation
Account Lock/Suspension
```

It does not own:

```text
Role
Permission
Membership
Product
Entitlement
Workspace content
```

---

# 7. Session Boundary

Session remains inside the Identity boundary.

Invariant:

```text
One User
→ Maximum One Active Session
```

New login:

```text
Authenticate
→ Revoke Existing Session
→ Create New Session
```

The single-login rule is enforced server-side.

---

# 8. Authorization Architecture

Role/Permission is a separate logical boundary:

```text
Identity
   ↓
Authorization
   ↓
Domain Permission Check
```

Authorization service receives:

```text
user_id
session_id
resource
action
scope
resource_id
tenant_id
workspace_id
```

It returns:

```text
ALLOW
DENY
```

---

# 9. Role and Membership Separation

Architecture must never use:

```text
Membership
```

as a replacement for:

```text
Role
```

Separate paths:

```text
Membership
→ Entitlement / Product Access

Role
→ Permission / Authorization
```

---

# 10. Configuration Architecture

Configuration is a shared control-plane capability.

It should not become a domain "God Service."

Recommended:

```text
Configuration Core
       ↓
Domain Configuration Consumers
```

Configuration owns:

```text
key
value
schema
scope
version
effective time
override
audit
```

Domain modules own:

```text
business meaning
state
transaction
enforcement
```

Example:

```text
Configuration:
support.auto_close_days = 7

Support Module:
owns ticket lifecycle and executes 7-day policy
```

---

# 11. Configuration Scope

Supported logical scopes:

```text
Global
Market
Membership
Role
Product
User
Tenant
Workspace
```

A specific key declares which scopes are valid.

Configuration precedence must be deterministic.

Safety policies override ordinary configurable values.

---

# 12. Feature Flag Boundary

Feature flags live within Configuration Infrastructure but are evaluated by domain modules.

Example:

```text
feature.cross_source_analysis.enabled
```

The Feature Flag layer does not implement Cross-Source Analysis.

It only determines whether the domain feature is enabled.

---

# 13. Product Architecture

Product Domain owns:

```text
Product
Product Version
Product Type
Product Capability Mapping
Product Market Availability
Price
Price Version
Bundle Definition
```

Product Domain does not own:

```text
Payment
Order
Entitlement Ledger
```

---

# 14. Entitlement Architecture

Entitlement is a distinct domain.

It owns:

```text
Entitlement
Entitlement Grant
Consumption
Adjustment
Lock
Unlock
Revoke
Usage Ledger
```

Key rule:

```text
Product
→ defines what can be sold

Entitlement
→ defines what the user actually owns/can use
```

---

# 15. Entitlement Consumption Boundary

Actual consumption happens at the feature execution boundary.

Example:

```text
Blueprint
→ prepares Image Asset Requirement

Asset Generation
→ checks Entitlement
→ consumes Image Generation
→ executes provider
```

Blueprint preparation does not consume generation entitlement.

---

# 16. Order Architecture

Order owns:

```text
Order
Order Item
Commercial Snapshot
Order Lifecycle
```

Order references:

```text
Product
Price
User
Market
Currency
```

It does not directly own:

```text
Provider credentials
Entitlement ledger
Referral calculation
```

---

# 17. Payment Architecture

Payment owns:

```text
Payment
Payment Attempt
Payment Method
Payment Status
Refund
Webhook Reconciliation
```

Order and Payment remain separate.

```text
Order
→ what was bought

Payment
→ how it was settled
```

---

# 18. Payment Provider Boundary

Payment calls:

```text
Provider Infrastructure
```

through an adapter.

Example:

```text
Payment Module
→ Xendit Adapter
→ Provider
```

Payment remains the owner of:

```text
payment status
order settlement
refund
```

Provider Infrastructure does not become owner of Payment business state.

---

# 19. Manual Transfer Boundary

Manual Transfer is implemented as a Payment Method/Provider Adapter.

Flow:

```text
Order
→ Manual Payment
→ Support Ticket
→ Admin Approval
→ Payment Paid
→ Entitlement Fulfillment
```

Support owns the ticket.

Payment owns payment state.

Entitlement owns grant.

No domain writes directly into another domain's tables.

---

# 20. Provider Infrastructure Architecture

Provider Infrastructure is an adapter layer.

Logical structure:

```text
Provider Registry
        ↓
Provider Pool
        ↓
Routing
        ↓
Provider Adapter
        ↓
External Provider
```

It supports:

```text
AI
Research/Data
Payment
```

but does not own the consuming domain's business state.

---

# 21. Provider Pools

Core pools:

```text
Text & Analysis
Image Generator
Video Generator
Voice Generator
Research & Data
```

Payment adapters remain under Payment Domain while using the same provider infrastructure principles.

---

# 22. Provider Credential Boundary

Credentials are stored through:

```text
Secret Management
```

Providers receive:

```text
credential_ref
```

not plaintext credentials.

No domain database stores:

```text
API Secret
Private Key
Webhook Secret
```

---

# 23. Provider Routing Boundary

Routing decides:

```text
which provider
which model
which credential
which route
```

based on:

```text
capability
priority
health
quota
market
policy
cost
quality
latency
```

The consuming domain decides:

```text
what business operation
```

---

# 24. Storage Architecture

Storage owns:

```text
StorageObject
Upload
Download Authorization
Retention
Purge
Storage Usage
Storage Provider Adapter
```

Binary:

```text
Object Storage
```

Metadata:

```text
Application Database
```

---

# 25. Storage vs Business Data

Storage does not own:

```text
Project
Content Slot
Support Ticket
Blueprint
Order
```

Those domains reference:

```text
storage_object_id
```

---

# 26. Storage Retention Architecture

Retention is resolved before/at object creation and stored as:

```text
retention_expires_at
```

Current finalized policies:

```text
Content Export / Editor Media
→ 48 hours

Support Attachment
→ 90 days after Closed
```

Storage Worker performs:

```text
Eligibility
→ Dependency Check
→ Purge
```

---

# 27. Event Architecture

Events are a platform infrastructure capability.

Logical pipeline:

```text
Domain Transaction
   ↓
Outbox
   ↓
Event Bus
   ↓
Handlers / Workers
```

Events are:

```text
immutable
versioned
idempotent
at-least-once
```

---

# 28. Event Ownership

Domain owns event meaning.

Example:

```text
Payment Domain
→ PaymentPaid
```

Event Infrastructure owns:

```text
delivery
retry
routing
dead-letter
replay
```

---

# 29. Audit Architecture

Audit is separate from Event Transport.

```text
Domain Action
   ↓
Audit Record
```

Audit owns:

```text
actor
target
action
before
after
reason
timestamp
session
correlation
```

Audit records are append-only.

---

# 30. Notification Architecture

Notification is separate from Audit and Event Transport.

```text
Domain Event
   ↓
Notification Policy
   ↓
Recipient Resolver
   ↓
Template
   ↓
Delivery Adapter
```

Initial channels:

```text
In-App
Email foundation
```

Future:

```text
Push
SMS
WhatsApp
```

---

# 31. Job / Worker Architecture

Long-running or asynchronous operations use workers.

Examples:

```text
Provider Jobs
Research Sync
Analyzer Run
Blueprint Generation
Asset Generation
Storage Purge
Notification Delivery
Digest Generation
Webhook Retry
```

Workers consume commands/events and update their owning domain through domain services.

---

# 32. Workspace Architecture

Workspace owns:

```text
Workspace
Workspace Membership
Workspace Status
Workspace Settings Reference
```

It does not own:

```text
Content Slot details
Research data
Role definition
Membership billing
```

Workspace access is enforced through Authorization.

---

# 33. Content Context Architecture

Content Context owns:

```text
Content Plan
Content Slot
Content Slot Lifecycle
Ownership
Workspace Relationship
Revision
Lock
```

`content_slot_id` is the production anchor.

---

# 34. Content Slot Relationship

Core production chain:

```text
Content Slot
  ↓
Research Reference
  ↓
Planner
  ↓
Analyzer
  ↓
Blueprint Variant
  ↓
Asset Requirements
  ↓
Editor
  ↓
Export
  ↓
Analytics Attribution
```

Domains reference `content_slot_id`.

They do not duplicate the Content Slot as a separate project identity.

---

# 35. Research Architecture

Research is a bounded domain.

Canonical graph:

```text
Research Workspace
      ↓
Competitor
      ↓
Content Observation
      ↓
Topic
 ├── Hook Pattern
 ├── CTA Pattern
 ├── Keyword
 └── Trend Signal
      ↓
Audience Signal
      ↓
Research Evidence
      ↓
Research Insight
      ↓
Opportunity
      ↓
Planner / Analyzer
```

This follows the revised Core Contract #10 and final PRD Research Data Model.

---

# 36. Research Provider Boundary

Research calls:

```text
Research Provider Pool
```

Research does not know:

```text
API key
provider URL
vendor schema
```

Research receives normalized results.

---

# 37. Research vs Analytics

Analytics owns:

```text
performance ingestion
platform metrics
historical performance
performance calculation
```

Research consumes Analytics-derived signals to produce:

```text
pattern
insight
opportunity
```

Research does not recreate the Analytics engine.

---

# 38. Research vs Analyzer

Research owns:

```text
competitive intelligence
trend
keyword
audience demand
research opportunity
research insight
```

Analyzer owns:

```text
deep analysis of selected source/set
claim/evidence interpretation
angle
content readiness
```

They share:

```text
Research Source
Research Evidence
```

instead of duplicating them.

---

# 39. Planner Architecture

Planner owns:

```text
Content Plan
Idea Pool
Content Idea
Calendar
Scheduling
Constraints
Pillar Allocation
Content Type Allocation
Calendar Health
Versioning
```

Planner consumes:

```text
Research Opportunity
Analytics Recommendation
```

Planner is the authority for calendar state.

---

# 40. Planner Boundary

Planner does not own:

```text
Research truth
Analyzer truth
Script
Asset
Editor
Analytics metrics
```

It creates:

```text
Content Slot
```

and planning metadata.

---

# 41. Analyzer Architecture

Analyzer owns:

```text
Analyzer Run
Source Classification
Quality Gate
Structured Extraction
Claim Interpretation
Angle
Hook Validation
Content Readiness
Multi-AI
Deep Intelligence
Media Intelligence
Cross-Source Analysis
```

Analyzer references Research's canonical:

```text
Source
Evidence
Topic / Audience context
```

It does not create a duplicate Research Workspace.

---

# 42. Analyzer Add-on Boundary

Official capabilities:

```text
Multi-AI
Deep Source Intelligence
Media Intelligence
Cross-Source Analysis
```

Access is:

```text
Authorization
+
Entitlement
```

The add-ons extend Analyzer capability without changing Identity/Product/Payment architecture.

---

# 43. Blueprint Architecture

Blueprint owns:

```text
Content Production Blueprint
Blueprint Variant
Script
Slide
Shot
Prompt
Asset Requirement
Editor Mapping
Production QA
```

The core production relation is:

```text
Content Slot
+
Angle
+
Content Type
→ Blueprint Variant
```

---

# 44. Blueprint Variant Boundary

Example:

```text
Content Slot CS-100
├── Angle A × Carousel
├── Angle A × Video
└── Angle B × Video
```

Each Variant has independent:

```text
Script
Visual Blueprint
Prompt
Asset Requirements
Editor Mapping
QA
Approval
Version
```

---

# 45. Blueprint Add-on Architecture

Official Module 3 add-ons:

```text
Visual Continuity Engine
Advanced Prompt Studio & Auto-Fix
```

They are capabilities layered on Blueprint Variant.

They do not own:

```text
Product
Payment
Entitlement Ledger
```

---

# 46. Visual Continuity Boundary

Continuity owns:

```text
Continuity Group
Locked Attributes
Inheritance
Consistency Validation
```

Flow:

```text
Blueprint Prompt
→ Continuity Resolution
→ Final Structured Prompt
```

Actual provider execution remains in Provider/Asset Generation boundary.

---

# 47. Prompt Studio Boundary

Prompt Studio owns:

```text
Prompt Inspection
Prompt Editing
Provider-specific Optimization
Auto-Fix
Prompt Validation
```

Canonical prompt remains provider-neutral.

---

# 48. Auto-Fix Boundary

Auto-Fix can modify:

```text
copy
layout
prompt
asset requirement
```

but must preserve:

```text
angle
evidence meaning
audience
objective
content type
```

Auto-Fix creates a new revision.

---

# 49. Support Architecture

Support is a separate domain even though it was not yet fully represented by a numbered Core Contract.

Support owns:

```text
Ticket
Reply
Status
Priority
Assignment
SLA
Resolution
Attachment Reference
Auto-Close
Reopen
```

Current finalized policy:

```text
Attachment ≤ 2 MB
PDF / PNG / JPG
Auto-close if user does not reply within 7 days
Closed ticket can be reopened by Admin only
```

Support attachment binaries are owned by Storage.

---

# 50. Referral Architecture

Referral is a separate domain even though the numbered Core Contracts have not yet isolated it.

Referral owns:

```text
Attribution
Downline relationship
Active Downline
Commission
Milestone
Withdrawal Request
Payout
Clawback
```

Core rules:

```text
Attribution window:
90 days default

Commission:
10% of actual amount paid after discount

Minimum withdrawal:
20,000 IDR
```

Referral consumes Order/Payment events but does not own Order/Payment.

---

# 51. Membership Architecture

Membership is a commercial/domain layer around:

```text
Subscription
Billing Cycle
Active / Inactive state
Membership Product
Membership Entitlements
```

Membership does not replace:

```text
Role
```

and does not directly operate payment provider state.

---

# 52. Analytics Architecture

Analytics is a separate engine/domain.

It owns:

```text
Performance Ingestion
Metrics
Baseline
Median
Pattern Detection
Performance Insight
Recommendation
Learning Signals
```

Analytics produces recommendations for:

```text
Research
Planner
Future Blueprint Optimization
```

It does not silently mutate strategy.

---

# 53. Admin Godmode Architecture

Godmode is an administrative application/control plane.

It does not bypass domain ownership.

Example:

```text
Admin UI
→ Product API
→ Product Domain

Admin UI
→ Provider API
→ Provider Infrastructure

Admin UI
→ Configuration API
→ Configuration Domain
```

Godmode is therefore:

```text
control plane
```

not a separate business database.

---

# 54. Admin Role Builder

Role Builder operates through:

```text
Authorization API
```

It can:

```text
create role
assign permissions
assign role
```

but cannot invent backend capabilities that do not exist.

---

# 55. Product Builder

Product Builder operates through:

```text
Product Domain
```

Admin can create:

```text
Membership
Package
Add-on
Bundle
```

only when the corresponding capability exists in Core.

---

# 56. Provider Administration

Admin controls:

```text
Provider
Pool
Capability
Model
Priority
Health
Credential Reference
Routing
```

Provider Administration must not directly mutate Research, Payment, or Asset business records.

---

# 57. Multi-language Architecture

Localization is platform infrastructure.

Logical:

```text
Language Catalog
Translation Resources
Locale Resolver
Market Default
User Override
Fallback
```

Minimum:

```text
id
en
```

Future languages can be added.

Business domain data should not contain UI-translated copies unnecessarily.

---

# 58. Currency Architecture

Currency is shared configuration/commerce infrastructure.

Minimum:

```text
IDR
USD
```

Market determines defaults.

Product Price owns actual:

```text
amount
currency
```

Order/Payment snapshots the actual transaction currency.

---

# 59. Market Architecture

Market is a first-class context:

```text
market
language
currency
payment providers
product availability
```

Market is not the same as:

```text
tenant
workspace
language
currency
```

They are related but separate dimensions.

---

# 60. Tenant / White-label Foundation

White-label is:

> **core-ready, not fully implemented in the current product phase.**

Architecture prepares:

```text
tenant_id
tenant configuration
tenant domain reference
tenant product visibility
tenant pricing policy
tenant branding reference
```

Full White-label UI/business flow remains a separate future project.

---

# 61. Agency / Wholesale Settlement Boundary

Future Agency settlement differs from normal member billing.

Normal member:

```text
Order
→ Payment
→ Entitlement
```

Agency:

```text
Agency Deposit
→ Webmaster Approval
→ Agency Available Balance
→ Member Order
→ Wholesale Deduction
```

Agency deposit is not the normal member wallet.

Agency deposit cannot be refunded according to finalized decisions.

This remains future/core-ready.

---

# 62. Tenant Isolation

Future White-label authorization:

```text
tenant_id
→ workspace_id
→ resource
```

Cross-tenant access:

```text
DENY
```

unless explicit global administrative permission exists.

---

# 63. Data Architecture — Source of Truth

Recommended ownership:

```text
Identity DB
→ User / Session

Access DB
→ Role / Permission

Configuration DB
→ Config

Commerce DB
→ Product / Price / Entitlement / Order / Payment / Referral

Research DB
→ Research domain

Content DB
→ Workspace / Content Plan / Content Slot / Planner / Blueprint

Analytics DB/Store
→ Performance / Insights

Support DB
→ Ticket / SLA

Storage Metadata DB
→ StorageObject
```

An initial modular monolith may still use one physical database with strict schema/module boundaries.

Logical ownership must exist before physical database splitting.

---

# 64. Cross-Domain Reference Rule

Allowed:

```text
reference ID
read API
domain event
```

Avoid:

```text
direct table mutation
cross-domain transaction by default
```

Example:

```text
Referral
→ reads PaymentPaid event

not:

Referral
→ writes Payment table
```

---

# 65. Database Transaction Boundary

A domain transaction should normally cover:

```text
one domain's state
+
its local outbox record
```

Cross-domain workflows use:

```text
event
+
idempotent handler
```

rather than one giant database transaction.

---

# 66. Example — Payment → Entitlement

```text
Payment Domain
    ↓
Payment = PAID
    ↓
Outbox
    ↓
PaymentPaid Event
    ↓
Entitlement Handler
    ↓
Entitlement Grant
```

If Entitlement fails temporarily:

```text
retry
```

Payment is not rolled back.

---

# 67. Example — Payment → Referral

```text
PaymentPaid
    ↓
Referral Handler
    ↓
Commission Pending
```

Referral does not participate in the original Payment database transaction.

---

# 68. Example — Support Manual Transfer

```text
Support Ticket
    ↓
Admin Approval
    ↓
Command Payment Domain
    ↓
Payment = PAID
    ↓
PaymentPaid
    ↓
Entitlement Grant
```

Support does not directly grant entitlement.

---

# 69. Event / Queue Topology

Logical queues:

```text
Critical Domain Events
Standard Domain Events
Long-running Jobs
Provider Jobs
Notification Jobs
Storage Jobs
Analytics Jobs
```

Exact broker implementation is not fixed.

---

# 70. Idempotency Strategy

Idempotency keys should exist at:

```text
API request
Payment attempt
Webhook event
Entitlement grant
Entitlement consumption
Provider request where supported
Background job
Notification delivery
```

Typical identifiers:

```text
request_id
event_id
external_event_id
order_id
payment_id
job_id
```

---

# 71. Concurrency Control

Important domains require optimistic concurrency/versioning:

```text
Configuration
Content Slot
Planner
Blueprint
Payment state
Entitlement
```

Examples:

```text
revision_number
version
updated_at
```

---

# 72. Security Boundary

Security responsibilities are distributed:

```text
Identity
→ authentication/session

Authorization
→ permission

Domain
→ business authorization

Storage
→ file access

Provider Infrastructure
→ credential security

Audit
→ historical integrity
```

No single module is allowed to bypass another boundary merely because the caller is Admin.

---

# 73. Admin Safety Boundary

Admin permissions are powerful but still constrained by:

```text
system safety rules
financial integrity
audit integrity
tenant isolation
credential secrecy
```

Example:

```text
Admin can configure role
≠
Admin can disable mandatory security invariant
```

---

# 74. API Architecture

Recommended external boundary:

```text
Web Client
   ↓
API Layer / Application Layer
   ↓
Domain Services
```

The client does not call:

```text
Database
Provider
Storage
Queue
```

directly except through secure upload/download mechanisms intentionally issued by the server.

---

# 75. Application Layer

Application services orchestrate use cases.

Examples:

```text
CreateOrder
ApproveManualPayment
GenerateResearchRun
GeneratePlan
RunAnalyzer
CreateBlueprintVariant
ApproveBlueprint
```

Application layer coordinates domains.

Domain rules remain inside domain services.

---

# 76. Domain Service vs Application Service

Example:

```text
Application:
ApproveManualPaymentUseCase

Support Domain:
approve ticket

Payment Domain:
confirm payment

Event:
PaymentPaid
```

Do not put all business rules into controller/API handlers.

---

# 77. Worker Architecture

Workers should be used for:

```text
Research Sync
Provider Calls
Analyzer Jobs
Blueprint AI Generation
Storage Purge
Notification Delivery
Analytics Processing
Digest Generation
Webhook Retry
```

Short deterministic reads/writes remain synchronous where practical.

---

# 78. Provider Async Job Boundary

For long provider jobs:

```text
Application Request
→ Create Job
→ Worker
→ Provider
→ Poll/Callback
→ Normalize
→ Domain Result
```

The user-facing request does not need to remain open for the entire provider operation.

---

# 79. Storage Upload Boundary

Recommended:

```text
Client
→ Request Upload Session
→ Server Authorizes
→ Signed Upload URL
→ Object Storage
→ Commit
→ StorageObject Available
```

Binary does not pass through the application server unless required.

---

# 80. Signed Download Boundary

```text
Client
→ Request Download
→ Server Authorization
→ Signed URL
→ Object Storage
```

The client never receives unrestricted storage credentials.

---

# 81. Observability Architecture

All major workflows should carry:

```text
request_id
correlation_id
causation_id
```

Logs should include:

```text
service/module
operation
user_id
workspace_id
tenant_id
duration
status
error
provider_id
```

Sensitive values must be redacted.

---

# 82. Monitoring Domains

Monitor:

```text
API
Database
Queue
Workers
Providers
Storage
Payments
Research
AI Generation
Notifications
```

Metrics should be aggregated by domain rather than only by server.

---

# 83. Financial Observability

For every commercial workflow:

```text
Order
→ Payment
→ Entitlement
→ Referral
```

there must be a traceable correlation chain.

This supports:

```text
support
finance
refund
reconciliation
audit
```

---

# 84. Research Observability

Research should trace:

```text
ResearchRun
→ ProviderResult
→ Provider
→ Source
→ Insight
→ Opportunity
```

This is critical when data is estimated or incomplete.

---

# 85. Production Observability

Production should trace:

```text
Content Slot
→ Analyzer Run
→ Blueprint Variant
→ Asset Requirement
→ Generation Job
→ Provider
→ Asset
→ Editor
→ Export
```

This provides end-to-end production debugging.

---

# 86. API Versioning

Public API should support versioning from the beginning.

Example:

```text
/api/v1/
```

Internal domain contracts should also be versioned when externally consumed.

---

# 87. Event Versioning

Events use:

```text
event_type
event_version
```

Example:

```text
PaymentPaid.v1
ResearchOpportunityCreated.v1
BlueprintVariantApproved.v1
```

Consumers should not depend on unstable event payloads.

---

# 88. Configuration / Feature Activation Flow

Example:

```text
Admin
→ Configuration
→ Enable Capability
→ Domain evaluates
→ Feature available
```

Feature availability may still be restricted by:

```text
Entitlement
Role
Market
Tenant
Product
```

---

# 89. Permission / Entitlement Evaluation

A feature request may require both:

```text
Authorization
+
Entitlement
```

Example:

```text
Use Deep Source Intelligence

Authorization:
allowed? YES

Entitlement:
owned? NO

Result:
DENY
```

For Admin operations:

```text
Authorization
+
System Safety Rule
```

may be sufficient.

---

# 90. Architecture for Add-ons

General capability flow:

```text
Product
→ Price
→ Order
→ Payment
→ Entitlement
→ Capability Check
→ Domain Execution
```

This supports:

```text
Image Package
Video Package
Deep Source Intelligence
Multi-AI
Visual Continuity
Advanced Prompt Studio
```

without creating separate billing systems for each feature.

---

# 91. Error Boundary

Errors should be classified as:

```text
Validation
Authorization
Business Rule
Provider
Infrastructure
Concurrency
Not Found
Timeout
```

Domain errors should not expose:

```text
secret
provider credentials
internal SQL
stack traces
```

---

# 92. Resilience Principles

External provider failures should not corrupt domain state.

Recommended:

```text
Timeout
Retry
Circuit Breaker
Fallback
Dead Letter
Manual Review
```

only where semantically safe.

---

# 93. Data Retention Boundary

Retention belongs to the data owner.

Examples:

```text
Storage
→ binary retention

Audit
→ audit retention

Research
→ research retention

Notification
→ notification retention

Financial
→ financial/legal policy
```

Do not create one universal purge policy.

---

# 94. Backup / Recovery Boundary

Backup belongs to infrastructure.

Logical domains must remain recoverable independently enough to rebuild:

```text
critical business state
```

Storage metadata and binary backup policy must be aligned.

Exact backup vendor/technology is not specified in this architecture version.

---

# 95. White-label Architecture

Current implementation:

```text
Core-ready only
```

Prepared fields:

```text
tenant_id
tenant configuration
domain reference
market
currency
product visibility
pricing policy
branding reference
```

Not implemented now:

```text
full white-label frontend
agency admin UI
custom domain automation
agency onboarding workflow
full tenant billing
```

---

# 96. Agency API Model

Future:

```text
Agency Website
      ↓
Webmaster API
      ↓
Product Catalog / Pricing / Entitlement Capability
```

Agency can apply:

```text
fixed override price
percentage markup
```

according to finalized business rules.

The Webmaster remains the source for master product capability.

---

# 97. Architecture Dependency Graph

High-level:

```text
Identity
   ↓
Authorization
   ↓
Configuration
   ↓
┌───────────────┬─────────────────┐
↓               ↓                 ↓
Commerce      Workspace        Provider
↓               ↓                 ↓
Entitlement  Content Context      │
↓               ↓                 │
Order         Research ←──────────┘
↓               ↓
Payment       Planner
↓               ↓
Referral      Analyzer
                ↓
             Blueprint
                ↓
          Asset Preparation
                ↓
             Editor
                ↓
             Export
                ↓
            Analytics
                ↓
        Research feedback loop
```

---

# 98. Vertical Slice Readiness

The architecture is intentionally designed to support:

```text
Build
→ Test
→ Verify
→ Continue
```

without requiring the entire platform to exist first.

Each vertical slice should include:

```text
UI
Application API
Domain
Persistence
Events
Worker where needed
Tests
Observability
```

only for the slice being built.

---

# 99. Recommended Initial Physical Structure

Before scale requires service extraction:

```text
/apps
  /web
  /api
  /worker

/modules
  /identity
  /authorization
  /configuration
  /commerce
  /provider
  /storage
  /events
  /audit
  /notification
  /workspace
  /content-context
  /research
  /planner
  /analyzer
  /blueprint
  /support
  /referral
  /analytics
```

This is a logical structure, not a mandated filesystem implementation.

---

# 100. Modular Monolith Rule

A module may not directly access another module's repository/data layer.

Allowed:

```text
Module A
→ Module B Application Contract
```

or:

```text
Module A
→ Domain Event
```

Not allowed:

```text
Module A
→ SQL query into Module B tables
```

This rule is essential if later extraction into microservices is desired.

---

# 101. Extraction Readiness

A module is a candidate for future service extraction if it has:

```text
clear owner
clear data
clear API
clear events
low direct coupling
```

Likely extraction candidates later:

```text
Provider Workers
Research Workers
Analytics Processing
Notification Delivery
Asset Generation
Storage Worker
Payment Integration
```

Do not extract everything into microservices prematurely.

---

# 102. Critical First Architecture Invariants

Before implementation begins, the following must be treated as non-negotiable:

```text
1. One User → max one active Session.

2. Role ≠ Membership.

3. Product ≠ Entitlement.

4. Order ≠ Payment.

5. Provider ≠ Business Domain.

6. File ≠ Project.

7. Event ≠ Audit.

8. Event ≠ Notification.

9. Workspace ≠ Tenant.

10. Research ≠ Analytics.

11. Research ≠ Analyzer.

12. Blueprint ≠ Asset Generation.

13. Blueprint Variant = Angle × Content Type.

14. Normal member billing does not use Top Up/PAYG Wallet/Deposit.

15. Financial history is immutable.

16. Domain owns its own data.

17. Cross-domain mutation uses contracts/events.

18. Admin Godmode does not bypass domain ownership.

19. White-label is core-ready, not current full product.

20. Server-side state is authoritative.
```

---

# 103. Architecture Definition of Done

Core Architecture V1 is complete when:

1. All Core Contracts #1–#13 have a clear logical owner.
2. No two domains claim the same authoritative data.
3. Core/Engine boundaries are explicit.
4. Provider infrastructure is separated from business domains.
5. Configuration is not a business-logic God Service.
6. Event, Audit, and Notification have clear boundaries.
7. Research uses the revised PRD-aligned data model.
8. Planner uses Content Slot as the production anchor.
9. Analyzer uses Research data instead of duplicating it.
10. Blueprint Variant is explicit.
11. Actual generation is outside Blueprint.
12. Storage is separated from business data.
13. Order and Payment are separate.
14. Entitlement is separated from Product.
15. Workspace is separated from Tenant.
16. Cross-domain transactions have defined event/async strategy.
17. Worker boundaries are defined.
18. Security boundaries are defined.
19. Configuration boundaries are defined.
20. White-label foundation is core-ready.
21. The architecture supports incremental vertical slices.
22. The architecture can remain a modular monolith initially.
23. The architecture leaves clear future extraction boundaries.

---

# 104. Final Architecture Position

The platform should now be understood as:

```text
                    PLATFORM
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
      CORE          ENGINES       CONTROL PLANE
        │              │              │
        │              │              └── Admin Godmode
        │              │
        │      ┌───────┼────────┐
        │      ↓       ↓        ↓
        │   Research  Planner  Analytics
        │      ↓       ↓
        │   Analyzer   │
        │      ↓       │
        │   Blueprint  │
        │      ↓       │
        │ Asset Prep   │
        │      ↓       │
        │    Editor    │
        │      ↓       │
        │    Export    │
        │
        ├── Identity
        ├── Authorization
        ├── Configuration
        ├── Commerce
        ├── Provider Infrastructure
        ├── Storage
        ├── Event Infrastructure
        ├── Audit
        ├── Notification
        └── Workspace / Content Context
```

The logical architecture is now ready for the next planning step:

> **Final Vertical Slice Order**

That step should map this architecture into a build sequence where every slice is independently testable and demonstrable.
