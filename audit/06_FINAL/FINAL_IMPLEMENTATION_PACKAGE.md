# FINAL IMPLEMENTATION PACKAGE

## Purpose
This is the single handoff document for implementation. It consolidates the audited authority map and tells the builder exactly which documents control each concern.

## Source hierarchy
1. `original/` — immutable source material. Never edit.
2. `audit/03_DECISIONS/source-of-truth.md` — reconciled business/product authority.
3. `audit/03_DECISIONS/phase-4-final-source-of-truth-decisions-id.md` — Phase 4 reconciled decisions.
4. `audit/04_CHANGE_PLAN/working/` — controlled corrected working set for PRD, architecture, contracts, domain, lifecycle, API, UI, operations and build rules.
5. `audit/05_FINAL_VERIFICATION/slices/` — slice-level implementation specifications. These are implementation specifications, not evidence that software has already been implemented or runtime-tested.
6. `audit/04_CHANGE_PLAN/canonical-registries-id.md` and related registry files — canonical cross-document indexes.

## Implementation rule
Build from the corrected working set and slice specifications while resolving every requirement against the source-of-truth documents. Do not invent business behavior where a source is silent. When a specification conflicts with an immutable original or reconciled decision, stop and resolve the conflict before implementation.

## Final document set
| Concern | Authoritative implementation document |
|---|---|
| Product requirements | `audit/04_CHANGE_PLAN/working/01-final-prd-id.md` |
| Architecture | `audit/04_CHANGE_PLAN/working/02-final-architecture-id.md` |
| Contracts | `audit/04_CHANGE_PLAN/working/03-final-contracts-id.md` |
| Domain specifications | `audit/04_CHANGE_PLAN/working/04-final-domain-specifications-id.md` |
| Lifecycle/state | `audit/04_CHANGE_PLAN/working/05-final-lifecycle-state-id.md` |
| API | `audit/04_CHANGE_PLAN/working/06-final-api-specifications-id.md` |
| UI/design | `audit/04_CHANGE_PLAN/working/07-final-ui-design-specifications-id.md` |
| Operations/deployment | `audit/04_CHANGE_PLAN/working/08-final-operations-deployment-specifications-id.md` |
| Build/implementation rules | `audit/04_CHANGE_PLAN/working/09-build-rules-implementation-rules-id.md` |
| Canonical registries | `audit/04_CHANGE_PLAN/working/canonical-registries-id.md` |
| Document synchronization | `audit/04_CHANGE_PLAN/working/final-document-synchronization-matrix-id.md` |
| Slice gate | `audit/05_FINAL_VERIFICATION/final-slice-specification-gate-id.md` |

## Build order
### P0
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

### P1
P1.01 Planner Intelligence
P1.02 Analyzer Add-ons
P1.03 Blueprint Add-ons
P1.04 Analytics Foundation
P1.05 Analytics Intelligence/Feedback
P1.06 Support Center
P1.07 Referral & Milestones
P1.08 Finance/Reconciliation
P1.09 Full Admin Godmode

## Mandatory trace for every slice
Business decision → PRD requirement → domain owner → contract → data model → lifecycle/state → API/application operation → event/worker → UI behavior → security/authorization → persistence/storage → observability → error handling → idempotency/concurrency → tests → acceptance criteria → exit condition.

## Non-negotiable boundaries
- Order, Payment and Fulfillment remain separate authorities.
- Authorization is server-enforced and is not replaced by UI visibility.
- Entitlement is distinct from authorization.
- Research owns canonical source/evidence/provenance; Analyzer owns derived interpretation.
- Planner issues planning commands; Content Context owns Content Slot state.
- Storage owns object lifecycle; consuming domains own business meaning.
- Provider adapters never become business-state owners.
- Retryable commands/event consumers are idempotent.
- Ambiguous payment/provider outcomes enter reconciliation; they are not silently treated as success or failure.
- Security-sensitive configuration changes require the approved authorization/approval path.
- `original/` is read-only.

## Acceptance rule
A slice is build-ready only when its required specification sections are resolved against the authoritative sources. A written checklist or specification is not evidence of runtime success. Runtime success requires implementation and test evidence.

## Final handoff status
The audit deliverable is the corrected implementation package above plus its referenced authoritative documents. The package is ready to be used as the implementation baseline. Runtime implementation/testing is a separate execution stage and must not be represented as completed by documentation alone.
