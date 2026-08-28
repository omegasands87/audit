# FINAL IMPLEMENTATION PACKAGE — AI BUILDER

## 0. PURPOSE
This is the **single build instruction** for the AI Builder.

Build the complete production web application represented by `original/`. The folder `original/` is the product source of truth. This file is the execution/control layer.

**BUILD INPUT = ONLY:**
1. `original/`
2. this file

Do **not** require, reference, or depend on any other `audit/` file or folder. Do not invent missing product requirements from outside these inputs.

`original/` is READ-ONLY. Never modify it.

## 1. SOURCE AUTHORITY
Read **all files under `original/`**, not only the PRD.

Priority when resolving a conflict:

1. `original/00_GOVERNANCE/` — locked business/governance decisions.
2. `original/01_PRODUCT/` — product requirements, flows, features and UX intent.
3. `original/02_ARCHITECTURE/` — system architecture and boundaries.
4. `original/03_CORE_CONTRACTS/` — domain ownership and contracts.
5. `original/04_IMPLEMENTATION/` — implementation requirements and slices.
6. `original/05_DESIGN/` — visual/UI authority.
7. `original/06_OPERATIONS/` — deployment, operations, security and recovery authority.

If two statements conflict, follow the higher authority and preserve the lower-level requirement where it does not conflict. Never silently create a new business rule.

If a genuinely required technical detail is absent, choose the safest conventional implementation, document the assumption in the project, and do not change business semantics.

## 2. EXECUTION COMMAND
Build the application **all the way to a coherent production-ready implementation**.

Do NOT deliver:

- a mockup;
- a static frontend;
- fake API responses presented as real functionality;
- disconnected demo pages;
- placeholder buttons for required functionality;
- hard-coded business data;
- isolated feature slices that cannot use the shared system;
- a frontend-only prototype;
- a partial build declared complete.

The final system must have real persistence, real API/service boundaries, real authentication, real authorization, real domain logic, real asynchronous processing where required, real error handling, and real end-to-end integration.

## 3. PRODUCT TO BUILD
Implement the complete initial-release product described by `original/`, including its complete user experience and all required supporting systems.

Core product flow:

```text
Research & Insight
      ↓
Content Planner
      ↓
Analyzer
      ↓
Content Production Blueprint
      ↓
Asset Preparation
      ↓
Image Canvas / Video Timeline
      ↓
Export
      ↓
Analytics
      ↺
Research / Planner learning loop
```

`content_slot_id` is the persistent production anchor wherever the source requirements use it.

Do not replace the original product concept with a generic SaaS dashboard.

## 4. UI/UX PRESERVATION
The implementation must preserve the product concept, information architecture, terminology, workflows and visual intent contained in `original/05_DESIGN/` and the product requirements.

For every required screen:

- implement the real route;
- implement the real data source;
- implement the real user actions;
- implement loading state;
- implement empty state;
- implement validation state;
- implement success state only after backend confirmation;
- implement permission-denied state;
- implement entitlement-unavailable state where applicable;
- implement pending/background-processing state;
- implement retryable failure;
- implement terminal failure;
- implement destructive confirmation;
- implement responsive desktop/tablet/mobile behavior;
- implement accessible keyboard/focus/semantic behavior.

Do not simplify away important workflows merely to make the build faster.

## 5. SHARED CORE FIRST
Before feature modules, establish one shared platform foundation:

- authentication;
- server-side session management;
- single-login behavior if required by source;
- authorization with Role + Permission + scope;
- membership/product/subscription/entitlement separation;
- tenant/workspace foundation;
- configuration and feature flags;
- market, i18n and currency;
- database/repository boundaries;
- provider abstraction and provider pools;
- API/error framework;
- event/outbox infrastructure;
- idempotency/deduplication;
- workers/jobs/retries/DLQ;
- audit trail;
- notification delivery/read state;
- storage lifecycle;
- observability/correlation IDs;
- secure secrets/configuration;
- validation and rate/abuse controls.

All feature modules must consume this shared core. Do not create duplicate implementations of authentication, authorization, entitlement, storage or event infrastructure.

## 6. CANONICAL DOMAIN OWNERSHIP
Use one authoritative owner for every persistent business entity.

