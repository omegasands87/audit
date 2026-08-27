# Implementation Specification per Vertical Slice

## Status

**Final Implementation Specification Framework**

Dokumen ini menjadi template/standar yang digunakan untuk setiap Vertical Slice pada P0, P1, dan P2.

Dokumen ini tidak menggantikan:

- PRD Final
- Business Decision Register
- Core Contracts #1–#13
- Core Architecture V1
- Final Vertical Slice Order
- Implementation Roadmap P0/P1/P2

Fungsinya adalah menjembatani:

```text
PRD / Business Decision
        ↓
Core Contract
        ↓
Core Architecture
        ↓
Implementation Specification
        ↓
Implementation
        ↓
Test
        ↓
Acceptance Gate
        ↓
Lock
        ↓
Next Slice
```

---

# 1. One Slice = One Implementation Specification

Setiap slice akan memiliki dokumen spesifikasi sendiri.

Contoh:

```text
P0.00_Architecture_Development_Skeleton.md
P0.01_Identity_Session_Single_Login.md
P0.02_Role_Permission_Authorization.md
...
P1.01_Planner_Intelligence.md
...
P2.04_Full_Platform_Regression.md
```

Dokumen ini mendefinisikan format yang harus digunakan untuk seluruh slice.

---

# 2. Mandatory Sections

Setiap Implementation Specification harus berisi:

```text
1. Slice Identity
2. Objective
3. Scope
4. Out of Scope
5. Source Documents
6. Dependencies
7. Architecture Boundary
8. Domain Ownership
9. Data Model
10. State Machine
11. API / Application Contract
12. UI / UX Scope
13. Event Contract
14. Worker / Job Scope
15. Security / Authorization
16. Configuration
17. Storage
18. Observability
19. Error Handling
20. Idempotency / Concurrency
21. Test Specification
22. Acceptance Criteria
23. Acceptance Gate
24. Implementation Checklist
25. Exit Conditions
26. Known Risks
27. Open Decisions
```

Tidak semua section harus panjang, tetapi semuanya harus ditinjau secara eksplisit.

---

# 3. Slice Identity

Format:

```text
Slice ID:
Phase:
Order:
Title:
Status:
Priority:
```

---

# 4. Objective

Tuliskan hasil yang harus dicapai dan harus dapat diuji.

Hindari tujuan yang terlalu umum.

Contoh:

```text
Create a runnable Web/API/Worker foundation with database,
logging, health checks, module boundaries, infrastructure
abstractions, and automated test harness.
```

---

# 5. Scope

Pisahkan dengan tegas:

```text
IN
OUT
```

Contoh:

```text
IN:
- API skeleton
- Worker skeleton
- Database connection

OUT:
- User registration
- Payment
- Research
```

Scope tidak boleh meluas secara diam-diam selama implementation.

---

# 6. Source Documents

Setiap slice harus menyebut sumber yang menjadi dasar implementasinya.

Minimum:

```text
PRD Final
Business Decision Register
Relevant Core Contract(s)
Core Architecture V1
Final Vertical Slice Order
Implementation Roadmap
```

Cantumkan section spesifik bila relevan.

---

# 7. Dependencies

Pisahkan:

```text
Required
Optional
Future
```

Sebutkan slice lain yang harus sudah PASS.

Format:

```text
Depends On:
Blocks:
Consumes:
Produces:
```

---

# 8. Architecture Boundary

Jelaskan posisi slice:

```text
UI
↓
Application Layer
↓
Domain
↓
Persistence
↓
Events / Workers
↓
Provider / Storage bila diperlukan
```

Jelaskan juga apa yang **tidak** dimiliki slice tersebut.

Aturan utama:

> Satu domain tidak boleh menjadi owner kedua untuk data milik domain lain.

---

# 9. Domain Ownership

Wajib mencatat:

```text
Owned Entities
Read-only References
Events Consumed
Events Produced
```

Contoh:

```text
Owned:
User
Session

Reference:
Role

Produced:
UserCreated
SessionRevoked
```

---

# 10. Data Model

Setiap entity penting harus menjelaskan:

```text
Field
Type
Required?
Default
Validation
Constraint
Index
Relation
Owner
```

