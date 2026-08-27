# Consistency & Completeness Audit Report

## Status
**AUDIT IN PROGRESS — FINDINGS ONLY**

`original/` is the immutable baseline. No corrective edits are being applied to `original/` during this audit.

This report records both:

- **CONFLICT** = existing rules/statements contradict each other.
- **GAP** = the documentation does not define enough information for implementation without inference.

A finding is **not a final decision** until it is verified and reviewed with the project owner.

## Phase 1 — Inventory & Authority

Repository baseline contains 21 Markdown documents under `original/`, grouped into Governance, Product, Architecture, Core Contracts #1–#13, Implementation, Design, and Operations.

Working authority hierarchy from the Engineering Constitution:

```text
Final Business Decision Register
        ↓
Final PRD
        ↓
Core Contract
        ↓
Core Architecture
        ↓
Implementation Specification
        ↓
Code / Current Implementation
```

## Phase 2 — Deep Audit Findings

### CONFLICT-001 — Manual Transfer payment state
**Severity:** 🔴 Critical

Earlier wording uses `Payment Approved`; later wording explicitly defines `Admin Approve Ticket → Payment = Paid` and says no separate payment approval is required.

**Required resolution:** canonical flow should be:

```text
Order → Manual Transfer → Proof via Support Ticket
→ Admin Approve Ticket → Payment = Paid → Entitlement Granted
```

**Affected:** Business Decision Register, Payment, Support, Implementation.

### CONFLICT-002 — Role vs Membership/Product configuration
**Severity:** 🔴 Critical

Core Contract #2 contains role-configuration examples associated with membership/product-like benefits, while Contract #4 and the Business Decision Register explicitly separate Role/Permission from Membership/Product/Entitlement.

**Risk:** role assignment could accidentally grant commercial entitlement.

**Required resolution:** Role owns authorization; Product/Membership/Entitlement owns commercial capability. Role defaults must not grant entitlement.

**Affected:** Contract #2, Contract #4, PRD, Architecture.

### CONFLICT-003 — Content Slot ownership vs Planner mutation authority
**Severity:** 🟠 High

Content Slot is the stable production anchor owned by the Content Context boundary, while Planner is described as creating/updating slots.

**Required resolution:** separate entity ownership from command authority:

```text
Content Context → owns Content Slot identity/lifecycle/context
Planner → owns planning decisions and uses the approved Content Slot contract
```

Planner must not create a competing slot entity.

**Affected:** Architecture, Contracts #9/#11/#12/#13.

### CONFLICT-004 — Research canonical evidence vs Analyzer source/evidence concepts
**Severity:** 🔴 Critical

Research is the canonical source/evidence layer, while Analyzer contains source/evidence/claim concepts that could be implemented as a second canonical model. Contract #12 requires Analyzer to use Research core entities.

**Required resolution:**

```text
Research → canonical Source / Evidence / Research truth
Analyzer → Analysis Run / Interpretation / Derived Output
```

**Affected:** PRD, Architecture, Contracts #10/#12.

### CONFLICT-005 — Provider infrastructure vs domain-specific adapter boundary
**Severity:** 🟠 High

Provider Contract defines shared Provider Service/Pool/Adapter infrastructure, while Payment and other domains retain domain-specific provider behavior.

**Required resolution:**

```text
Provider Infrastructure → registry, credentials, routing, health, retry/failover
Consuming Domain → business operation, canonical business state, domain semantics
```

**Affected:** Architecture, Contract #6, Payment, Operations.

### CONFLICT-006 — Configuration scope vs business ownership
**Severity:** 🟠 High

Configuration supports broad scopes including Product, Membership, Role, User, Tenant and Workspace, while those domains own business/security semantics.

**Required resolution:** Configuration owns values/schema/scope/version/effective value; consuming domains own meaning, validation, enforcement and lifecycle.

**Affected:** Contract #3, Contract #2, Contract #4, Architecture.

### CONFLICT-007 — Workspace vs Research Workspace identity
**Severity:** 🟡 Medium

The documents state these are distinct concepts, but flows use both as working context.

