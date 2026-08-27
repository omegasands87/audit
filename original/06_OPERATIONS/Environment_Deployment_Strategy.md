# Environment & Deployment Strategy

## Status

**Final Development/Deployment Strategy Baseline**

This document defines the deployment and environment strategy for the platform from initial development through future VPS migration.

It complements:

```text
PRD Final
Business Decision Register
Core Contracts #1–#13
Core Architecture V1
Final Vertical Slice Order
Implementation Roadmap P0/P1/P2
Implementation Specification per Vertical Slice
```

The primary principle is:

> **Development infrastructure may change without changing the domain/business architecture.**

Initial stack:

```text
Vercel
+
Supabase
+
Cloudflare R2
```

Future production may move application infrastructure to:

```text
VPS
+
PostgreSQL
+
R2
```

or another compatible infrastructure arrangement.

---

# 1. Strategy Objectives

The environment strategy must support:

```text
Local Development
        ↓
Development Environment
        ↓
Staging Environment
        ↓
Production Environment
        ↓
Future VPS Migration
```

Goals:

- fast vertical-slice development;
- safe testing;
- isolated environments;
- controlled secrets;
- predictable database migrations;
- repeatable deployment;
- provider abstraction;
- easy rollback;
- easy VPS migration;
- no vendor lock-in at the domain layer.

---

# 2. Initial Infrastructure

The initial implementation uses:

| Layer | Initial Platform |
|---|---|
| Frontend / Web | Vercel |
| Application/API | Vercel application/runtime |
| Database | Supabase PostgreSQL |
| Authentication | Supabase Auth integration |
| Object Storage | Cloudflare R2 |
| Database/Admin Tooling | Supabase |
| Background Worker | Separate worker runtime, initially deployable independently |
| External Providers | Provider adapters |

The exact worker hosting technology is intentionally not hard-coded into this document.

---

# 3. Target Architecture

Logical architecture:

```text
                         Internet
                            │
                            ▼
                       Vercel / CDN
                            │
                            ▼
                     Web / Application
                            │
                   ┌────────┴────────┐
                   ▼                 ▼
              Application API     Auth Boundary
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
   PostgreSQL    Storage     Workers
   (Supabase)      │           │
                   ▼           ▼
                  R2      External Providers
```

The business/domain modules remain independent from the infrastructure vendors.

---

# 4. Environment Model

The platform should maintain at least four logical environments:

```text
LOCAL
DEVELOPMENT
STAGING
PRODUCTION
```

A future VPS deployment is treated as a **production infrastructure target**, not necessarily as a new application environment.

---

# 5. LOCAL Environment

Purpose:

```text
Developer workstation
```

Used for:

```text
coding
unit tests
integration tests
local debugging
schema development
```

Characteristics:

- lowest cost;
- disposable;
- safe for test data;
- no production credentials;
- mock external providers by default.

Recommended:

```text
Local PostgreSQL-compatible database
Local environment variables
Mock Provider Adapters
Local object-storage emulator or dedicated development bucket
```

---

# 6. DEVELOPMENT Environment

Purpose:

```text
Shared development / integration
```

Characteristics:

- persistent enough for team testing;
- connected to development Supabase project;
- connected to development R2 bucket;
- uses test provider credentials;
- may contain synthetic/test user data.

No production data should be copied into Development without an explicit sanitized-data process.

---

# 7. STAGING Environment

Purpose:

```text
Pre-production validation
```

Staging should reproduce production behavior as closely as practical.

Recommended:

```text
Vercel
+
Supabase Staging Project
+
R2 Staging Bucket
+
Production-like Worker
+
Sandbox/Test Providers
```

Staging is where:

```text
P0 slice acceptance
P1 integration tests
release candidate validation
database migration validation
```

should occur.

---

# 8. PRODUCTION Environment

Initial production:

```text
Vercel
+
Supabase Production
+
R2 Production
+
Production Workers
+
Real Payment Providers
+
Real AI/Research Providers
```