Contoh:

| Field | Type | Required | Constraint | Owner |
|---|---|---|---|---|
| `example_id` | UUID | Yes | PK | Domain |
| `status` | Enum | Yes | allowed values | Domain |

Tambahkan bila relevan:

```text
created_at
updated_at
version
revision
archived_at
```

---

# 11. State Machine

Untuk entity yang memiliki lifecycle:

```text
STATE A
 ↓
STATE B
 ↓
STATE C
```

Wajib mendefinisikan:

```text
Allowed Transition
Actor
Permission
Validation
Side Effects
Event
```

Illegal transition harus ditolak.

---

# 12. API / Application Contract

Untuk setiap operation:

```text
Use Case Name
Actor
Permission
Input
Validation
Domain Action
Output
Errors
Events
```

Contoh:

```text
CreateContentSlot

Actor:
Member

Permission:
content_slot.create

Input:
workspace_id
title
scheduled_at

Output:
content_slot_id

Event:
ContentSlotCreated
```

---

# 13. API Boundary

Bedakan:

```text
External API
Internal Application API
Domain Service
Repository
Provider Adapter
Storage Adapter
```

UI tidak boleh langsung mengakses:

```text
Database
Provider
Storage
Queue
```

di luar boundary yang telah ditetapkan.

---

# 14. UI / UX Scope

Setiap screen yang termasuk slice harus menjelaskan:

```text
Route
Purpose
Components
Actions
Data
Permission
Loading State
Empty State
Error State
Success State
Saving State
Conflict State
```

Tidak ada UI yang dianggap selesai hanya karena tampil.

---

# 15. Functional UI Rule

Vertical slice harus membuktikan:

```text
UI
↔ API
↔ Domain
↔ Persistence
```

dan jika diperlukan:

```text
↔ Worker
↔ Provider
↔ Storage
```

Button dan action harus menjalankan fungsi nyata.

---

# 16. Event Contract

Untuk setiap event:

```text
Event Name
Version
Producer
Trigger
Payload
Correlation ID
Causation ID
Idempotency
Consumers
Retry Policy
```

Event bersifat immutable setelah dipublish.

---

# 17. Worker / Job Scope

Jika membutuhkan asynchronous processing, dokumentasikan:

```text
Job Name
Trigger
Input
Output
Timeout
Retry
Idempotency
Failure State
DLQ
```

Tidak semua slice wajib memiliki worker.

---

# 18. Security / Authorization

Setiap protected operation harus menjelaskan:

```text
Authentication
Role
Permission
Scope
Resource Ownership
Workspace Scope
Tenant Scope
```

Authorization wajib server-side.

---

# 19. Configuration

Jika behavior configurable, dokumentasikan:

```text
Configuration Key
Type
Default
Allowed Values
Scope
Runtime Effect
Audit Requirement
```

Configuration menyimpan policy/value.

Domain tetap memiliki business logic dan enforcement.

---

# 20. Storage

Jika slice menggunakan file/media:

```text
Storage Object
Owner
Purpose
Upload Method
Download Authorization
Retention
Purge
```

Binary tidak boleh disimpan di business table kecuali memang dibutuhkan oleh contract.

---

# 21. Observability

Minimum yang dibutuhkan:

```text
structured logs
request_id
correlation_id
event_id
operation
duration
success/failure
```

Jangan log:

```text
password
API secret
private key
payment secret
```

---

# 22. Audit

Tentukan operasi yang wajib diaudit.

Contoh:

```text
Role Change
Payment Approval
Refund
Entitlement Adjustment
Configuration Change
Provider Credential Change
Manual Override
Security Policy Change
```

Audit minimal:

```text
actor
action
target
before
after
reason
timestamp
correlation_id
```

---

# 23. Error Handling

Kelompok error:

```text
Validation
Authorization
Not Found
Business Rule
Concurrency
Provider
Timeout
Storage
Infrastructure
```

Contoh code:

```text
PERMISSION_DENIED
VERSION_CONFLICT
ENTITLEMENT_REQUIRED
PROVIDER_TIMEOUT
```

User-facing error harus aman dan jelas.

---