**Required resolution:** preserve separate entities and explicitly define their relationship/reference.

**Affected:** PRD, Architecture, Contracts #9/#10.

### CONFLICT-008 — Agency settlement balance vs normal member billing
**Severity:** 🟡 Medium

Normal members do not use Top Up/PAYG Wallet/Deposit Balance, while Agency/White-label uses a wholesale settlement balance.

**Required resolution:** Agency settlement must be explicitly scoped and must not become a generic member wallet.

**Affected:** Business Decision Register, Product/Entitlement, Payment, Architecture.

### CONFLICT-009 — Engine grouping vs bounded-domain ownership
**Severity:** 🟡 Medium

PRD groups functionality into Engines; Architecture/Core Contracts define bounded domains.

**Required resolution:** Engine = product/feature grouping or orchestration concept; Domain/Bounded Context = authoritative ownership boundary.

**Affected:** PRD, Architecture, Vertical Slice Order.

### CONFLICT-010 — Architecture dependency vs build dependency
**Severity:** 🟡 Medium

Architecture diagrams can be read as runtime dependency chains while implementation order also represents delivery prerequisites.

**Required resolution:** distinguish runtime dependency, build dependency, data reference and event subscription.

**Affected:** Architecture, Vertical Slice Order, Roadmap.

### CONFLICT-011 — P0 Manual Transfer depends on Support, but full Support is scheduled in P1
**Severity:** 🔴 Critical

Implementation Roadmap places P0.07 Manual Transfer with Support Ticket Reference, Proof Attachment and Admin Approval, but the complete Support Center is scheduled later at Slice 24 / P1.06.

**Risk:** P0 cannot satisfy its own Manual Transfer acceptance gate without an explicitly defined minimal Support capability in P0.

**Required resolution:** introduce a clearly scoped P0 Support Payment Verification capability before/inside P0.07, or move Manual Transfer out of P0. Final choice must be recorded as an implementation decision.

**Affected:** Final Vertical Slice Order, Implementation Roadmap, Payment, Support requirements.

### CONFLICT-012 — Agency Mode is described as a Role while commercial access is governed by Product/Membership/Entitlement
**Severity:** 🔴 Critical

PRD UI/roadmap language describes Agency Mode as a `role` with separate pricing/mechanism, while the final business model explicitly separates Role (permission) from Membership/Product/Entitlement (commercial capability).

**Risk:** AI Builder may implement purchase of Agency Mode as role assignment or infer entitlement from role.

**Required resolution:** decide whether Agency Mode is a Product/Membership/Entitlement capability, a Role, or two explicitly separate concepts. The term `role` must not carry commercial meaning.

**Affected:** PRD, Business Decision Register, Contracts #2/#4, Architecture.

### CONFLICT-013 — Analyzer add-on entitlement can be included by Role
**Severity:** 🔴 Critical

PRD states Deep Source Intelligence and Multi-AI can be provided through Product/Package or included in membership/role via Admin, while the same business model separates Role from commercial entitlement.

**Risk:** role configuration becomes an entitlement grant mechanism.

**Required resolution:** role-specific defaults may be configuration/preferences; commercial inclusion must originate from Product/Membership/Entitlement.

**Affected:** PRD Analyzer sections, Contracts #2/#3/#4.

### CONFLICT-014 — Content Slot field ownership may be over-expanded by Planner
**Severity:** 🟠 High

Planner's Content Slot field list includes downstream references such as `script_id`, `asset_project_id`, and `editor_project_id`, while Contract #9/Architecture treat Content Slot as the stable context anchor and downstream domains as separate owners.

**Risk:** Content Slot becomes a God Entity that owns downstream production state.

**Required resolution:** distinguish stable cross-domain references from owned state. Downstream IDs should be references/links, not imply ownership by Content Context/Planner.

**Affected:** Contracts #9/#11, Architecture, Blueprint/Asset/Editor implementation.

### CONFLICT-015 — PRD summary still couples entitlement access to Role
**Severity:** 🔴 Critical

