# Phase 4 — Pass 1 Decision Matrix — Reclassified (DRAFT)

## Status

**DRAFT — VERIFIED AGAINST PHASE 1–3 AUDIT RESULTS; PENDING OWNER DECISION**

This version reclassifies the original Phase 4 Pass 1 matrix using the existing Phase 1–3 verification results. It does not establish final Source-of-Truth decisions and does not authorize corrective edits.

## Classification Rules

- **CRITICAL — DECISION REQUIRED**: verified conflict or implementation-blocking authority gap.
- **HIGH — RECONCILIATION REQUIRED**: verified completeness/authority gap.
- **MEDIUM — CLARIFICATION REQUIRED**: verified governance, taxonomy, or relationship gap.
- **MERGED / RECLASSIFIED**: not an independent final decision.
- **NOT VERIFIED**: removed from decision scope unless new evidence appears.
- Existing contracts are not treated as missing merely because a dedicated registry/index does not exist.

## Reclassified Decision Set

| Priority | Finding / Decision Area | Verified Evidence Basis | Correct Interpretation | Decision Required | Status |
|---|---|---|---|---|---|
| CRITICAL | Role ↔ Entitlement boundary | CONFLICT-002, CONFLICT-012, CONFLICT-013, CONFLICT-015; Core Contracts #2/#4; PRD/Business Decisions | Existing contracts establish separation, but PRD/Analyzer/Agency wording creates implementation risk. | Approve canonical rule: Role controls authorization; Membership/Entitlement controls commercial access. | PENDING OWNER |
| CRITICAL | Agency Mode semantics | CONFLICT-012; Business Decision Register; PRD; Contract #4 | Agency Mode is described inconsistently as Role vs Membership/commercial product. | Approve canonical terminology and commercial model. | PENDING OWNER |
| CRITICAL | Manual Transfer + minimal Support sequencing | CONFLICT-011 was verified in Pass 01, but Pass 02 reclassified it because P0.07 already includes minimal Support Payment Verification. | Not a current critical sequencing conflict. Remaining issue is explicit ownership/API boundary for P0 support subset. | Approve ownership/boundary only if required. | RECLASSIFIED |
| CRITICAL | Analyzer ↔ Research canonical source | CONFLICT-004; Contracts #10/#12 | Canonical Research model already exists and Analyzer is required to reuse it. Remaining issue is raw-input persistence/classification. | Approve raw-input persistence and ownership policy. | PENDING OWNER |
| CRITICAL | Subscription authority/lifecycle | GAP-013 / Pass 02 | Existing Product/Entitlement contract references lifecycle, but no dedicated authoritative Subscription entity/state-machine source was found. | Decide canonical Subscription authority and lifecycle model. | PENDING OWNER |
| CRITICAL | Security & Content Protection | GAP-015 / Pass 02/03 | PRD references a dedicated source, but the dedicated source is absent from the original inventory. | Decide whether to create the authoritative specification and its owner. | PENDING OWNER |
| CRITICAL | Asset / Editor / Export contract coverage | GAP-014/GAP-027 / Pass 02 | Implementation requirements exist; dedicated equivalent core contracts are absent. This is a contract-coverage gap, not proof that the domains are unspecified. | Decide whether existing implementation specs are elevated or dedicated contracts are created. | PENDING OWNER |
| CRITICAL | PRD feature-access wording | CONFLICT-015 | Existing authority separates authorization and entitlement, but PRD wording can cause incorrect implementation. | Approve canonical wording before correction. | PENDING OWNER |
| HIGH | Capability vocabulary/registry | GAP-001 | Capability concepts already exist; no single canonical vocabulary/registry was found. | Decide whether canonical registry/index is needed or existing contract becomes authority. | PENDING OWNER |
| HIGH | Permission catalog | GAP-002 | Contract #2 exists; complete canonical permission catalog is not centralized. | Decide canonical catalog authority. | PENDING OWNER |
| HIGH | Configuration key registry | GAP-003 | Contract #3 exists and is substantial; complete authoritative registry is not centralized. | Decide whether Contract #3 itself is authoritative or a registry/index is required. | PENDING OWNER |
| HIGH | State machine index | GAP-004 | State machines exist across contracts; no unified cross-domain index. | Decide canonical index/reference mechanism. | PENDING OWNER |
| HIGH | Event catalog | GAP-005 | Event definitions/infrastructure exist; complete cross-domain catalog is not centralized. | Decide canonical event catalog authority. | PENDING OWNER |
| HIGH | API contract registry | GAP-006 | API behavior exists locally across contracts/implementation. | Decide canonical API registry/reference mechanism. | PENDING OWNER |
| HIGH | Entity ownership registry | GAP-007 | Architecture defines domain ownership; complete entity-level registry is absent. | Decide whether entity ownership matrix is required as canonical authority. | PENDING OWNER |
| HIGH | Per-slice specifications | GAP-008 | Existing implementation specification is a framework/template; concrete per-slice set is absent. | Approve required concrete slice specification set and gate. | PENDING OWNER |
| HIGH | Historical observation mapping | GAP-009 | Deferred/extended research behavior exists but exact slice ownership is not centralized. | Decide ownership and specification location. | PENDING OWNER |
| HIGH | Configuration ↔ Security boundary | GAP-010 / CONFLICT-006 | Configuration authority exists, but security/authorization precedence enforcement needs explicit rules. | Approve precedence and enforcement boundary. | PENDING OWNER |
| HIGH | Planner ↔ Content Context command boundary | GAP-011 / CONFLICT-003 | Content Slot ownership and Planner command behavior are separate concerns. | Approve ownership vs command authority. | PENDING OWNER |
| HIGH | Entitlement failure/reversal | GAP-012 | Consumption/idempotency exists; full failure/reversal matrix is decentralized. | Approve canonical transaction/reversal matrix. | PENDING OWNER |
| HIGH | Subscription allocation schedule | GAP-017 | Contract #4 defines cycle-based allocation but not all proration/renewal/unused-balance rules. | Approve schedule and edge-case rules. | PENDING OWNER |
| HIGH | Purchase eligibility | GAP-018 | Eligibility dimensions exist in Contract #4 but no canonical decision matrix. | Approve canonical eligibility matrix. | PENDING OWNER |
| HIGH | Refund ↔ Entitlement reversal | GAP-019 | Payment triggers reversal, but complete outcomes are decentralized. | Approve canonical refund/reversal behavior. | PENDING OWNER |
| HIGH | Provider failure ↔ consumption | GAP-020 | Idempotency exists, but reservation/commit/release and ambiguous-success handling are incomplete. | Approve transaction/failure semantics. | PENDING OWNER |
| HIGH | Event aggregate/partition semantics | GAP-021 | Event fields/versioning exist; complete aggregate/partition guarantees are not centralized. | Approve canonical event partition rules. | PENDING OWNER |
| HIGH | API error taxonomy | GAP-022 | Error examples exist locally; shared taxonomy absent. | Approve canonical error taxonomy. | PENDING OWNER |
| HIGH | Data deletion/privacy lifecycle | GAP-023 | Storage retention exists; complete account/data deletion lifecycle is not centralized. | Approve deletion/anonymization/retention authority. | PENDING OWNER |
| HIGH | Backup/DR acceptance | GAP-024 | Operations requires backup/restore testing; concrete RPO/RTO/acceptance criteria absent. | Approve measurable DR criteria. | PENDING OWNER |
| HIGH | Observability standard | GAP-025 | Logging/correlation exists; domain-wide mandatory observability standard absent. | Approve metrics/alerts/dashboard ownership. | PENDING OWNER |
| HIGH | Own Content Intelligence ↔ Analytics | GAP-029 | Analytics and Research responsibilities exist but handoff/ownership of derived intelligence is incomplete. | Approve ownership and interface. | PENDING OWNER |
| HIGH | White-label activation boundary | GAP-030 / CONFLICT-008 | White-label commercial decisions exist; activation/ownership boundary not centralized. | Approve activation authority and boundary. | PENDING OWNER |
| HIGH | Security-sensitive configuration approval | GAP-031 | Security/config concepts exist; approval state/actor separation incomplete. | Approve protected keys and approval workflow. | PENDING OWNER |
| HIGH | Platform-wide clock/timezone authority | GAP-032 | Multiple domains use time semantics; no platform-wide authority. | Approve clock/timezone/DST authority. | PENDING OWNER |
| HIGH | Entitlement remaining amount | GAP-033 | Granted/consumed/remaining concepts exist; authoritative/derived status is not fully fixed. | Approve source and mutation/reconciliation rule. | PENDING OWNER |
| HIGH | Order fulfillment failure/reconciliation | GAP-034 | PAID/FULFILLED exists; post-payment failure/retry/reconciliation is incomplete. | Approve canonical state/recovery model. | PENDING OWNER |
| HIGH | Refund after fulfillment | GAP-035 | Refund and entitlement reversal exist separately; complete cross-domain workflow decentralized. | Approve canonical workflow. | PENDING OWNER |
| HIGH | Notification delivery vs read state | GAP-036 | Delivery and read state are distinct dimensions but ownership/API semantics need explicit definition. | Approve state model. | PENDING OWNER |
| HIGH | Provider/Product/Entitlement capability vocabulary | GAP-037 | ProviderCapability and product capability concepts exist; cross-layer vocabulary not canonical. | Approve mapping/terminology. | PENDING OWNER |
| MEDIUM | Market/localization/currency depth | GAP-016 | Configuration/product rules exist but cross-domain authority is not centralized. | Approve canonical ownership and rule location. | PENDING OWNER |
| MEDIUM | Dependency taxonomy | CONFLICT-010 | Architecture dependency and implementation sequencing are different dimensions. | Approve terminology/classification rules. | PENDING OWNER |
| MEDIUM | Final sequencing document status | CONFLICT-016 | Document title says Final while status says Draft. | Approve governance/status correction. | PENDING OWNER |
| MEDIUM | Workspace ↔ Research Workspace relationship | CONFLICT-007 | Both are intentionally distinct; no direct conflict verified. | Clarify relationship only if required. | PENDING OWNER |

