# Phase 4 — Pass 1 Decision Matrix (DRAFT)

## Status

**DRAFT — PENDING OWNER DECISION**

This document converts the Phase 4 Pass 1 analysis into a traceable decision matrix. It is a working document only. It does not establish final Source-of-Truth decisions and does not authorize corrective edits to `original/`.

## Rules

- `original/` remains immutable.
- No row is treated as an approved decision.
- Recommendations are analysis, not project-owner decisions.
- Existing findings are preserved; reinforcement relationships are not counted as separate independent problems.
- A candidate authority is a hypothesis to verify, not a final authority designation.
- If evidence is insufficient, the matrix explicitly says so rather than inferring a decision.

## Candidate authority hierarchy currently recorded in the audit

1. Final Business Decision Register
2. PRD Final
3. Core Contracts
4. Core Architecture
5. Implementation

This hierarchy is itself subject to Phase 4 verification/reconciliation.

## Decision Matrix

| ID | Finding | Evidence / audit basis | Candidate Source-of-Truth | Conflict / Gap | Recommendation | Decision Required | Status |
|---|---|---|---|---|---|---|---|
| PH3-001 | Canonical Capability Registry | Completeness finding; reinforces capability vocabulary/role-entitlement findings | New canonical registry or explicitly designated Core Contract | No single authoritative registry | Establish one canonical capability registry | Approve authority, fields and location | DRAFT |
| PH3-002 | Canonical Permission Registry | Completeness finding; reinforces role/permission separation | New canonical registry or Core Contract | Permission catalog not centralized | Establish canonical permission registry | Approve authority, fields and location | DRAFT |
| PH3-003 | Canonical Configuration Key Registry | Completeness finding; reinforces configuration findings | New canonical registry; Architecture + Operations may define technical boundary | Keys/scope/precedence not centralized | Establish registry with owner, schema, scope, default and precedence | Approve authority and precedence model | DRAFT |
| PH3-004 | Canonical State Machine Index | Reinforces LIFECYCLE-001..007 | Architecture / canonical lifecycle registry | State machines distributed across contracts | Establish canonical state-machine index | Approve owning document and required fields | DRAFT |
| PH3-005 | Canonical Event Catalog | Reinforces event findings | Core Architecture / canonical event registry | Event ownership/payload/version/retry semantics not centralized | Establish canonical event catalog | Approve authority and event ownership model | DRAFT |
| PH3-006 | Canonical API Contract Registry | Reinforces API ownership findings | Core Contracts / canonical API registry | Operations/errors/idempotency/versioning distributed | Establish canonical API contract registry | Approve authority and versioning rules | DRAFT |
| PH3-007 | Canonical Entity Ownership Registry | Reinforces cross-contract findings | Core Architecture / entity ownership registry | Ownership inferred from individual contracts | Establish one authoritative ownership matrix | Approve authority and conflict-resolution rule | DRAFT |
| PH3-008 | Concrete per-slice specifications | Existing completeness finding | Implementation specification layer under approved product/architecture authority | Framework exists but concrete slices incomplete | Require concrete slice specs before implementation | Approve required slice-spec fields and gate | DRAFT |
| PH3-009 | Subscription Entity + Lifecycle Contract | TERM-001 + LIFECYCLE-001 | Core Contract + canonical lifecycle authority | Subscription concept/lifecycle not sufficiently canonicalized | Define one canonical subscription entity and lifecycle | Approve terminology, owner and lifecycle authority | DRAFT |
| PH3-010 | Support Contract / explicit minimal P0 Support ownership | Cross-contract/support sequencing finding | Product/Operations/Core Contract depending business decision | P0 support boundary not fully consolidated | Define explicit P0 support ownership and escalation | Approve P0 scope and owner | DRAFT |
| PH3-011 | Referral/Milestones Contract | Completeness finding | Product/Business Decision + Core Contract | Business rules not consolidated into one contract | Define authoritative referral/milestone contract | Approve owner and contract scope | DRAFT |
| PH3-012 | Analytics Contract | Reinforces analytics ownership finding | Core Architecture/Product contract | Analytics ownership and interface incomplete | Define canonical analytics contract and owner | Approve ownership and contract boundary | DRAFT |
| PH3-013 | Asset Preparation Contract | Production contract coverage finding | Core Contract / Architecture | Asset preparation boundary incomplete | Define canonical asset-preparation contract | Approve owner and lifecycle boundary | DRAFT |
| PH3-014 | Editor Contract | Production contract coverage finding | Core Contract / Architecture | Editor contract not fully explicit | Define canonical editor contract | Approve owner, inputs/outputs and lifecycle | DRAFT |
| PH3-015 | Export Contract | Production contract coverage finding | Core Contract / Architecture | Export contract not fully explicit | Define canonical export contract | Approve owner and output semantics | DRAFT |
| PH3-016 | Tenant/White-label Contract | White-label boundary finding | Business Decision + Core Contract | Tenant/white-label semantics not fully consolidated | Define canonical tenant/white-label contract | Approve activation and ownership boundary | DRAFT |
| PH3-017 | Security/Content Protection Contract | Missing dedicated source finding | Security authority / Core Contract | Security/content-protection rules lack one authoritative source | Create explicit security/content-protection authority | Approve authority, scope and controls | DRAFT |
| PH3-018 | Market/Localization/Currency Contract | Completeness finding | Product/Business Decision + Core Contract | Market/localization/currency rules not centralized | Define canonical market/localization/currency contract | Approve scope and authority | DRAFT |
| PH3-019 | Subscription/package allocation schedule | Product/lifecycle finding | Business Decision / PRD Final | Allocation timing and rules not canonical | Define allocation schedule and lifecycle semantics | Approve allocation timing/rules | DRAFT |
| PH3-020 | Product purchase eligibility matrix | Eligibility finding | Business Decision / PRD Final | Eligibility rules not centralized | Establish canonical purchase eligibility matrix | Approve eligibility authority | DRAFT |
| PH3-021 | Refund ↔ Entitlement reversal policy | Refund workflow finding | Business Decision + Core Contract | Refund and entitlement reversal not fully consolidated | Define canonical reversal policy | Approve reversal timing and ownership | DRAFT |
| PH3-022 | Provider failure ↔ entitlement reservation/commit/release model | Payment/entitlement lifecycle finding | Core Architecture/Core Contracts | Provider failure semantics across domains incomplete | Define reservation/commit/release model | Approve failure and compensation semantics | DRAFT |
| PH3-023 | Event aggregate/partition key catalog | Event contract finding | Architecture/event registry | Partition/aggregate semantics not centralized | Define canonical key catalog | Approve key authority and invariants | DRAFT |
| PH3-024 | API error/code registry | API completeness finding | Core API contract registry | Error semantics not centralized | Establish canonical API error/code registry | Approve code ownership/versioning | DRAFT |
| PH3-025 | Account/data deletion & privacy lifecycle | Privacy lifecycle finding | Business Decision + Security/Operations authority | Deletion lifecycle not fully canonical | Define deletion/privacy lifecycle | Approve retention, deletion and ownership rules | DRAFT |
| PH3-026 | Backup/restore & disaster recovery acceptance criteria | OPS-010; Operations audit | Operations | RPO/RTO and acceptance criteria not fully decided | Define measurable DR acceptance criteria | Approve RPO/RTO and restore-test requirements | DRAFT |
| PH3-027 | Cross-domain observability matrix | OPS-009; UI/Operations findings | Operations | Signals, thresholds, owners and actions not centralized | Establish observability matrix | Approve severity/threshold ownership | DRAFT |
| PH3-028 | Research Source vs raw concept/media persistence rule | Research/Analyzer boundary finding | Core Architecture/Core Contract | Canonical research evidence vs raw inputs unclear | Explicitly separate canonical evidence from derived/raw inputs | Approve persistence and authority boundary | DRAFT |
| PH3-029 | Own Content Intelligence / Analytics ownership boundary | Analytics ownership finding | Product + Architecture | Ownership between intelligence and analytics incomplete | Define ownership and data boundary | Approve owner and interface | DRAFT |
| PH3-030 | White-label activation boundary | Tenant/white-label finding | Business Decision + Core Contract | Activation authority not fully defined | Define activation boundary and authority | Approve who/what activates white-label | DRAFT |
| PH3-031 | Security-sensitive configuration approval workflow | Security/configuration finding | Security + Operations | Configuration can affect security but approval flow incomplete | Define approval, audit and rollback workflow | Approve protected keys and approval authority | DRAFT |
| PH3-032 | Platform-wide time/clock authority | Completeness finding | Core Architecture | Time authority not centralized | Define canonical platform clock/timezone rules | Approve time source and timezone semantics | DRAFT |
| PH3-033 | Entitlement remaining_amount source-of-truth rule | Entitlement finding | Core Contract / canonical entitlement authority | Remaining amount authority not explicit | Define single authoritative value and mutation rules | Approve source and reconciliation rule | DRAFT |
| PH3-034 | Order fulfillment failure/recovery state machine | LIFECYCLE-003 + Operations | Architecture + Operations | Failure/recovery transitions not consolidated | Define canonical fulfillment failure/recovery machine | Approve states, owners and recovery actions | DRAFT |
| PH3-035 | Refund-after-fulfillment workflow | Refund/fulfillment finding | Business Decision + Core Contract | Post-fulfillment refund semantics incomplete | Define canonical workflow and reversal semantics | Approve business rule and ownership | DRAFT |
| PH3-036 | Notification delivery vs read-state separation | UI-015 / event finding | Product/UI + Core Contract | Delivery and read-state responsibilities not fully separated | Define two distinct states/contracts | Approve state ownership and API semantics | DRAFT |
| PH3-037 | Provider/Product/Entitlement capability vocabulary | TERM-006 / CC-008 | Product/Business Decision + Core Contracts | Capability vocabulary crosses commercial/provider boundaries | Establish canonical vocabulary and ownership | Approve definitions and mapping rules | DRAFT |
| PH3-038 | Subscription lifecycle state machine | LIFECYCLE-001 | Core Architecture/Core Contract | No single canonical subscription state machine | Establish canonical machine and reference it everywhere | Approve states/transitions/authority | DRAFT |
| PH3-039 | Entitlement reservation/commit/release/reversal state model | LIFECYCLE-002 | Core Architecture/Core Contract | Consumption lifecycle incomplete | Establish canonical entitlement state model | Approve transition authority and compensation rules | DRAFT |
| PH3-040 | Order/Payment/Fulfillment cross-domain transition matrix | LIFECYCLE-003 / CC-005 | Core Architecture + Core Contracts | Cross-domain transitions incomplete | Establish authoritative transition matrix | Approve domain ownership and legal transitions | DRAFT |
| PH3-041 | Production pipeline cross-domain state matrix | LIFECYCLE-004 / UI-007/UI-008 | Core Architecture + Core Contracts | Production states and ownership distributed | Establish canonical production state matrix | Approve lifecycle authority and transitions | DRAFT |
| PH3-042 | Event retry/DLQ/replay resolution matrix | LIFECYCLE-005 / OPS-006 | Operations + Architecture | Retry/DLQ/replay lifecycle incomplete | Establish operational event recovery matrix | Approve retry/backoff/DLQ/replay authority | DRAFT |
| PH3-043 | Storage purge failure recovery policy | LIFECYCLE-006 / OPS-005 | Operations | PURGE_FAILED recovery incomplete | Define retry/manual/terminal handling | Approve retention and recovery policy | DRAFT |
| PH3-044 | Workspace/Content Plan/Content Slot transition authority matrix | LIFECYCLE-007 / CC-010 | Core Architecture + Product/Contracts | Entity ownership vs command authority not fully consolidated | Define entity ownership and command authority separately | Approve ownership and mutation boundaries | DRAFT |

## Cross-Decision Clusters

The 44 PH3 IDs should not automatically become 44 independent owner decisions. They cluster into:

1. Identity / Authorization / Capability
2. Commercial / Billing / Entitlement
3. Configuration / Security
4. Lifecycle / State / Events
5. API / Integration
6. Content Production
7. Research / Intelligence / Analytics
8. Tenant / White-label
9. Platform Operations / DR / Observability
10. Privacy / Data Lifecycle
11. UI State / Product Interaction

## Verification Requirements Before Owner Approval

Each row must be rechecked against the relevant `original/` evidence before it can become an approved Source-of-Truth decision.

A recommendation may be changed or rejected if the evidence shows:
- the proposed authority already exists elsewhere;
- the issue is only terminology/relationship clarification;
- two findings are actually one decision;
- the proposed registry/contract would duplicate an existing authoritative source;
- the evidence is insufficient.

## Phase 4 Pass 1 Status

```text
Analysis performed                         COMPLETE
Draft matrix prepared                      COMPLETE
Owner decisions                            NOT STARTED
Source-of-Truth finalization               NOT STARTED
Corrective edits                           NOT STARTED
original/                                  IMMUTABLE
```