The PRD's final comparison table states that core feature access follows `membership/role`, even though the same PRD and Business Decision Register establish Role as permission/access control and Membership/Product/Entitlement as commercial capability.

**Risk:** this wording can cause an AI implementation to treat Role as a commercial entitlement source.

**Required resolution:** rewrite the summary terminology so entitlement/capability access is determined by Entitlement, while Role determines authorization to perform the operation.

**Affected:** PRD final summary/decision table, Contract #2/#4, Architecture.

### CONFLICT-016 — Final Vertical Slice Order status is internally ambiguous
**Severity:** 🟡 Medium

The document is titled `Final Vertical Slice Order`, but its status is `Final Build Sequencing Draft`.

**Risk:** AI Builder may not know whether this is locked sequencing or still provisional.

**Required resolution:** explicitly choose `FINAL` or `DRAFT` and define whether the sequence can change without a formal change decision.

**Affected:** Final Vertical Slice Order, Roadmap, Constitution/authority metadata.

## Completeness Gaps

### GAP-001 — Canonical Capability Registry
**Severity:** 🟠 High

Need one authoritative registry for capability code, owner, input/output contract, entitlement unit, provider requirements, preview/final semantics, status and version.

### GAP-002 — Canonical Permission Registry
**Severity:** 🟠 High

Need authoritative permission catalog defining permission code, resource, action, scope, system/core/custom classification and protected permissions.

### GAP-003 — Canonical Configuration Key Registry
**Severity:** 🟠 High

Need registry defining valid keys, schema, owner, allowed scope, defaults and precedence.

### GAP-004 — Canonical State Machine Index
**Severity:** 🟠 High

Need an index covering entity, owner, states, transitions, trigger, actor, side effects and terminal states.

### GAP-005 — Canonical Event Catalog
**Severity:** 🟠 High

Need event name, owner, payload, version, producer, consumers and retry/idempotency semantics in one catalog.

### GAP-006 — Canonical API Contract Registry
**Severity:** 🟠 High

Need operation, owner, authorization, request/response schema, errors, idempotency and versioning in one registry.

### GAP-007 — Canonical Entity Ownership Registry
**Severity:** 🟠 High

Ownership is currently inferred from individual contracts. Need one authoritative matrix covering all entities.

### GAP-008 — Completed Vertical Slice Specifications
**Severity:** 🟠 High

The implementation document is a framework/template, while the roadmap expects concrete slice specifications. The repository currently does not contain the individual per-slice specification set described by the framework.

### GAP-009 — Research Historical Observation Priority Mapping
**Severity:** 🟡 Medium

Deferred historical observation capabilities need explicit mapping to implementation phase/slice.

### GAP-010 — Configuration Precedence vs Security Boundary
**Severity:** 🟠 High

Need separate definitions for configuration precedence, tenant isolation and authorization scope. Configuration overrides must never bypass security boundaries.

### GAP-011 — Planner / Content Context command boundary
**Severity:** 🟡 Medium

Need explicit command-level contract for Content Slot operations, including ownership and authorization.

### GAP-012 — Entitlement Consumption Failure/Reversal Matrix
**Severity:** 🟡 Medium

Need explicit outcomes for success, failure, retry, timeout, provider failover, cancellation and reversal.

### GAP-013 — Subscription Entity and Lifecycle Owner
**Severity:** 🔴 Critical

Business rules repeatedly depend on subscription state (`Active`, `Cancelled`, `Expired`, `Reactivated`, etc.), but the current Product/Entitlement and Payment contracts do not define a canonical Subscription entity/owner or complete subscription state machine.

**Risk:** multiple domains may independently infer subscription status.

**Required addition:** define authoritative Subscription ownership, lifecycle, renewal/cancellation/reactivation behavior, billing-cycle relation, and events.

**Affected:** Business Decision Register, Contract #4, Contract #5, Architecture, Implementation.

### GAP-014 — Missing dedicated core contracts for required domains
**Severity:** 🔴 Critical

The repository has Core Contracts #1–#13, but implementation/architecture require additional domains without equivalent dedicated contracts, including at least:

