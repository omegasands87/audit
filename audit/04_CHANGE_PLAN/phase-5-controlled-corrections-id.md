# Phase 5 — Controlled Corrections

## Status
**WORKING CHANGE PLAN — GENERATED FROM FINAL PHASE 4 SOURCE OF TRUTH**

## Guardrails
- `original/` remains immutable.
- No business concept is changed.
- Existing Final Business Decisions remain authoritative.
- Corrections only reconcile wording, authority, lifecycle, ownership, and implementation detail.
- No feature is added unless already required by the existing audited concept.

## Correction Groups

### C-01 — Authority/status normalization
- Normalize Draft/Baseline/Final labels.
- Remove misleading "Final" naming where content is still draft.
- Preserve content unless authority requires correction.

### C-02 — Role / Membership / Entitlement
- Normalize all downstream wording to:
  - Role/Permission = authorization.
  - Membership/Product/Entitlement = commercial access.
- Remove wording that implies Role grants commercial entitlement.

### C-03 — Agency Mode
- Normalize Agency Mode as commercial membership/product mode.
- Agency roles remain authorization mechanisms.

### C-04 — Research / Analyzer
- Research remains canonical source/evidence authority.
- Analyzer owns interpretation/derived output.
- Raw Analyzer input is persisted under Research ownership/provenance.

### C-05 — Subscription
- Add authoritative Subscription entity/lifecycle specification.
- Preserve existing business decisions for cancellation, expiry and package behavior.

### C-06 — Security / Content Protection
- Add technical specification derived from the already-approved business policy.
- Do not strengthen the business promise beyond the existing policy.

### C-07 — Registries / indexes
- Add only discovery/governance indexes required by Phase 4.
- Domain contracts remain semantic authority.

### C-08 — Lifecycle/recovery
- Document deterministic rules for entitlement reservation/commit/release/reversal.
- Document Order/Payment/Fulfillment separation.
- Document refund-after-fulfillment.
- Document provider timeout/ambiguous-success reconciliation.
- Document event retry/DLQ/replay.
- Document storage purge recovery.

### C-09 — Production pipeline
- Make Content Slot → Blueprint → Asset Preparation/Generation → Editor → Export states, ownership and handoffs explicit.

### C-10 — Operations / security / platform rules
- Document UTC platform clock.
- Separate delivery and read state.
- Define privacy deletion versus financial/audit retention.
- Define measurable backup/DR acceptance criteria.
- Define minimum observability requirements.
- Define security-sensitive configuration approval/actor separation.

## Correction Order

1. Authority/status normalization
2. Domain ownership and terminology
3. Subscription/security missing authorities
4. Lifecycle and cross-domain matrices
5. Registry/index references
6. Implementation specification synchronization
7. UI/design synchronization
8. Operations synchronization
9. Cross-reference verification

## Required Final Document Set

The controlled correction phase must produce Indonesian working/final documents for:

1. Final PRD
2. Final Architecture
3. Final Contracts
4. Final Domain Specifications
5. Final Lifecycle/State Definitions
6. Final API Specifications
7. Final UI/Design Specifications
8. Final Operations/Deployment Specifications
9. Build Rules / Implementation Rules

These documents must preserve the original website concept and business decisions.

## Explicit Non-Changes

- No change to product vision.
- No change to core user journeys unless required to resolve an already-identified contradiction.
- No new monetization model.
- No removal of existing product capability.
- No change to Final Business Decision Register.
- No edit to `original/`.

## Phase 5 Acceptance

Phase 5 is complete only when every approved correction has been applied to the working/final document set and all cross-references are synchronized. Final Verification must then re-audit the corrected set.