| Domain | Authority |
|---|---|
| Identity | User, Account, Session |
| Authorization | Role, Permission, Assignment |
| Configuration | configuration schemas, versions, flags and effective values |
| Product | Product, Product Version, Price |
| Subscription | Subscription lifecycle |
| Entitlement | granted/consumed/released/reversed rights |
| Order | order/purchase lifecycle |
| Payment | settlement/refund |
| Provider | generic provider infrastructure |
| Storage | stored-object lifecycle |
| Event Infrastructure | delivery/retry/DLQ |
| Audit | audit records |
| Notification | delivery/read state |
| Workspace | operational workspace context |
| Content Context | Content Plan / Content Slot |
| Research | research sources, evidence, provenance, insight |
| Planner | planning decisions |
| Analyzer | analysis runs and derived interpretation |
| Blueprint | blueprint/variant/production mapping |
| Asset Preparation | preparation/generation workflow |
| Editor | editing state |
| Export | export jobs/output |
| Analytics | derived measurement/analytics |
| Support | support tickets |
| Referral | referral commission |
| Tenant | organizational boundary |

No feature may create a second authoritative copy of another domain's business state.

## 7. NON-NEGOTIABLE BUSINESS BOUNDARIES
Implement the following boundaries wherever applicable in `original/`:

- Role/Permission is authorization, not commercial entitlement.
- Membership/Product/Subscription/Entitlement remain distinct concepts.
- Agency Mode is not a replacement for authorization roles.
- Feature access requires applicable authorization and applicable commercial access.
- Order, Payment and Fulfillment are separate concerns.
- Payment success does not automatically mean fulfillment success.
- Payment owns refund; Entitlement owns entitlement reversal; Referral owns commission consequences.
- Research owns canonical source/evidence/provenance; Analyzer owns derived interpretation.
- Planner owns planning decisions; Content Context owns Content Slot state.
- Workspace and Tenant are distinct.
- Provider adapters do not own consuming-domain business state.
- Cross-domain state changes use defined commands/events/service boundaries, not direct repository mutation across domains.
- Configuration cannot bypass authorization, tenant isolation or security.
- System timestamps use the canonical time convention defined by `original/`.
- Delivery state and read state remain separate.
- Privacy deletion is distinct from legally/operationally required retention.
- Ambiguous provider/payment results enter reconciliation; never fabricate success/failure.
- Retryable mutations and event consumers are idempotent.

## 8. BILLING / ENTITLEMENT
Implement the complete commercial flow defined by the source:

```text
Product / Package
      ↓
Order
      ↓
Payment
      ↓
Fulfillment / Entitlement
      ↓
Feature Usage
```

Implement the exact membership, product, pricing, subscription, entitlement, package, refund and manual-transfer behavior defined by `original/`.

Do not introduce a generic wallet/top-up/PAYG model unless the source explicitly requires it.

Historical financial records must preserve the required product/price/currency snapshot.

Duplicate payment/webhook processing must not duplicate financial or entitlement effects.

## 9. RESEARCH
Implement the complete Research & Insight Center defined by the source, including every required module, source/evidence model, provenance, freshness, confidence, historical behavior, provider integration, opportunity/insight behavior and downstream handoff.

Observed, estimated and AI-inferred information must remain distinguishable wherever required.

Never fabricate metrics, competitor data, trends, audience data or source evidence.

## 10. PLANNER
Implement the complete Planner defined by the source, including all required planning periods, constraints, allocation, Idea Pool, recommendations, Content Slots, scheduling, diversity/anti-repetition, workload awareness, calendar views, drag/drop, lock/unlock, conflict detection, regeneration, rebalance, bulk actions, duplicate, preview, undo/versioning and persistence.

Manual locked decisions outrank AI recommendations where required by the source.

## 11. ANALYZER
Implement the complete Analyzer defined by the source, including all supported input types, source identity, provenance, quality gates, extraction, evidence, classification, angles, hooks, audience fit, scoring, anti-hallucination controls, add-ons, cross-source behavior and traceable handoff to Blueprint.

Scores are decision support, never guaranteed outcomes.