```text
Support
Referral / Milestones
Analytics
Asset Preparation
Editor
Export
Tenant / White-label
Security / Content Protection
```

Some behavior exists in PRD/Architecture/Implementation, but no single domain contract establishes owner, entities, state, API, events, invariants and DoD for each.

**Required addition:** create dedicated contracts or explicitly declare and fully specify ownership in existing contracts.

### GAP-015 — Security / Content Protection source document missing
**Severity:** 🔴 Critical

PRD explicitly references a separate `Security & Content Protection` document for technical details, but it is not present in the current `original/` inventory.

**Risk:** screenshot/DevTools/Auto-Blur/content-protection implementation lacks a complete technical source of truth.

**Required addition:** create the missing specification or formally relocate the requirements into an authoritative existing document.

### GAP-016 — Market / Localization contract depth
**Severity:** 🟡 Medium

Market, language and currency are used by Identity, Configuration, Product/Pricing and UI, but there is no dedicated contract defining canonical Market entity, locale registry, fallback resolution, currency registry and interaction rules.

### GAP-017 — Subscription/package allocation schedule
**Severity:** 🟠 High

Contract #4 says membership allocations refresh according to membership cycle/policy, including annual subscriptions with monthly allocations, but does not fully define allocation calendar, proration, renewal boundary, unused-balance behavior or timezone rules.

### GAP-018 — Product purchase eligibility decision matrix
**Severity:** 🟠 High

Purchasability combines Product Active, Price Active, Market Allowed, Eligibility Allowed and Payment Method Available, while subscription-inactive rules add constraints. A canonical decision matrix is needed.

### GAP-019 — Refund ↔ Entitlement reversal semantics
**Severity:** 🟠 High

Payment supplies refund trigger and Entitlement supplies grant/consumption state, but exact reversal policy for granted, partially consumed, refunded and failed entitlements is not fully centralized.

### GAP-020 — Provider failure ↔ entitlement consumption transaction model
**Severity:** 🟠 High

Provider execution and entitlement consumption are separate transitions. Idempotency is required, but reservation/commit/release semantics for timeout, ambiguous success, partial completion and failover are not fully specified.

### GAP-021 — Event catalog completeness and aggregate keys
**Severity:** 🟡 Medium

Events are defined in multiple contracts, but no single catalog establishes canonical aggregate/partition keys, producer ownership, version compatibility and consumer guarantees for all critical events.

### GAP-022 — API error/code registry
**Severity:** 🟡 Medium

Contracts define local error examples, but there is no canonical error taxonomy/code registry shared across API, UI, workers and support diagnostics.

### GAP-023 — Data deletion / privacy lifecycle
**Severity:** 🟠 High

Storage purge rules exist, but a complete user/account data lifecycle is not clearly defined for account deletion, anonymization, dependent records, audit retention, financial records, research data and provider traces.

### GAP-024 — Backup/restore and disaster-recovery acceptance criteria
**Severity:** 🟡 Medium

Operations requires backup, recovery and restore testing, but concrete RPO/RTO targets, restore verification procedure and acceptance criteria are not clearly defined.

### GAP-025 — Observability standard across domains
**Severity:** 🟡 Medium

Logging/correlation requirements exist, but no canonical observability matrix defines mandatory metrics, traces, alerts, dashboards and ownership for each critical domain/worker.

### GAP-026 — Per-slice dependency matrix reconciliation
**Severity:** 🟠 High

Vertical Slice Order and Implementation Roadmap use related slice groupings and should be reconciled in one canonical mapping, even where the numbering appears aligned in the current documents.

### GAP-027 — Asset / Editor / Export state ownership and handoff
**Severity:** 🔴 Critical

Implementation requires Asset Preparation → Editor → Export, but these domains lack dedicated core contracts defining authoritative entities, state machines, API/application boundaries, events, idempotency and failure recovery.

### GAP-028 — Research Source vs Analyzer raw concept/media persistence rule
**Severity:** 🟡 Medium

Analyzer accepts raw concepts, media and documents while Research defines canonical Source identity. Need an explicit rule for when each input becomes a persistent Research Source versus remaining Analyzer-only input.

