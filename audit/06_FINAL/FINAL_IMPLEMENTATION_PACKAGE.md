# FINAL IMPLEMENTATION PACKAGE — AI BUILDER HANDOFF

## 0. EXECUTION COMMAND
Build the complete web application described by the authoritative source set in this repository. Do not build a mockup, partial prototype, static frontend, or disconnected slices. Build the shared core first, then integrate every P0 slice, then every P1 slice, and finish with end-to-end testing, security checks, billing/payment checks, persistence checks, responsive UI checks, failure/retry checks, and production deployment readiness.

Do not ask the product owner to make decisions that are already defined by the source documents. Do not invent business rules. If a behavior is genuinely absent from the sources, implement the safest neutral technical behavior and record the assumption in code/docs rather than silently changing product semantics.

The application must be delivered as one coherent system: one account/session model, one authorization model, one entitlement model, one Content Slot identity, one Research source/evidence authority, one domain owner per persistent business entity, and explicit cross-domain APIs/events.

## 1. AUTHORITATIVE SOURCE ORDER
1. `original/00_GOVERNANCE/` — immutable governance/business authority.
2. `original/01_PRODUCT/PRD_Master_Platform_Konten_AI_FINAL.md` — full product requirements and locked product behavior.
3. `original/02_ARCHITECTURE/` — architecture authority.
4. `original/03_CORE_CONTRACTS/` — domain contract authority.
5. `original/04_IMPLEMENTATION/` — implementation/slice requirements.
6. `original/05_DESIGN/` — design/UI authority.
7. `original/06_OPERATIONS/` — operational authority.
8. `audit/03_DECISIONS/source-of-truth.md` and `phase-4-final-source-of-truth-decisions-id.md` — reconciled authority and conflict resolution.
9. `audit/04_CHANGE_PLAN/working/` — corrected implementation rules.
10. `audit/05_FINAL_VERIFICATION/slices/` — slice implementation requirements.

`original/` is reference-only. Never edit it.

## 2. PRODUCT TO BUILD
Build the full platform for the initial release defined by the PRD:

- Research & Insight Center
- Content Planner & Calendar
- AI Multi-Source Analyzer
- Script & Visual Prompt / Content Production Blueprint
- Asset Preparation
- Smart Canvas Image Editor
- Smart Timeline Video Editor
- Storage & Auto-Purge
- Analytics & Performance Center
- Membership / Product / Package / Entitlement
- Order / Payment / Refund
- Manual Transfer
- Referral & Milestones
- Support Tickets
- Admin Godmode
- Shared authentication, authorization, configuration, provider pools, audit, notification, i18n, currency, tenant foundation and observability.

Initial content formats: Single Image, Carousel and Short Video 9:16. Long-form video, full Agency Mode and full White-label remain core-ready/future unless explicitly activated by the source requirements.

The platform is platform-agnostic: Planner and Analyzer do not select Instagram/TikTok/YouTube as publishing destinations. They work with content type.

## 3. CORE PRODUCT FLOW
```text
Research & Insight
      ↓
Content Planner
      ↓
Analyzer
      ↓
Script / Content Production Blueprint
      ↓
Asset Preparation / Generation
      ↓
Image Canvas OR Video Timeline Editor
      ↓
Export
      ↓
Analytics
      ↺
Research / Planner learning loop
```

`content_slot_id` is the persistent production anchor connecting Planner → Analyzer → Script → Assets → Editor → Export.

## 4. SHARED CORE — BUILD BEFORE FEATURE SLICES
Implement these as reusable platform services/modules:

- Authentication and server-side session management with single-login behavior.
- Authorization: Role + Permission + scope, server enforced.
- Membership/Product/Subscription/Entitlement separation.
- Configuration + typed keys + versioning + feature flags.
- Market / i18n / currency: minimum Indonesia + English and IDR + USD.
- Tenant/workspace foundation.
- Provider abstraction and provider pools.
- Database/repository boundaries.
- Event/outbox infrastructure.
- Idempotency and deduplication.
- Worker/job infrastructure, retries and DLQ.
- Audit trail.
- Notification delivery/read separation.
- Storage lifecycle and retention scheduler.
- Correlation IDs and structured observability.
- Error taxonomy.
- Secure secret/configuration handling.