# 24. Idempotency

Wajib ditentukan untuk operasi yang dapat dipanggil ulang:

```text
Payment
Webhook
Entitlement Grant
Entitlement Consumption
Provider Job
Generation
Notification
Storage Commit
Background Job
```

Contoh:

```text
event_id + handler_id
```

atau:

```text
payment_id + external_event_id
```

---

# 25. Concurrency

Jika data dapat diedit dari beberapa session:

```text
revision
version
lock
optimistic concurrency
```

Contoh:

```text
expected_revision = 10
current_revision = 11

→ VERSION_CONFLICT
```

Silent overwrite dilarang bila contract memerlukan protection.

---

# 26. Test Specification

Setiap slice harus memiliki:

## Unit Tests

```text
Domain Rules
Calculations
Validators
State Transitions
```

## Integration Tests

```text
Database
API
Repository
Events
Workers
Provider Adapters
Storage
```

## End-to-End Tests

```text
Real Browser / API Journey
```

## Regression

```text
Previously completed slices
```

---

# 27. Acceptance Criteria

Gunakan format:

```text
Given
When
Then
```

Contoh:

```text
Given user memiliki permission content_slot.edit
When user mengubah Content Slot
Then perubahan tersimpan
And revision bertambah
And ContentSlotUpdated diterbitkan
```

Acceptance criteria harus observable.

---

# 28. Acceptance Gate

Setiap slice wajib melewati:

## Gate A — Functional

```text
Happy Path PASS
```

## Gate B — Failure

```text
Invalid Input
Permission Denied
Conflict
Timeout
Provider Failure
Retry / Recovery
```

## Gate C — Persistence

```text
Refresh
Logout
Login
Reload
```

## Gate D — Observability

```text
Logs
Events
Audit
Correlation
Error Trace
```

Slice belum boleh dikunci sebelum gate yang relevan PASS.

---

# 29. Definition of Done

Checklist minimum:

```text
[ ] Scope selesai
[ ] Data model selesai
[ ] Domain logic selesai
[ ] API selesai
[ ] UI selesai jika diperlukan
[ ] Authorization selesai
[ ] Persistence selesai
[ ] Event selesai jika diperlukan
[ ] Worker selesai jika diperlukan
[ ] Audit selesai jika diperlukan
[ ] Error handling selesai
[ ] Idempotency selesai jika diperlukan
[ ] Tests PASS
[ ] Acceptance Gate PASS
[ ] Manual Acceptance PASS
[ ] Dokumentasi diperbarui
```

---

# 30. Exit Conditions

Slice dianggap selesai dan dapat dikunci jika:

```text
Functional = PASS
Data Integrity = PASS
Security = PASS
Persistence = PASS
Observability = PASS
Regression = PASS
```

Setelah LOCK:

> Perubahan harus diperlakukan sebagai controlled change, bukan improvisasi.

---

# 31. Open Decisions

Jika ditemukan hal yang belum ditentukan oleh:

```text
PRD
Business Decision Register
Core Contract
Core Architecture
```

jangan membuat business rule permanen secara diam-diam.

Catat:

```text
Question
Impact
Options
Recommendation
Decision
```

Lalu update dokumen sumber yang sesuai.

---

# 32. Risk Register

Untuk setiap slice:

```text
Risk
Impact
Probability
Mitigation
Owner
```

Contoh:

```text
Risk:
Provider schema berubah

Impact:
Normalization gagal

Mitigation:
Provider Adapter + canonical domain model
```

---

# 33. Implementation Checklist

## Architecture

```text
[ ] Boundary confirmed
[ ] Domain owner confirmed
[ ] Cross-domain access checked
```

## Data

```text
[ ] Schema created
[ ] Migration created
[ ] Constraints created
[ ] Indexes verified
```

## Backend

```text
[ ] Domain service
[ ] Application use case
[ ] API
[ ] Validation
[ ] Error handling
```

## Frontend

```text
[ ] Route
[ ] Screen
[ ] Loading
[ ] Empty
[ ] Error
[ ] Success
[ ] Permission
[ ] Saving / Conflict where applicable
```

## Integration

```text
[ ] Events
[ ] Worker
[ ] Provider
[ ] Storage
```

