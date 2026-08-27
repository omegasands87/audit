# Audit Checklist — Repository `original`

## Purpose
Ensure all documents in `original/` are consistent, complete, have clear ownership, and are safe to use as an AI Builder implementation baseline.

## Rules
- `original/` is immutable during audit.
- Audit cross-document consistency, not only individual files.
- Record findings before corrective edits.
- Classify findings as `CONFLICT` or `GAP`.
- Do not silently resolve unresolved business decisions.
- Update this single checklist as the audit progresses.

## 1. Inventory & Authority
- [x] Inventory all files in `original/`
- [x] Group files by Governance / Product / Architecture / Core Contracts / Implementation / Design / Operations
- [x] Identify Draft / Baseline / Final status
- [x] Identify Source-of-Truth declarations
- [x] Establish working authority hierarchy
- [ ] Reconcile all authority conflicts

## 2. Product / PRD
- [x] Business Decision alignment — preliminary
- [x] Feature scope — preliminary
- [x] Billing / entitlement — preliminary
- [x] Research / Planner / Analyzer / Blueprint — preliminary
- [ ] Complete detailed requirement audit
- [ ] Complete terminology audit
- [ ] Complete lifecycle/state audit
- [ ] Complete White-label / Agency audit
- [ ] Complete localization / market / currency audit

## 3. Core Contracts #1–#13
- [x] Scope — preliminary
- [x] Ownership — preliminary
- [x] Entity definitions — preliminary
- [x] Lifecycle/state — preliminary
- [x] Boundaries/dependencies — preliminary
- [ ] Complete cross-contract audit
- [ ] Complete completeness audit
- [ ] Complete DoD/acceptance audit

## 4. Cross-Contract Consistency
- [x] Identity ↔ Role & Permission
- [x] Role ↔ Membership
- [x] Product ↔ Entitlement
- [ ] Order ↔ Payment
- [ ] Payment ↔ Refund
- [ ] Payment ↔ Referral
- [ ] Payment ↔ Entitlement
- [x] Provider ↔ consuming domains — preliminary
- [ ] Storage ↔ business domains
- [ ] Event ↔ Audit ↔ Notification
- [x] Workspace ↔ Tenant — preliminary
- [x] Research ↔ Analyzer — preliminary
- [x] Planner ↔ Content Slot — preliminary
- [ ] Content Slot ↔ Blueprint
- [ ] Blueprint ↔ Asset Generation
- [x] Configuration ↔ Product — preliminary
- [x] Configuration ↔ Permission — preliminary
- [ ] Support ↔ Payment
- [x] Agency ↔ normal member billing — preliminary

## 5. Architecture
- [x] Domain ownership vs Core Contracts — preliminary
- [x] Engine vs Domain — preliminary
- [ ] Dependency graph
- [ ] Database ownership
- [ ] Cross-domain mutation
- [ ] Event/outbox strategy
- [ ] Worker boundaries
- [x] Provider boundary — preliminary
- [ ] Security boundary
- [ ] Tenant isolation
- [ ] Vertical-slice dependency model

## 6. Implementation
- [ ] Structure follows architecture
- [ ] Business rules follow contracts
- [ ] No duplicate entities/models
- [ ] No duplicate service ownership
- [ ] API matches contracts
- [ ] State machines match contracts
- [ ] Events match contracts
- [ ] Config keys match configuration contract
- [ ] Slice IDs/phases/dependencies are synchronized across implementation documents

## 7. Design / UI
- [ ] UI terminology matches PRD
- [ ] Role/permission UI matches authorization
- [ ] Membership/product UI matches entitlement
- [ ] Research → Planner → Analyzer → Blueprint flow matches contracts
- [ ] UI does not imply unsupported capability
- [ ] UI states/errors match backend state machines

## 8. Operations
- [ ] Deployment matches architecture
- [ ] Monitoring matches boundaries
- [ ] Logging/audit matches security requirements
- [ ] Backup/recovery matches ownership
- [ ] Retention/purge matches policy
- [ ] Provider failure/retry matches contracts
- [ ] Runbooks match implementation

## 9. Completeness Gaps
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

## 10. Terminology
- [ ] One canonical term per concept
- [ ] No duplicate terms for one entity
- [ ] No overloaded entity names
- [ ] State names have explicit local semantics
- [ ] Product / Package / Add-on / Capability / Entitlement separated
- [ ] Role / Permission / Membership separated

## 11. Findings Management
- [x] Findings recorded before correction
- [x] Findings classified as CONFLICT or GAP
- [x] Severity assigned
- [ ] Every finding has evidence/reference
- [ ] Every finding has final Source of Truth decision
- [ ] Every finding mapped to required change

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
├── 03_DECISIONS/
├── 04_CHANGE_PLAN/
└── 05_FINAL_VERIFICATION/
```

## Current Phase

```text
Phase 1 — Inventory & Authority          COMPLETE
Phase 2 — Deep Cross-Document Audit      IN PROGRESS
Phase 3 — Full Completeness Audit        PENDING
Phase 4 — Source-of-Truth Reconciliation PENDING
Phase 5 — Controlled Corrections         PENDING
Phase 6 — Final Verification             PENDING
```
