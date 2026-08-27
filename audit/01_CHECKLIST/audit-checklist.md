# Audit Checklist — Repository `original`

## Purpose
Ensure all documents in `original/` are consistent, complete, have clear ownership, and are safe to use as an AI Builder implementation baseline.

## Rules
- `original/` is immutable during audit.
- Audit cross-document consistency, not only individual files.
- Record findings before corrective edits.
- Classify findings as `CONFLICT` or `GAP`.
- Findings are not final until verified and reviewed with the project owner.
- Do not silently resolve unresolved business decisions.
- Update this single checklist as the audit progresses.

## 1. Inventory & Authority
- [x] Inventory all files in `original/`
- [x] Group files by Governance / Product / Architecture / Core Contracts / Implementation / Design / Operations
- [x] Identify Draft / Baseline / Final status
- [x] Identify Source-of-Truth declarations
- [x] Establish working authority hierarchy
- [ ] Reconcile all authority conflicts

## 2. Governance & Source of Truth
- [x] Check hierarchy of authority
- [x] Check Final / Baseline / Draft status
- [ ] Reconcile all conflicting authority/status statements
- [ ] Verify every referenced source document actually exists
- [ ] Verify every domain has an authoritative contract or explicit owner

## 3. Product / PRD
- [x] Business Decision alignment — preliminary
- [x] Feature scope — preliminary
- [x] Billing / entitlement — preliminary
- [x] Research / Planner / Analyzer / Blueprint — preliminary
- [ ] Complete detailed requirement audit
- [ ] Complete terminology audit
- [ ] Complete lifecycle/state audit
- [ ] Complete White-label / Agency audit
- [ ] Complete localization / market / currency audit
- [ ] Verify all PRD-referenced technical source documents exist

## 4. Core Contracts #1–#13
- [x] Scope — preliminary
- [x] Ownership — preliminary
- [x] Entity definitions — preliminary
- [x] Lifecycle/state — preliminary
- [x] Boundaries/dependencies — preliminary
- [ ] Complete cross-contract audit
- [ ] Complete completeness audit
- [ ] Complete DoD/acceptance audit
- [ ] Verify subscription ownership/lifecycle
- [ ] Verify missing-domain contract coverage

## 5. Cross-Contract Consistency
- [x] Identity ↔ Role & Permission — preliminary
- [x] Role ↔ Membership — conflict found
- [x] Product ↔ Entitlement — preliminary
- [ ] Order ↔ Payment
- [ ] Payment ↔ Refund
- [ ] Payment ↔ Referral
- [ ] Payment ↔ Entitlement
- [x] Provider ↔ consuming domains — preliminary
- [ ] Storage ↔ business domains
- [ ] Event ↔ Audit ↔ Notification
- [x] Workspace ↔ Tenant — preliminary
- [x] Research ↔ Analyzer — conflict/gap found
- [x] Planner ↔ Content Slot — conflict found
- [ ] Content Slot ↔ Blueprint
- [ ] Blueprint ↔ Asset Generation
- [x] Configuration ↔ Product — preliminary
- [x] Configuration ↔ Permission — conflict/gap found
- [ ] Support ↔ Payment
- [x] Agency ↔ normal member billing — preliminary
- [ ] Subscription ↔ Product ↔ Payment ↔ Entitlement
- [ ] Analytics ↔ Research ↔ Planner
- [ ] Asset ↔ Editor ↔ Export ↔ Storage

## 6. Architecture
- [x] Domain ownership vs Core Contracts — preliminary
- [x] Engine vs Domain — conflict/clarification found
- [ ] Runtime dependency graph
- [ ] Build dependency graph
- [ ] Database ownership
- [ ] Cross-domain mutation
- [ ] Event/outbox strategy
- [ ] Worker boundaries
- [x] Provider boundary — preliminary
- [ ] Security boundary
- [ ] Tenant isolation
- [ ] Configuration vs security boundary
- [ ] Vertical-slice dependency model
- [ ] Architecture coverage for all required domains

## 7. Implementation
- [ ] Structure follows architecture
- [ ] Business rules follow contracts
- [ ] No duplicate entities/models
- [ ] No duplicate service ownership
- [ ] API matches contracts
- [ ] State machines match contracts
- [ ] Events match contracts
- [ ] Config keys match configuration contract
- [ ] Slice IDs/phases/dependencies are synchronized across implementation documents
- [x] Framework for per-slice specification exists
- [ ] Concrete per-slice specifications exist
- [ ] P0 Manual Transfer ↔ Support dependency is resolved