## 5. CANONICAL DOMAIN OWNERSHIP
| Domain | Owns |
|---|---|
| Identity | User, Account, Session |
| Authorization | Role, Permission, Assignment |
| Configuration | Configuration key/schema/version/effective value/flag |
| Product | Product, Product Version, Price |
| Subscription | Subscription lifecycle |
| Entitlement | Granted/consumed/released/reversed rights |
| Order | Purchase/order lifecycle |
| Payment | Settlement/refund |
| Provider | Generic provider infrastructure |
| Storage | Storage object lifecycle |
| Event Infrastructure | Delivery/retry/DLQ |
| Audit | Audit record |
| Notification | Notification delivery/read state |
| Workspace | Operational workspace context |
| Content Context | Content Plan / Content Slot |
| Research | Research workspace, source, evidence, provenance, insight |
| Planner | Planning decisions |
| Analyzer | Analysis run and derived interpretation |
| Blueprint | Blueprint / Variant / production mapping |
| Asset Preparation | Asset preparation/generation workflow |
| Editor | Editing state |
| Export | Export job/output |
| Analytics | Derived analytics/measurement |
| Support | Support Ticket |
| Referral | Referral Commission |
| Tenant | Organizational/white-label boundary |

No persistent entity may have two authoritative owners.

## 6. NON-NEGOTIABLE BUSINESS BOUNDARIES
- Role/Permission = authorization; never commercial entitlement.
- Membership/Product/Entitlement = commercial access/benefit.
- Agency Mode = commercial mode; Agency roles provide authorization.
- Feature access requires applicable entitlement AND authorization.
- Product = sellable definition; Subscription = active commercial relationship; Entitlement = usable granted right.
- Order ≠ Payment ≠ Fulfillment.
- Payment success does not imply fulfillment success.
- Payment owns refund; Entitlement owns entitlement reversal; Referral owns commission consequence.
- Research owns canonical source/evidence/provenance; Analyzer owns derived interpretation.
- Planner owns planning decisions; Content Context owns Content Slot state.
- Workspace ≠ Tenant.
- Provider adapters never own consuming-domain business state.
- Cross-domain state changes use commands/events, never direct repository mutation.
- Configuration cannot override authorization, tenant isolation or security.
- UTC is canonical platform/system time.
- Delivery state and read state are separate.
- Privacy deletion is distinct from mandatory financial/audit retention.
- Ambiguous provider/payment results enter reconciliation; never fabricate success/failure.
- Retryable operations and event consumers are idempotent.

## 7. MEMBERSHIP / BILLING
Implement the approved model:

```text
Membership/Product Package
        ↓
Order
        ↓
Payment
        ↓
Entitlement
        ↓
Feature Usage
```

No normal-member Top Up/PAYG wallet/deposit balance.

Generate-heavy features use entitlements. Core intelligence features follow the approved membership/access model. Purchased packages remain owned when membership becomes inactive but are locked until membership is active again. New package purchases are blocked while membership is inactive.

Payment must support configured providers and Manual Transfer. Historical transactions retain their product/price/currency snapshot. Duplicate webhook processing must never duplicate payment or entitlement effects.

## 8. CONTENT PRODUCTION STATE
Canonical content state:

```text
Draft → Source Needed → Script Ready → Asset Ready → Editing → Exported
```

Do not invent a competing global content state machine.

## 9. ENTITLEMENT STATE
```text
AVAILABLE
   ↓ reserve
RESERVED
   ├─ commit → CONSUMED
   └─ release → AVAILABLE

CONSUMED
   ↓ approved reversal
REVERSED
```

Reservation/commit/release/reversal must be idempotent and auditable.

## 10. PAYMENT STATE
```text
REQUESTED
   ↓
PROVIDER_PENDING
   ├─ confirmed success → PAID
   ├─ confirmed failure → FAILED
   └─ ambiguous → RECONCILIATION_REQUIRED
```

Never treat timeout as automatic failure.

## 11. SUBSCRIPTION STATE
```text
ACTIVE → CANCELLED_PENDING_END → EXPIRED
```

Renewal/reactivation is explicit. Cancellation does not remove active benefits before the effective end date.