### GAP-029 — Own Content Intelligence / Analytics ownership boundary
**Severity:** 🟠 High

PRD expects Own Winners, historical baseline and A/B learnings to feed Research, while Architecture says Analytics owns performance ingestion/calculation. Canonical ownership and handoff of derived signals need explicit definition.

### GAP-030 — White-label foundation contract and activation boundary
**Severity:** 🟠 High

White-label is core-ready but not fully built. Tenant-aware authorization, pricing, product synchronization, domain mapping and settlement behavior need one explicit contract/boundary.

### GAP-031 — Security-sensitive configuration approval workflow
**Severity:** 🟡 Medium

Configuration identifies High/Critical settings and suggests confirmation/step-up/approval, but does not define canonical approval state machine, actor separation or enforcement mechanism.

### GAP-032 — Timezone/clock authority across scheduling, billing and retention
**Severity:** 🟡 Medium

Planner defines plan timezone; Storage uses retention timestamps; subscription/billing uses cycle dates. A platform-wide rule for authoritative timestamps, user timezone, market timezone, billing timezone and DST handling is not fully centralized.

### GAP-033 — Entitlement `remaining_amount` source of truth
**Severity:** 🟡 Medium

Contract #4 describes `remaining_amount` as potentially calculated/stored while also maintaining `granted_amount` and `consumed_amount`.

**Risk:** multiple writable representations can drift.

**Required addition:** define whether remaining is derived (`granted - committed consumption + adjustments`) or a ledger projection with strict reconciliation rules.

### GAP-034 — Order fulfillment failure state machine
**Severity:** 🟠 High

Order has `PAID` and `FULFILLED`, but the complete behavior when payment is paid and entitlement fulfillment fails, times out, or is partially completed is not centralized.

**Required addition:** define fulfillment state, retry/reconciliation behavior, idempotency and operational recovery.

### GAP-035 — Refund state transitions after fulfillment
**Severity:** 🟡 Medium

Payment defines refund states and Entitlement defines reversal separately, but the complete cross-domain transition from fulfilled purchase → refund → entitlement reversal/adjustment → referral adjustment is not represented as one explicit workflow.

### GAP-036 — Notification delivery state vs read state
**Severity:** 🟡 Medium

Notification Contract includes `READ` alongside delivery states such as `PENDING`, `SENT`, and `FAILED`. Read/unread is a recipient interaction state, not the same dimension as delivery.

**Required addition:** separate delivery lifecycle from recipient read state.

### GAP-037 — Provider capability vs capability registry ownership
**Severity:** 🟡 Medium

Contract #6 defines `ProviderCapability` and Contract #4 defines product capability references, but there is no canonical capability vocabulary connecting Provider Capability, Product Capability, Entitlement Capability and execution Capability.

### GAP-038 — Source identity for raw concepts
**Severity:** 🟡 Medium

Analyzer lists `Raw Concept` as a source class while Research Source Identity is designed around external/persistent source identity. Need explicit identity semantics for internally authored concepts.

## Audit Method Note

These findings are a working inventory. Before any correction, each finding must be verified against all affected source documents and discussed with the project owner. Findings may be merged, split, downgraded, rejected or converted into formal decisions.

## Rules Going Forward

1. Continue auditing all source files before corrective changes.
2. Every finding must be classified as `CONFLICT` or `GAP`.
3. Record severity and affected documents.
4. Add evidence/reference before marking a finding `VERIFIED`.
5. Do not modify `original/`.
6. Do not silently resolve unresolved business decisions.
7. After the full audit, reconcile Source of Truth.
8. Only then execute the Change Plan.
9. After corrections, run a second full cross-document audit.

## Phase Status

```text
Phase 1 — Inventory & Authority          COMPLETE
Phase 2 — Deep Cross-Document Audit      IN PROGRESS
Phase 3 — Full Completeness Audit        IN PROGRESS
Phase 4 — Source-of-Truth Reconciliation PENDING
Phase 5 — Controlled Corrections         PENDING
Phase 6 — Final Verification             PENDING
```