Production secrets must be isolated from Development/Staging.

---

# 9. Future VPS Production

The future target may be:

```text
                  VPS
        ┌──────────┼──────────┐
        ▼          ▼          ▼
      Web/API    Workers   PostgreSQL
                           │
                           ▼
                         R2
```

Possible deployment:

```text
Reverse Proxy
Application
Worker
PostgreSQL
Monitoring
Backup Agent
```

R2 may remain external.

This is recommended because object storage is independently scalable and does not need to move simply because application hosting moves.

---

# 10. Migration Principle

The migration path should be:

```text
Infrastructure Migration
≠
Business Logic Rewrite
```

The following should remain stable:

```text
Core Contracts
Domain Logic
Business Rules
API Contracts
Data Ownership
Event Contracts
Storage Contract
Provider Contract
Entitlement Logic
```

The following may change:

```text
Hosting
Runtime
Database deployment
Auth deployment
Reverse Proxy
DNS
SSL
CI/CD
Environment Variables
Monitoring
```

---

# 11. Vendor-Neutral Architecture Rule

Domain code should not directly depend on:

```text
Vercel SDK
Supabase SDK
R2 SDK
```

for business rules.

Instead:

```text
Domain Contract
        ↓
Infrastructure Adapter
        ↓
Vendor
```

Examples:

```text
Storage Contract
→ R2 Adapter

Identity Contract
→ Supabase Auth Integration

Database Boundary
→ PostgreSQL Adapter/Repository

Payment Contract
→ Xendit / Duitku / NOWPayments Adapter
```

---

# 12. Database Strategy

The logical database target is:

```text
PostgreSQL
```

Supabase is the initial managed PostgreSQL environment.

The application should not assume:

```text
Supabase-specific database behavior
```

unless that capability has been explicitly approved as an infrastructure feature.

---

# 13. Database Ownership

The database should use logical module boundaries even if a single physical PostgreSQL instance is used.

Possible logical schemas/modules:

```text
identity
authorization
configuration
commerce
provider
storage
audit
notification
workspace
content
research
planner
analyzer
blueprint
support
referral
analytics
```

The exact schema strategy will be decided during implementation, but ownership boundaries must remain strict.

---

# 14. Migration Strategy

Database changes must use migrations.

Never rely on:

```text
manual production schema edits
```

Recommended lifecycle:

```text
Create Migration
→ Local Test
→ Development
→ Staging
→ Production
```

Every migration should be:

```text
reviewed
repeatable
versioned
rollback-aware
```

---

# 15. Migration Safety

For high-risk schema changes:

```text
Expand
→ Migrate Data
→ Verify
→ Switch Read/Write
→ Contract
```

Avoid destructive migrations in the same deployment as application changes whenever practical.

---

# 16. Backup Strategy

Production database must have:

```text
automated backup
point-in-time recovery where available
backup verification
restore test
```

The exact provider feature is infrastructure-specific.

The application architecture must assume that:

> **Backup exists but is not a substitute for application-level audit/history.**

---

# 17. Object Storage Strategy

R2 is the canonical initial object-storage target.

Logical boundary:

```text
Storage Service
        ↓
R2 Adapter
        ↓
Cloudflare R2
```

Business domains store:

```text
storage_object_id
```

not provider-specific bucket logic.

---

# 18. R2 Bucket Strategy

Use separate logical buckets/paths for:

```text
development
staging
production
```

Do not mix environments in one bucket unless there is a compelling operational reason.

Suggested logical prefixes:

```text
dev/
staging/
prod/
```

---

# 19. Storage Security

Applications should use:

```text
signed upload
signed download
short-lived access
server-side authorization
```

Never expose permanent object-store credentials to the browser.

---

# 20. Storage Retention

The application must persist:

```text
retention_expires_at
```

or equivalent lifecycle metadata.

Initial business policies:

```text
Editor / Content Export
→ 48 hours

Support Attachments
→ 90 days after Ticket Closed
```