## 8. Design / UI
- [ ] UI terminology matches PRD
- [ ] Role/permission UI matches authorization
- [ ] Membership/product UI matches entitlement
- [ ] Research → Planner → Analyzer → Blueprint flow matches contracts
- [ ] UI does not imply unsupported capability
- [ ] UI states/errors match backend state machines
- [ ] Content protection UI/technical requirements have an authoritative source
- [ ] Agency Mode UI/commercial semantics are reconciled

## 9. Operations
- [ ] Deployment matches architecture
- [ ] Monitoring matches boundaries
- [ ] Logging/audit matches security requirements
- [ ] Backup/recovery matches ownership
- [ ] Retention/purge matches policy
- [ ] Provider failure/retry matches contracts
- [ ] Runbooks match implementation
- [ ] RPO/RTO and disaster recovery acceptance criteria defined
- [ ] Production data deletion/privacy lifecycle defined

## 10. Completeness Gaps
- [ ] Canonical Capability Registry
- [ ] Canonical Permission Registry
- [ ] Canonical Configuration Key Registry
- [ ] Canonical State Machine Index
- [ ] Canonical Event Catalog
- [ ] Canonical API Contract Registry
- [ ] Canonical Entity Ownership Registry
- [ ] Completed per-slice specifications
- [ ] Research historical-observation phase mapping
- [ ] Configuration precedence vs security boundary specification
- [ ] Planner ↔ Content Context command boundary
- [ ] Entitlement consumption failure/reversal matrix
- [ ] Subscription Entity + Lifecycle Contract
- [ ] Support Contract
- [ ] Referral/Milestones Contract
- [ ] Analytics Contract
- [ ] Asset Preparation Contract
- [ ] Editor Contract
- [ ] Export Contract
- [ ] Tenant/White-label Contract
- [ ] Security/Content Protection Contract
- [ ] Market/Localization/Currency Contract
- [ ] Subscription/package allocation schedule
- [ ] Product purchase eligibility matrix
- [ ] Refund ↔ Entitlement reversal policy
- [ ] Provider failure ↔ entitlement reservation/commit/release model
- [ ] Event aggregate/partition key catalog
- [ ] API error/code registry
- [ ] Account/data deletion & privacy lifecycle
- [ ] Backup/restore & disaster recovery acceptance criteria
- [ ] Cross-domain observability matrix
- [ ] Per-slice dependency mapping
- [ ] Research Source vs raw concept/media persistence rule
- [ ] Own Content Intelligence / Analytics ownership boundary
- [ ] White-label activation boundary
- [ ] Security-sensitive configuration approval workflow
- [ ] Platform-wide time/clock authority
- [ ] Reference-document existence audit

## 11. Findings Management
- [x] Findings recorded before correction
- [x] Findings classified as CONFLICT or GAP
- [x] Severity assigned
- [ ] Every finding has verified evidence/reference
- [ ] Every finding has final Source of Truth decision
- [ ] Every finding mapped to required change
- [ ] Project owner has reviewed findings
- [ ] Findings marked final only after project owner verification

## 12. Final Verification
- [ ] All conflicts resolved
- [ ] All gaps resolved or explicitly deferred
- [ ] `original/` unchanged
- [ ] Corrected documents separated from original
- [ ] Cross-references synchronized
- [ ] Full cross-document audit passes

## Audit Output Structure

```text
audit/
├── 01_CHECKLIST/
│   └── audit-checklist.md
├── 02_DEEP_AUDIT/
│   └── consistency-report.md
├── 03_DECISIONS/
│   └── source-of-truth.md
├── 04_CHANGE_PLAN/
│   └── change-plan.md
└── 05_FINAL_VERIFICATION/
```

## Current Phase

```text
Phase 1 — Inventory & Authority          COMPLETE
Phase 2 — Deep Cross-Document Audit      IN PROGRESS
Phase 3 — Full Completeness Audit        IN PROGRESS
Phase 4 — Source-of-Truth Reconciliation PENDING
Phase 5 — Controlled Corrections         PENDING
Phase 6 — Final Verification             PENDING
```

## Current Finding Count

```text
CONFLICT — 14 recorded
GAP      — 32 recorded

Status: working findings, NOT FINAL
```