## Security

```text
[ ] Authentication
[ ] Authorization
[ ] Workspace scope
[ ] Tenant scope
```

## Testing

```text
[ ] Unit
[ ] Integration
[ ] E2E
[ ] Regression
```

## Acceptance

```text
[ ] Gate A
[ ] Gate B
[ ] Gate C
[ ] Gate D
[ ] Manual acceptance
```

---

# 34. Naming Convention

Gunakan:

```text
P0.00_Architecture_Development_Skeleton.md
P0.01_Identity_Session_Single_Login.md
P0.02_Role_Permission_Authorization.md
...
P1.01_Planner_Intelligence.md
...
P2.01_Advanced_Security_Content_Protection.md
```

---

# 35. Specification Workflow

Setiap slice mengikuti:

```text
1. Read source documents
2. Extract requirements
3. Confirm dependencies
4. Define scope
5. Define data
6. Define API
7. Define UI
8. Define event/worker
9. Define security
10. Define tests
11. Define acceptance
12. Implement
13. Test
14. Fix
15. Acceptance
16. Lock
17. Proceed
```

---

# 36. No Hidden Scope

Selama implementation, jangan menambahkan secara diam-diam:

```text
Business Rule Baru
Product Type Baru
Billing Behavior Baru
Permission Baru
Retention Policy Baru
Tenant Rule Baru
```

tanpa keputusan formal.

---

# 37. Change Management

Jika implementasi menemukan masalah architecture:

```text
Problem
↓
Impact
↓
Architecture Review
↓
Contract / Architecture Update
↓
Implementation
↓
Test
```

Jangan meninggalkan:

```text
temporary direct DB access
temporary cross-domain write
temporary duplicate model
temporary provider call from UI
```

sebagai architecture permanen.

---

# 38. Vendor Neutrality

Walaupun initial build menggunakan:

```text
Vercel
Supabase
Cloudflare R2
```

implementation specification tetap mendefinisikan:

```text
Application Contract
```

sebelum:

```text
Vendor-specific implementation
```

Contoh:

```text
Storage Contract
→ R2 Adapter

Identity Contract
→ Supabase Auth integration

Database Boundary
→ PostgreSQL
```

Tujuannya agar migrasi ke VPS dapat dilakukan tanpa membongkar domain layer.

---

# 39. Environment Targets

Boleh mendefinisikan:

```text
Development
Staging
Production
Future VPS
```

tetapi domain tidak boleh bergantung pada vendor deployment tertentu.

Initial environment:

```text
Vercel
+
Supabase
+
R2
```

Future:

```text
VPS
+
PostgreSQL
+
R2 / optional self-hosted object storage
```

---

# 40. P0 Slice List

```text
P0.00 Architecture & Development Skeleton
P0.01 Identity, Session & Single Login
P0.02 Role, Permission & Authorization
P0.03 Configuration Core + Basic Godmode Control Plane
P0.04 Market, Localization & Currency Foundation
P0.05 Product, Pricing & Entitlement Foundation
P0.06 Order & Payment Core
P0.07 Manual Transfer + Support Payment Verification
P0.08 Storage Core & File Lifecycle
P0.09 Event, Audit & Notification Infrastructure
P0.10 Workspace & Content Slot
P0.11 Research Workspace & Core Research Data
P0.12 Research Insight & Opportunity Engine
P0.13 Planner Core
P0.14 Analyzer Default Intelligence
P0.15 Content Production Blueprint Core
P0.16 Asset Preparation Core
P0.17 Editor Foundation
P0.18 Export / Storage Integration
```

---

# 41. P1 Slice List

```text
P1.01 Planner Intelligence
P1.02 Analyzer Add-ons
P1.03 Blueprint Add-ons
P1.04 Analytics Foundation
P1.05 Analytics Intelligence & Feedback
P1.06 Support Center
P1.07 Referral & Milestones
P1.08 Finance & Operational Reconciliation
P1.09 Full Admin Godmode
```

---

# 42. P2 Slice List

```text
P2.01 Advanced Security & Content Protection
P2.02 White-label Core Verification
P2.03 Advanced Operational Hardening
P2.04 Full Platform Regression
```