## Removed / Merged From Decision Scope

| Original ID | Result | Reason |
|---|---|---|
| GAP-026 | NOT VERIFIED | Roadmap and Vertical Slice Order matched in inspected sections. |
| GAP-038 | MERGED INTO GAP-028 | Same raw-source identity/persistence issue. |
| CONFLICT-001 | RECLASSIFIED | Terminology ambiguity, not substantive business conflict. |
| CONFLICT-005 | RECLASSIFIED | Provider boundary clarification, not contradiction. |
| CONFLICT-006 | RECLASSIFIED | Configuration/security enforcement gap. |
| CONFLICT-009 | RECLASSIFIED | Engines vs Domains terminology/governance. |
| CONFLICT-014 | RECLASSIFIED | Entity/reference ownership clarification. |
| CONFLICT-016 | GOVERNANCE | Status ambiguity, not business conflict. |

## Decision Principle

The matrix does **not** assume that every gap requires a new document.

For each decision:

1. Identify existing authoritative material.
2. Determine whether it is complete enough.
3. Determine whether another document conflicts with it.
4. Determine whether a canonical index/registry is actually necessary.
5. Only then decide whether to create a new Source-of-Truth document.

## Phase 4 Pass 1 Status

```text
Phase 1–3 audit findings                 COMPLETE
Critical/High verification               COMPLETE
Medium/Low/reference verification         COMPLETE
Initial decision matrix                   SUPERSEDED
Reclassified decision matrix              COMPLETE — DRAFT
Owner decisions                           NOT STARTED
Source-of-Truth finalization              NOT STARTED
Corrective edits                          NOT STARTED
original/                                 IMMUTABLE
```

## Next Step

The next controlled step is:

**Owner Decision Review**

The project owner reviews each decision area and selects/approves the authoritative source or required correction. No source document should be edited before that decision is recorded.