## 12. STORAGE
Initial approved export/editor retention: 48 hours. Auto-purge binary objects without deleting persistent project/content history.

Storage lifecycle:
```text
AVAILABLE → EXPIRING → PURGE_PENDING → PURGED
```

A purge failure is retryable and observable. Support attachment retention remains separate.

## 13. RESEARCH ENGINE
Implement the full Research & Insight Center from the original PRD, including:

- Research Overview
- Content Gap / Opportunity Engine
- Competitor Tracker
- Trend Explorer
- Keyword / Hashtag Research
- Audience Insight
- Own Content Intelligence
- Mood Board / Visual Reference
- Research Evidence + Confidence
- Research Digest
- Provider/source management
- Historical snapshots where specified
- Research → Planner / Analyzer integration

Research output must distinguish observed, estimated and AI-inferred information and expose freshness/confidence/evidence. If data is insufficient, show insufficient data; never fabricate metrics.

## 14. PLANNER
Implement the complete planner behavior from the PRD:

- planning period, timezone, active days and frequency;
- integer allocation;
- content type and pillar allocation;
- Idea Pool;
- Research Opportunity and Analytics recommendations;
- candidate generation;
- hard vs soft constraints;
- Content Slot identity;
- smart scheduling;
- anti-repetition/diversity;
- workload awareness;
- Monthly/Weekly/Grid views;
- drag/drop;
- lock/unlock;
- conflict detection;
- regenerate selected/from date;
- rebalance;
- bulk actions;
- duplicate;
- change preview;
- undo/version history;
- autosave/persistence;
- explainable recommendations;
- trend-aware scheduling.

Locked manual decisions always outrank AI recommendations.

## 15. ANALYZER
Implement the complete Analyzer behavior from the PRD:

- URL, media and raw-concept input;
- source identity/provenance;
- source classification;
- pre-analysis quality gate;
- duplicate/reuse detection;
- structured extraction;
- fact/opinion/interpretation/prediction separation;
- evidence references;
- angle generation;
- hook accuracy check;
- audience fit;
- Virality Potential Score as decision support, never a guarantee;
- anti-hallucination controls;
- Deep Source Intelligence add-on;
- Media Intelligence add-on;
- Multi-AI cross-validation add-on;
- Cross-Source Analysis add-on;
- claim/evidence/risk handling;
- Content Readiness;
- traceable handoff to Script/Blueprint.

## 16. SCRIPT / BLUEPRINT
Implement Content Production Blueprint as the production layer, not a second Analyzer.

It must inherit selected angle, opportunity, evidence, audience, objective and risk from Analyzer; preserve selected angle unless the member explicitly changes it; support image/carousel and video script structures; evidence-bind claims; generate structured visual prompts; create asset requirements; map output to Editor; run production QA; support granular regeneration; preserve versions/undo; and hand off to Asset Preparation.

## 17. ASSET PREPARATION
Support:

- automatic asset checklist from blueprint;
- AI preview generation;
- final AI generation with entitlement consumption;
- member-uploaded assets;
- mixed AI/upload workflows;
- provider routing;
- retries/DLQ;
- asset versions;
- approval/finalization;
- usage accounting;
- traceability to blueprint and content slot.

Client-side processing is preferred for lightweight upload/preview/edit operations; heavy AI generation and final rendering are server-side.

## 18. EDITORS
### Image
Build a lightweight canvas editor with templates, layers, text, images, brand kit, background-removal workflow, slide navigation and batch export.

### Video
Build a lightweight multi-track timeline editor with 9:16 short-video support, video/image b-roll, voiceover/music, subtitles/text overlays, trimming, templates and final render.

Editing must persist and reload. Media lifecycle remains Storage-owned.

## 19. EXPORT
Export must produce the approved formats:

- PNG/JPG/WEBP/PDF for image content.
- MP4/WEBM for video.
- MP3 for audio where applicable.

Export job state is separate from Storage object lifecycle.

## 20. ANALYTICS
Implement:

- performance overview;
- platform/account integrations where approved;
- manual input;
- content rankings;
- topic/pillar/format/hook/angle/CTA learning;
- own-account baseline;
- median + average where meaningful;
- A/B/experiment insight where data supports it;
- confidence/data-quality metadata;
- recommendation engine;
- recommendation outcome tracking;
- attribution back to `content_slot_id` and, when available, Content → Angle → Opportunity → Source → Evidence.

Never invent performance data.

## 21. REFERRAL
Implement approved referral attribution, 90-day attribution window, active-downline definition, 10% recurring commission, availability rules, refund/clawback behavior, milestone checkpoints, dashboard and withdrawal flow. Super Admin is fallback referrer where the business authority specifies it.

## 22. SUPPORT
Implement Billing / Technical Bug / Feature Request categories, Open → In Progress → Resolved → Closed lifecycle, priority, SLA, attachments, auto-close and Manual Transfer evidence flow as defined by the PRD.

## 23. ADMIN GODMODE
Build a real administrative control plane, not a static dashboard:

- users;
- roles/permissions/scopes;
- products/pricing/packages;
- subscriptions/entitlements;
- payment providers;
- research providers;
- AI provider pools;
- configuration/feature flags;
- market/currency;
- templates;
- analytics/scoring configuration;
- support;
- finance;
- storage;
- sessions/security;
- tenant/white-label foundation;
- audit trail;
- operational jobs/health.

Admin actions must delegate to canonical domain authorities and remain fully audited.

## 24. SECURITY
Mandatory:

- server-side authorization;
- tenant isolation;
- secure sessions;
- protected secrets;
- validation and canonical error handling;
- audit of privileged actions;
- no client-only security controls;
- content protection default ON where required;
- content protection described as deterrence, not absolute OS-level protection;
- rate limiting/abuse controls appropriate to public endpoints;
- safe file upload validation;
- provider credential isolation;
- no secret values in logs;
- security-sensitive configuration requires approved authorization path.

## 25. API / EVENT RULES
Every operation must have:

- authoritative owner;
- authentication/authorization requirement;
- tenant/scope rule where applicable;
- request/response schema;
- canonical error classification;
- idempotency behavior for retryable mutations;
- correlation/request ID;
- authoritative state result;
- event behavior where applicable.

Every event must have producer ownership, stable name/version, payload contract, consumers, retry behavior and deduplication strategy.

## 26. UI RULES
All screens must implement:

- loading;
- empty;
- success only after backend confirmation;
- validation error;
- denied;
- entitlement unavailable;
- pending/ambiguous provider state;
- retryable failure;
- terminal failure;
- destructive confirmation;
- background processing.

UI terminology must respect domain semantics. Responsive behavior is required for desktop/tablet/mobile. Accessibility includes keyboard/focus, semantic labels and readable errors.

## 27. P0/P1 BUILD ORDER
Build and integrate in this order, without leaving disconnected mock slices:

P0.00 Architecture/Development Skeleton
P0.01 Identity/Session
P0.02 Role/Permission
P0.03 Configuration
P0.04 Market/Localization/Currency
P0.05 Product/Pricing/Entitlement
P0.06 Order/Payment
P0.07 Manual Transfer
P0.08 Storage
P0.09 Events/Audit/Notification
P0.10 Workspace/Content Slot
P0.11 Research Data Foundation
P0.12 Research Insight/Opportunity
P0.13 Planner Core
P0.14 Analyzer Default
P0.15 Blueprint/Variant Core
P0.16 Asset Preparation Core
P0.17 Editor Foundation
P0.18 Export/Storage Integration

Then:

P1.01 Planner Intelligence
P1.02 Analyzer Add-ons
P1.03 Blueprint Add-ons
P1.04 Analytics Foundation
P1.05 Analytics Intelligence/Feedback
P1.06 Support Center
P1.07 Referral & Milestones
P1.08 Finance/Reconciliation
P1.09 Full Admin Godmode

## 28. DO NOT BUILD SLICES AS ISOLATED DEMOS
Before declaring any slice complete, connect it to the already-built system and verify the complete path through the real database, authentication, authorization, API, events/workers, UI and persistence.

Examples:

- Planner must create real Content Slots used by Analyzer.
- Analyzer must persist real sources/evidence/results and feed Blueprint.
- Blueprint must create real asset requirements.
- Asset Preparation must use real entitlement and real provider abstraction.
- Editor must consume real assets and persist real editing state.
- Export must create real export jobs and Storage objects.
- Payment must trigger the real fulfillment/entitlement boundary.
- Analytics must consume real performance records and feed Research/Planner.

No fake local-only state may be used where persistent server state is required.

## 29. TEST-FIRST COMPLETION GATES
A feature is not complete until its automated and integration tests pass.

At minimum test:

- authentication/session and second-login revocation;
- authorization bypass attempts;
- tenant isolation;
- product/version/price snapshot;
- entitlement reservation/commit/release/reversal;
- payment success/failure/timeout/duplicate webhook/reconciliation;
- fulfillment separation;
- manual transfer approval;
- storage retention/purge/failure;
- event duplicate/retry/DLQ/replay;
- Content Slot persistence/revision conflict/locking;
- Research provenance and insufficient-data behavior;
- Analyzer anti-hallucination/evidence binding;
- Planner hard constraints and locked slots;
- Blueprint evidence inheritance;
- asset entitlement accounting;
- editor persistence;
- export retry/idempotency;
- analytics attribution;
- referral commission/refund/clawback;
- support SLA/lifecycle;
- admin authorization/audit;
- responsive/accessibility critical flows.

## 30. PRODUCTION READINESS GATE
Before handoff/deployment:

- build succeeds from clean environment;
- migrations succeed and are reversible where required;
- seed/demo data is separated from production;
- no secrets committed;
- environment variables documented;
- health/readiness checks work;
- logs/metrics/traces work;
- background workers recover after restart;
- retry/DLQ/replay verified;
- backup and restore tested with measurable RPO/RTO;
- storage purge verified;
- payment webhook security verified;
- authorization/tenant isolation penetration checks completed;
- critical user journeys tested end-to-end;
- mobile/responsive critical journeys tested;
- error states verified;
- billing/entitlement reconciliation verified;
- export files verified;
- deployment rollback procedure verified.

## 31. FINAL HANDOFF REQUIREMENT
The implementation is considered ready for commercial launch only after the actual application passes the production readiness gate above. Documentation alone is not evidence of a working application.

When the build is complete, leave the repository in a runnable state with:
- application source;
- database migrations;
- tests;
- environment example;
- deployment configuration;
- seed/admin bootstrap mechanism;
- operational documentation;
- final test report.

## 32. SOURCE TRACE
This handoff is based on the audited source hierarchy and the reconciled decisions in:
- `original/01_PRODUCT/PRD_Master_Platform_Konten_AI_FINAL.md`
- `audit/03_DECISIONS/source-of-truth.md`
- `audit/03_DECISIONS/phase-4-final-source-of-truth-decisions-id.md`
- `audit/04_CHANGE_PLAN/working/01-final-prd-id.md`
- `audit/04_CHANGE_PLAN/working/02-final-architecture-id.md`
- `audit/04_CHANGE_PLAN/working/03-final-contracts-id.md`
- `audit/04_CHANGE_PLAN/working/04-final-domain-specifications-id.md`
- `audit/04_CHANGE_PLAN/working/05-final-lifecycle-state-id.md`
- `audit/04_CHANGE_PLAN/working/06-final-api-specifications-id.md`
- `audit/04_CHANGE_PLAN/working/07-final-ui-design-specifications-id.md`
- `audit/04_CHANGE_PLAN/working/08-final-operations-deployment-specifications-id.md`
- `audit/04_CHANGE_PLAN/working/09-build-rules-implementation-rules-id.md`
- `audit/04_CHANGE_PLAN/working/canonical-registries-id.md`
- `audit/04_CHANGE_PLAN/working/final-document-synchronization-matrix-id.md`
- `audit/05_FINAL_VERIFICATION/final-slice-specification-gate-id.md`
- `audit/05_FINAL_VERIFICATION/slices/`

## FINAL INSTRUCTION TO AI BUILDER
**START BUILDING THE COMPLETE PRODUCT. Do not stop after creating the shell. Do not stop after P0. Do not stop after P1. Do not replace backend behavior with mock data. Do not create disconnected pages. Implement, integrate, test, fix, and verify the complete system against this specification and the authoritative source files.**