## 12. CONTENT PRODUCTION BLUEPRINT
Blueprint is the production layer after Analyzer, not a duplicate Analyzer.

It must preserve traceability to the selected opportunity/angle/evidence/audience/objective/risk, support all source-defined content formats, scripts, visual prompts, asset requirements, production QA, granular regeneration, versions/undo and Editor handoff.

## 13. ASSET PREPARATION
Implement source-defined AI generation, previews, uploads, mixed workflows, provider routing, entitlement consumption, retries, asset versions, approval/finalization, usage accounting and traceability.

Lightweight browser-side operations may remain client-side; heavy AI generation/rendering belongs server-side.

## 14. IMAGE + VIDEO EDITORS
Implement the actual editor capabilities specified in `original/05_DESIGN/` and implementation requirements.

Image editor must support the required canvas/layers/text/image/brand/template/slide/export behavior.

Video editor must support the required timeline/tracks/media/audio/text/subtitle/template/render behavior.

Editing state must persist and reload correctly.

## 15. EXPORT + STORAGE
Implement real export jobs and real storage integration.

Export-job state must remain distinct from storage-object lifecycle.

Implement the exact formats, retention, purge, retry, recovery and historical-project behavior defined by the source.

Do not delete persistent project/content history merely because binary assets expire.

## 16. ANALYTICS
Implement the complete analytics system defined by the source, including integrations/manual input where required, rankings, content/topic/pillar/format/hook/angle/CTA learning, baseline calculations, confidence/data-quality handling, recommendations, recommendation outcomes and traceability back to production/content records.

Never generate fake performance data.

## 17. REFERRAL / SUPPORT / ADMIN
Implement all source-defined functionality for:

- referral attribution and commission;
- milestones;
- refund/clawback effects;
- withdrawal;
- support tickets, SLA and attachments;
- finance/reconciliation;
- Admin Godmode;
- users and authorization;
- products/pricing;
- subscriptions/entitlements;
- providers;
- configuration;
- templates;
- analytics configuration;
- storage;
- security/session controls;
- tenant/white-label foundation where required;
- operational health;
- audit trail.

Administrative screens must perform real operations through canonical domain services.

## 18. API / EVENT CONTRACT RULE
Every API operation must have, as applicable:

- authenticated actor;
- authorization requirement;
- tenant/scope rule;
- input validation;
- request/response contract;
- authoritative owner;
- canonical state result;
- error classification;
- idempotency behavior;
- correlation/request ID;
- event side effects.

Every event must have:

- stable name/version;
- producer;
- payload contract;
- consumers;
- retry behavior;
- deduplication behavior;
- failure/DLQ behavior where applicable.

## 19. STATE / LIFECYCLE RULE
Do not invent competing global state machines.

Use the lifecycle definitions in `original/` and ensure every state transition has the required guard, actor/authority, command/event, side effects, persistence, idempotency, failure handling and recovery behavior.

Where the source defines lifecycle states, implement them exactly.

## 20. BUILD ORDER
Build in dependency order, not page order.

### Phase A — Shared Core
Architecture → database → identity/session → authorization → configuration → market/i18n/currency → product/subscription/entitlement → provider infrastructure → API/error framework → events/jobs → audit/notification → storage → observability.

### Phase B — Core Product Chain
Workspace/Content Slot → Research Foundation → Research Insights → Planner → Analyzer → Blueprint → Asset Preparation → Editors → Export.

### Phase C — Intelligence / Commercial / Operations
Planner intelligence → Analyzer add-ons → Blueprint add-ons → Analytics → Analytics feedback → Support → Referral → Finance/Reconciliation → Admin.

If `original/04_IMPLEMENTATION/` defines a more specific dependency order, follow that order.

## 21. VERTICAL SLICE INTEGRATION RULE
A feature is not complete when its page renders.

A slice is complete only when:

```text
UI
 ↓
API
 ↓
Authorization
 ↓
Domain Service
 ↓
Persistence
 ↓
Events / Jobs where required
 ↓
Provider / External integration where required
 ↓
Real result
 ↓
UI state update
```

The next dependent slice must be able to consume the real persisted result.