Storage lifecycle workers enforce these policies.

---

# 21. Storage Migration to VPS

Moving the application to VPS does not require moving R2.

Preferred:

```text
VPS
→ R2
```

Only move storage when there is a real business/operational reason.

If self-hosted object storage is later required:

```text
Storage Contract
→ MinIO/S3-compatible Adapter
```

without changing domain logic.

---

# 22. Authentication Strategy

Initial:

```text
Supabase Auth
```

Application boundary:

```text
Identity Contract
        ↓
Auth Adapter
        ↓
Supabase Auth
```

The rest of the application should depend on:

```text
user_id
session_id
authentication result
```

rather than directly coupling every domain to Supabase Auth APIs.

---

# 23. Authentication Migration to VPS

There are two future options.

### Option A

Keep a managed authentication provider.

### Option B

Move authentication to a self-hosted/auth service.

In either case, the goal is:

```text
Identity Contract
→ Auth Implementation
```

without rewriting all domains.

Session and authorization semantics must remain consistent.

---

# 24. Authorization Strategy

Authorization is application/domain logic.

Supabase RLS may provide an additional database protection layer, but:

> **RLS is not the only authorization boundary.**

The primary business authorization decision remains in the application Authorization layer.

---

# 25. Environment Secrets

Each environment has separate secrets.

Examples:

```text
DATABASE_URL
AUTH_SECRET
R2_ACCESS_KEY
R2_SECRET
R2_ENDPOINT
PAYMENT_PROVIDER_KEYS
AI_PROVIDER_KEYS
RESEARCH_PROVIDER_KEYS
EMAIL_PROVIDER_KEYS
WEBHOOK_SECRETS
```

Never reuse production secrets in Development.

---

# 26. Secret Management

Secrets should be supplied through environment-specific secret management.

Initial:

```text
Vercel Environment Variables
Supabase Secret Configuration / Edge Function Secrets
R2 credentials
```

Future VPS:

```text
Docker/Runtime Secrets
Secret Manager
Encrypted Environment Configuration
```

The exact mechanism is deployment-specific.

---

# 27. Environment Variable Rule

Code should access:

```text
typed configuration
```

rather than reading arbitrary environment variables throughout the codebase.

Recommended:

```text
Environment
→ Config Loader
→ Validated Configuration Object
→ Application
```

This prevents missing/invalid environment values from becoming runtime surprises.

---

# 28. Configuration Hierarchy

Distinguish:

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
→ Environment

support.auto_close_days
→ Business Configuration
```

The two must not be mixed.

---

# 29. CI/CD Strategy

Recommended pipeline:

```text
Commit
 ↓
Lint
 ↓
Type Check
 ↓
Unit Tests
 ↓
Build
 ↓
Integration Tests
 ↓
Deploy Development
 ↓
Smoke Test
```

For staging:

```text
Promotion
 ↓
Migration
 ↓
Deploy
 ↓
E2E
 ↓
Acceptance
```

Production:

```text
Approved Release
 ↓
Backup / Migration Preparation
 ↓
Deploy
 ↓
Smoke Test
 ↓