---

# 43. First Slice to Specify

The first detailed implementation specification is:

> **P0.00 — Architecture & Development Skeleton**

It should define the actual implementation foundation:

```text
Repository Structure
Application Structure
Module Boundaries
Environment Variables
Database Connection
Migration Strategy
API Skeleton
Worker Skeleton
Event Abstraction
Storage Abstraction
Logging
Health Checks
Testing
Local Development
Vercel / Supabase / R2 Integration
```

After P0.00 passes its acceptance gate:

```text
P0.01 — Identity, Session & Single Login
```

is specified next.

---

# 44. Final Principle

The Implementation Specification is the bridge between:

```text
PRODUCT DEFINITION
        ↓
SYSTEM ARCHITECTURE
        ↓
IMPLEMENTATION
```

The goal is not to generate documentation for its own sake.

The goal is:

```text
Specify one slice
→ build it
→ see the UI
→ test real behavior
→ find problems
→ fix
→ pass acceptance
→ lock
→ build next slice
```

That is the implementation method for the entire 32-slice roadmap.

---

# NON-PROGRAMMER OPERATIONAL ADDENDUM

## Purpose

This section is now part of every Vertical Slice Implementation Specification.

The project owner/operator is not required to understand the underlying code in order to verify a slice.

Every slice that reaches implementation must include an **Operator Verification** section.

## Operator Verification

For the current slice, the Implementation Specification must define:

```text
1. What the operator must do
2. Which command to run, if any
3. Which URL/screen to open
4. What should be visible
5. What PASS looks like
6. What FAIL looks like
7. What screenshot/log to send when FAIL occurs
8. What must NOT be shared because it is sensitive
```

The operator should never be asked to diagnose code.

## Operator Steps

Use this format:

```text
STEP 1
Action:

Expected Result:

If PASS:
→ Continue

If FAIL:
→ STOP
→ Do not change code randomly
→ Capture error
→ Send error to developer/AI
```

## Operator Safety

Never ask the operator to paste or share:

```text
API keys
API secrets
Passwords
Database passwords
OAuth client secrets
Webhook secrets
R2 secrets
Private keys
Production credentials
```

If a credential appears in a screenshot or terminal output:

```text
Mask/redact it before sharing.
```

## Environment Verification

Every slice specification must state explicitly:

```text
Environment:
Local / Development / Staging / Production

Safe to run:
YES / NO

Production access required:
YES / NO
```

Destructive commands must include an explicit warning.

## Non-Programmer Acceptance Report

Each slice should provide:

```text
Slice:
<slice id>

Environment:
<environment>

Functional:
PASS / FAIL

UI:
PASS / FAIL / N/A

Persistence:
PASS / FAIL / N/A

Authorization:
PASS / FAIL / N/A

Worker / Event:
PASS / FAIL / N/A

Storage:
PASS / FAIL / N/A

Error:
<if any>

Screenshot:
<if needed>
```

## Operator Rule

The operator may report:

```text
"UI tidak sesuai."
"Button tidak bekerja."
"Data hilang setelah refresh."
"Error muncul."
"Saya tidak mengerti langkah ini."
```

The developer/AI is responsible for translating that report into a technical diagnosis.

The operator is **not** responsible for deciding which file, package, database table, API, or architecture layer must be changed.

## UI Verification

When a slice contains UI, verify:

```text
[ ] Page loads
[ ] Main controls are visible
[ ] Main actions work
[ ] Loading state works
[ ] Empty state works
[ ] Error state works
[ ] Light mode works
[ ] Dark mode works
[ ] Responsive layout works
[ ] Data persists after refresh where required
[ ] Logout/login does not incorrectly lose persistent state
```

## "PASS" vs "LOCKED"

A local test may be:

```text
PASS
```

while the slice itself is not yet:

```text
LOCKED
```

The slice becomes `LOCKED` only after its defined Acceptance Gate has passed and no required blocker remains.

## Required Addition to Every Slice

Every future slice specification must include:

```text
### Operator Verification
### Operator Safety
### Operator Acceptance Report
```

This is mandatory for P0, P1, and P2.