Never use fake data to simulate integration that is required by the product.

## 22. NO PLACEHOLDER COMPLETION
The following are NOT acceptable completion states:

- `TODO` for required functionality;
- disabled required buttons;
- fake success responses;
- mocked database data in production paths;
- static JSON standing in for required backend behavior;
- unconnected UI;
- “coming soon” for an in-scope requirement;
- empty admin controls for required operations;
- hard-coded permissions;
- hard-coded entitlement decisions;
- fake analytics;
- fake provider/payment success;
- silently omitted requirements.

If a real external credential/provider is unavailable during development, implement the production adapter boundary and a controlled test/mock environment without pretending that the external integration is production-verified.

## 23. SECURITY / PRIVACY
Implement the security and operational requirements in `original/06_OPERATIONS/` and relevant governance/contracts.

At minimum where applicable:

- server-side authorization;
- tenant isolation;
- secure sessions;
- secret isolation;
- secure file validation;
- input validation;
- rate limiting/abuse controls;
- audit of privileged operations;
- no secrets in logs;
- provider credential isolation;
- safe error responses;
- privacy/deletion behavior;
- financial/audit retention;
- backup/restore;
- observability;
- incident/recovery procedures.

## 24. TESTING
Create and execute tests appropriate to the implementation:

- unit tests for domain rules;
- integration tests for APIs/services;
- persistence/database tests;
- authorization tests;
- entitlement/billing tests;
- webhook idempotency tests;
- event/retry/DLQ tests;
- lifecycle transition tests;
- provider failure/reconciliation tests;
- storage purge/recovery tests;
- UI component/state tests;
- end-to-end user journeys;
- responsive UI verification;
- security checks;
- regression tests.

Critical business flows must pass end-to-end before release.

## 25. FINAL END-TO-END ACCEPTANCE
Before declaring the application complete, verify at minimum:

1. A new user can register/login and maintain a valid session.
2. Authorization prevents unauthorized access server-side.
3. Membership/product/subscription/entitlement behavior matches the source.
4. A real Content Slot can travel through the complete production chain.
5. Research data persists and remains traceable.
6. Planner persists real plans and Content Slots.
7. Analyzer consumes real inputs and persists real results.
8. Blueprint consumes Analyzer output and produces real production requirements.
9. Asset Preparation uses real entitlement/provider boundaries.
10. Editors load, modify and persist real project state.
11. Export creates real jobs and outputs.
12. Storage retention/purge works without destroying required history.
13. Analytics uses real records and never fabricates metrics.
14. Payment/refund/manual-transfer/reconciliation paths behave correctly.
15. Referral consequences follow financial outcomes correctly.
16. Support and Admin perform real backend operations.
17. Failure/retry/ambiguous-provider states are handled correctly.
18. Audit/security/observability requirements work.
19. Responsive and accessibility requirements are met.
20. No required feature remains a placeholder or disconnected mock.

## 26. DEFINITION OF DONE
The application may be called **COMPLETE** only when:

- every in-scope requirement in `original/` has an implementation location;
- every required user journey is functional end-to-end;
- all required persistence is real;
- all required domain boundaries are enforced;
- all required API/event contracts are implemented;
- all required UI states exist;
- all required tests have been executed;
- critical tests pass;
- no known blocker remains;
- production configuration/deployment requirements are satisfied;
- build/lint/type checks pass;
- migrations are reproducible;
- backup/restore and recovery procedures are validated where required;
- security-sensitive behavior has been tested;
- the final application matches the product concept in `original/`.

Do not declare completion merely because the frontend looks finished.

## 27. FINAL AI BUILDER BEHAVIOR
Work autonomously through the full build.

Do not repeatedly stop after each slice asking whether to continue.

Do not ask the product owner to re-decide requirements already defined in `original/`.

When a requirement is clear, implement it.

When a dependency is clear, build it first.

When an implementation fails, diagnose and fix it rather than moving on with a broken dependency.

When a requirement is genuinely contradictory or technically impossible, identify the exact conflict, preserve the original source, and report the smallest required decision/blocker. Do not invent a business decision.

**The objective is one complete, integrated, production-quality application — not a collection of demonstrations.**