Monitor
```

---

# 30. Branch Strategy

The exact Git strategy can remain team-dependent.

The minimum required rule:

```text
No direct unreviewed production deployment
```

For a small team:

```text
main
feature/*
```

may be sufficient.

For a larger team:

```text
main
develop
feature/*
release/*
```

may be used.

---

# 31. Deployment Artifacts

Every deployment should produce traceable:

```text
commit SHA
build version
migration version
environment
deployment time
```

This allows:

```text
Which code is running?
Which schema is running?
```

to be answered immediately.

---

# 32. Vercel Deployment

Initial web deployment:

```text
Git
→ Vercel Build
→ Preview
→ Production
```

Recommended usage:

```text
Preview Deployment
→ feature validation

Staging/Production
→ controlled deployment
```

Do not allow preview environments to accidentally point to production payment/storage/data.

---

# 33. Worker Deployment

Workers should deploy independently from the web interface where necessary.

Examples:

```text
Research Worker
Analyzer Worker
Asset Generation Worker
Storage Purge Worker
Notification Worker
Analytics Worker
Webhook Worker
```

The initial worker runtime can be chosen based on workload.

The interface remains:

```text
Job / Event Contract
```

---

# 34. Serverless vs Worker Rule

Use request/response runtime for:

```text
short API operations
validation
reads
small writes
```

Use workers for:

```text
long AI generation
research synchronization
video generation
rendering
large processing
purges
batch analytics
```

Long-running work should not depend on an open browser request.

---

# 35. Queue Strategy

The architecture requires an asynchronous job abstraction.

Initial implementation may use:

```text
managed queue
database-backed jobs
platform queue
```

depending on the chosen infrastructure.

The domain should depend on:

```text
Job Contract
```

not a specific queue product.

---

# 36. Event Processing Strategy

Use:

```text
Transactional Outbox
```

for important domain events.

Flow:

```text
Domain Transaction
→ State Change
→ Outbox Record
→ Dispatcher
→ Event Bus
→ Consumer
```

This prevents a successful database transaction from losing its event.

---

# 37. Webhook Strategy

External webhooks:

```text
Provider
→ Webhook Endpoint
→ Validate Signature
→ Persist Raw Event
→ Idempotency Check
→ Domain Command/Event
```

Do not perform complex business processing directly in the webhook request if it can be asynchronous.

---

# 38. Payment Webhook Environment Isolation

Use separate webhook credentials/endpoints for:

```text
Development
Staging
Production
```

Never let a sandbox payment webhook trigger production entitlement.

---

# 39. AI / Research Provider Strategy

Providers should be configured per environment.

Development:

```text
Mock Provider
or
Sandbox Provider
```

Staging:

```text
Controlled Real/Sandbox Provider
```

Production:

```text
Production Provider
```

Provider selection remains inside Provider Infrastructure.

---

# 40. Payment Environment Strategy

Development:

```text
Mock Payment
```

Staging:

```text
Sandbox / Test Gateway
```

Production:

```text
Real Gateway
```

The Order/Payment domain should not need to know which environment it is running in.

---

# 41. Data Seeding

Each environment may have deterministic seed data.

Development examples:

```text
Admin
Support
Test Member
Test Product
Test Entitlement
Test Workspace
Demo Research
Demo Content Slot
```

Production should never be seeded with fake commercial transactions.

---

# 42. Production Data Safety

Rules:

```text
No fake payment
No test entitlement
No synthetic referral commission
No destructive development seed
```

in Production.

---

# 43. Test Data Strategy

Test data should be tagged:

```text
test
demo
synthetic
```

where possible.

Avoid confusing synthetic analytics/research data with actual user data.

---

# 44. Logging Strategy

Structured logs should include:

```text
timestamp
level
module
operation
request_id
correlation_id
user_id
workspace_id
tenant_id
status
duration
error_code
```

Sensitive information is redacted.

---

# 45. Monitoring

Monitor at minimum:

```text
API latency
API errors
Database health
Worker failures
Queue depth
Provider latency
Provider errors
Payment failures
Webhook failures
Storage failures
Purge failures
Notification failures
```

---

# 46. Alerting

Critical alerts:

```text
Payment webhook failure
Payment reconciliation issue
Entitlement grant failure
Provider outage
Queue backlog
Database failure
Storage failure
Audit/event pipeline failure
```

Alerts should have:

```text
severity
timestamp
affected environment
correlation/reference
```

---

# 47. Rollback Strategy

Application rollback:

```text
Previous Build
```

Database rollback:

> Use migration-safe rollback/forward-fix strategy.

Never blindly restore an old application against a newer incompatible schema.

---

# 48. Expand / Contract Deployment

For schema-breaking changes:

```text
Phase 1:
Add new field/table

Phase 2:
Deploy code supporting old + new

Phase 3:
Migrate data

Phase 4:
Switch reads/writes

Phase 5:
Remove old structure
```

This minimizes deployment downtime and rollback risk.

---

# 49. VPS Migration Plan

When the platform is production-ready:

```text
Step 1
Prepare VPS

Step 2
Deploy application

Step 3
Deploy workers

Step 4
Deploy PostgreSQL / connect managed PostgreSQL

Step 5
Connect R2

Step 6
Restore / migrate database

Step 7
Run migrations

Step 8
Seed required production configuration

Step 9
Run smoke tests

Step 10
Switch DNS / traffic

Step 11
Monitor

Step 12
Decommission old runtime after stability period
```

The exact migration order may be adjusted based on downtime tolerance.

---

# 50. VPS Application Layout

A practical future layout may be:

```text
VPS
├── Reverse Proxy
├── Web / Frontend
├── API
├── Worker
├── Scheduler
├── PostgreSQL
└── Monitoring
```

R2 remains:

```text
External Object Storage
```

---

# 51. VPS Reverse Proxy

Future:

```text
Internet
 ↓
Nginx / Caddy / equivalent
 ↓
Web / API
```

Responsibilities:

```text
TLS
routing
headers
compression
rate limiting where appropriate
```

---

# 52. VPS Process Isolation

Even on one VPS, keep:

```text
Web
API
Worker
Scheduler
Database
```

logically isolated.

Do not combine everything into one uncontrolled process.

---

# 53. Database on VPS vs Managed PostgreSQL

Two valid future options:

### Option A

```text
VPS Application
+
Managed PostgreSQL
```

### Option B

```text
VPS Application
+
PostgreSQL on VPS
```

For reliability, managed PostgreSQL may remain preferable if operational simplicity is more important than full self-hosting.

The architecture should support both.

---

# 54. R2 on VPS Migration

No migration required if:

```text
VPS
→ R2
```

continues.

Only credentials/endpoints change if necessary.

---

# 55. DNS Migration

Recommended sequence:

```text
Lower DNS TTL
↓
Deploy VPS
↓
Validate
↓
Switch DNS
↓
Monitor
```

The application domain should remain stable from the user's perspective.

---

# 56. SSL

Initial:

```text
Vercel managed HTTPS
```

Future VPS:

```text
Reverse Proxy
+
Let's Encrypt / managed certificate
```

TLS renewal must be automated.

---

# 57. Zero / Low Downtime Goal

Migration should target:

```text
minimal downtime
```

Use:

```text
database migration
traffic switch
health checks
rollback window
```

The exact downtime target is an operational decision and is not fixed by the PRD.

---

# 58. Deployment Health Checks

Every environment should expose health checks.

Minimum:

```text
/health
```

and where useful:

```text
/readiness
/liveness
```

Readiness should verify critical dependencies without exposing secrets.

---

# 59. Dependency Health

Health/readiness should be able to identify:

```text
Database
Queue
Storage
Required provider infrastructure
```

without making every external provider mandatory for application startup.

---

# 60. Provider Outage Behavior

A provider outage should not cause the entire application to become unavailable.

Example:

```text
Image Provider Down
→ Image Generation unavailable

Research still works
Planner still works
Workspace still works
```

This is a key benefit of domain/provider separation.

---

# 61. Environment Promotion Rule

Promotion:

```text
Development
→ Staging
→ Production
```

is controlled.

Do not copy environment databases blindly.

Promote:

```text
Code
Migration
Configuration
Validated Artifacts
```

not:

```text
development state
```

---

# 62. Production Configuration

Business configuration is persisted in the application Configuration Domain.

Infrastructure secrets remain environment-managed.

Do not store:

```text
API secrets
database passwords
private keys
```

inside ordinary business configuration records.

---

# 63. Cost Control

During early development:

```text
Mock Providers
Sandbox Gateways
Development Buckets
Low-cost environments
```

should be used where possible.

AI generation and video generation should require real entitlement/cost checks even in test flows when the purpose is to validate commercial logic.

---

# 64. Development Safety for AI Providers

Avoid accidentally running:

```text
expensive video generation
large batch generation
large research synchronization
```

during local development.

Use:

```text
Mock
Dry Run
Preview
Small Sample
```

where applicable.

---

# 65. Environment Naming

Recommended:

```text
local
dev
staging
prod
```

Avoid ambiguous names such as:

```text
test2
new-prod
backup-prod
temporary
```

---

# 66. Deployment Documentation

Every production release should record:

```text
version
commit SHA
migration version
environment
deploy time
operator/system
rollback target
```

---

# 67. Disaster Recovery

The future production environment must define:

```text
RPO
RTO
Database backup
Storage backup
Secrets recovery
DNS recovery
Deployment recovery
```

Exact numerical targets are an operational/business decision and are not fixed by the current PRD.

---

# 68. Environment Responsibility Matrix

| Component | Local | Dev | Staging | Production | Future VPS |
|---|---|---|---|---|---|
| Web | Local | Vercel | Vercel | Vercel initially | VPS |
| API | Local | Vercel/runtime | Vercel/runtime | Vercel initially | VPS |
| Worker | Local | Separate worker | Separate worker | Worker | VPS worker |
| PostgreSQL | Local | Supabase | Supabase | Supabase | PostgreSQL managed/self-hosted |
| Auth | Local/test | Supabase | Supabase | Supabase | Managed/self-hosted option |
| Storage | Local/dev | R2 dev | R2 staging | R2 prod | R2 / optional self-host |
| Payment | Mock | Sandbox | Sandbox | Live | Live |
| AI Provider | Mock | Controlled | Sandbox/controlled | Live | Live |
| Research Provider | Mock | Controlled | Controlled | Live | Live |
| Monitoring | Local logs | Basic | Full | Full | Full |

---

# 69. Migration-Safe Development Rules

From the first implementation slice:

```text
1. PostgreSQL is the logical DB target.

2. Storage goes through Storage Contract.

3. Authentication goes through Identity Boundary.

4. Payment goes through Payment Provider Adapter.

5. Research goes through Research Provider Adapter.

6. AI generation goes through Provider Infrastructure.

7. Business logic must not live inside Vercel-only runtime behavior.

8. Domain logic must not depend on Supabase-only APIs.

9. File lifecycle must not depend on R2-specific fields.

10. Configuration must separate environment values from business configuration.
```

---

# 70. What Should Not Change During VPS Migration

Ideally:

```text
Core Contracts
Domain Models
Business Rules
Entitlement Logic
Order Logic
Payment Domain
Research Domain
Planner
Analyzer
Blueprint
Content Slot
API Semantics
Event Semantics
Storage Contract
Provider Contracts
```

---

# 71. What May Change During VPS Migration

Expected:

```text
Hosting
Runtime
Reverse Proxy
Process Supervisor
Database Hosting
Auth Hosting
Secret Management
CI/CD
DNS
TLS
Monitoring
Autoscaling
Worker Deployment
```

---

# 72. Migration Acceptance Gate

Before switching production traffic:

```text
[ ] Database migrated
[ ] Schema version verified
[ ] R2 accessible
[ ] Auth works
[ ] API works
[ ] Worker works
[ ] Payment webhook works
[ ] Provider calls work
[ ] Storage upload/download works
[ ] Event processing works
[ ] Audit works
[ ] Health checks PASS
[ ] Smoke tests PASS
[ ] Rollback tested
```

---

# 73. Final Environment Architecture

```text
                   DEVELOPMENT
                       │
                       ▼
                  STAGING
                       │
                       ▼
                 PRODUCTION
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
          Vercel                 R2
             │
             ▼
         Supabase
             │
             ▼
          Workers
             │
             ▼
        Providers


Future:

                  PRODUCTION VPS
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
        Web/API      Worker      PostgreSQL

                       │
                       ▼
                      R2
```

---

# 74. Final Principle

The environment strategy must preserve this architectural property:

> **Infrastructure is replaceable; domain logic is durable.**

The initial stack:

```text
Vercel
+
Supabase
+
R2
```

is therefore a practical starting environment, not a permanent architectural dependency.

When the platform is ready to migrate:

```text
Vercel
→ VPS

Supabase deployment
→ PostgreSQL deployment

R2
→ remains R2 unless intentionally replaced
```

without rewriting:

```text
PRD
Core Contracts
Domain Logic
Business Rules
Vertical Slice structure
```

The implementation process should therefore begin with this strategy already in place, so every P0 slice is built in a way that remains deployable both on the initial stack and on the future VPS architecture.

---

# NON-PROGRAMMER ENVIRONMENT OPERATIONS ADDENDUM

## Purpose

The Environment & Deployment Strategy must also be usable by a project owner who is not a system administrator.

The project owner should be able to distinguish:

```text
What I need to click
What I need to copy
What I need to run
What I must never share
What the developer/AI must handle
```

## Environment Ownership

### Project Owner / Operator

May normally:

```text
Open dashboards
Check deployment status
Check service status
Run approved local commands
Copy non-sensitive IDs
Check logs
Open the application
Perform acceptance testing
Report errors
```

Should not normally:

```text
Change production infrastructure
Edit production database manually
Rotate production secrets without instruction
Delete production storage
Run destructive migrations
Change DNS blindly
```

### Developer / Engineering

Owns:

```text
Code deployment
Database migrations
Infrastructure configuration
Runtime configuration
Worker deployment
Provider adapters
Technical diagnosis
Rollback
```

## Environment Labels

Every operational instruction must clearly identify:

```text
LOCAL
DEVELOPMENT
STAGING
PRODUCTION
```

The operator must never be expected to infer which environment is active.

## Secret Handling

Never ask the operator to place production secrets into chat.

Use:

```text
Vercel Environment Variables
Supabase Secret Configuration
Secret Manager
Server-side environment
```

as appropriate.

If an instruction needs a credential, state:

```text
Credential type
Where to obtain it
Where to enter it
Whether it is safe to share
```

## Safe vs Destructive Commands

Every command provided to a non-programmer operator should be classified:

```text
🟢 Safe
Can be run as instructed.

🟡 Controlled
Run only in the specified environment.

🔴 Destructive
Requires explicit confirmation and should normally be handled by engineering.
```

Examples:

```text
pnpm dev:web
→ 🟢 Safe in Local/Development

pnpm db:migrate
→ 🟡 Controlled; environment must be confirmed

DROP DATABASE
→ 🔴 Destructive; do not delegate casually
```

## Deployment Verification

After a deployment, operator checks:

```text
[ ] Deployment completed
[ ] Website opens
[ ] Main page loads
[ ] No visible fatal error
[ ] Required smoke test passes
```

Technical health checks remain engineering responsibility.

## VPS Migration

For future VPS migration, the project owner should receive a separate runbook.

The operator should not be expected to manually perform:

```text
database dump/restore
reverse-proxy configuration
TLS setup
system service configuration
secret migration
worker orchestration
```

unless a dedicated runbook provides step-by-step instructions.

## Production Changes

Any Production change should identify:

```text
Purpose
Environment
Risk
Backup requirement
Expected result
Rollback strategy
Operator action
Engineering action
```

## Environment Incident Reporting

Use:

```text
Environment:
Development / Staging / Production

Service:
Vercel / Supabase / R2 / API / Worker / Provider

Action:
<what was executed>

Expected:
<what should happen>

Actual:
<what happened>

Screenshot:
<if needed>
```

## Documentation Rule

Every future deployment-related Implementation Specification must include:

```text
Environment
Required Credentials
Operator Steps
Engineering Steps
Verification
Rollback / Recovery
```

This addendum is now part of the Environment & Deployment Strategy.
