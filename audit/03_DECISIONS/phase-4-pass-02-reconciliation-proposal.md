# Phase 4 — Pass 2 Source-of-Truth Reconciliation Proposal

## Status

**PROPOSAL — evidence reconciled; owner approval still required for business decisions**

This document advances Phase 4 from the reclassified decision matrix into a controlled reconciliation proposal. It does **not** edit `original/` and does not silently convert proposals into final business decisions.

`original/` remains immutable.

---

## 1. Reconciliation Authority

The current authority chain is:

```text
Final Business Decision Register
        ↓
PRD Final
        ↓
Core Contracts
        ↓
Core Architecture
        ↓
Implementation
```

The Final Business Decision Register is explicitly marked **FINAL BUSINESS DECISION** and states that Role/Membership are separate: Membership owns entitlement/package/product benefit, while Role owns permission/access control. It also locks the member billing model, package behavior, manual transfer flow, white-label foundation, security defaults, and localization/currency decisions.

Therefore Phase 4 should prefer clarification/correction of downstream wording over inventing new business rules where the Business Decision Register already settles the matter.

---

# 2. Critical Reconciliation

## CR-001 — Role ↔ Entitlement

**Proposed disposition: RESOLVED BY EXISTING BUSINESS AUTHORITY; downstream wording correction required.**

### Evidence

The Final Business Decision Register explicitly states:

```text
Membership
→ entitlement / package / product benefit

Role
→ permission / access control
```

The PRD nevertheless contains wording that describes Agency Mode as a role and elsewhere couples feature access to membership/role. Core Contract #2 also contains role configuration language touching entitlement/feature configuration.

### Canonical rule

```text
Role
→ authorization / permission only

Membership / Product / Entitlement
→ commercial capability grant
```

A Role may contain defaults/preferences or authorization configuration, but it must not independently grant a commercial entitlement.

### Required correction

Update downstream PRD/Core Contract wording so it cannot be interpreted as Role being an entitlement source.

**Owner decision required:** NO new business decision appears necessary; approval should confirm this is a clarification of the existing Final Business Decision.

---

## CR-002 — Agency Mode semantics

**Proposed disposition: RECONCILE AS COMMERCIAL MEMBERSHIP/PRODUCT + SEPARATE AUTHORIZATION ROLE.**

### Evidence

The PRD uses `Agency Mode` in role terminology. The Final Business Decision Register defines Agency/White-label as a commercial model with tenant, pricing, synchronization and wholesale settlement foundations. Core Contract #4 models Agency Mode in the Membership/Product context.

### Proposed canonical model

```text
Commercial access:
Agency Membership / Agency Product
        ↓
Agency entitlements / tenant capabilities

Authorization:
Agency Admin / Agency Operator roles
        ↓
permissions within the tenant/workspace
```

`Agency Mode` should therefore not be the authorization owner itself.

### Owner decision required

Confirm the user-facing commercial name:

- `Agency Mode` as the product/membership tier; or
- another final commercial name.

If `Agency Mode` remains the name, its semantic type should be **commercial mode/product**, not System Role.

---

## CR-003 — Manual Transfer + Support

**Proposed disposition: RECLASSIFIED; no critical conflict.**

The Final Business Decision Register already defines the final flow:

```text
Order
→ Manual Transfer
→ Member Transfer
→ Support Ticket proof
→ Admin Approve Ticket
→ Payment = Paid
→ Entitlement Granted
```

No separate payment approval is required after ticket approval.

P0.07 therefore needs only the minimal Support Payment Verification capability; the full Support Center may remain later.

**No new business decision required.**

---

## CR-004 — Analyzer ↔ Research

**Proposed disposition: EXISTING BOUNDARY IS AUTHORITATIVE; raw-input policy remains the only open policy detail.**

Research owns canonical source/evidence truth. Analyzer consumes/reuses Research entities and owns analysis runs and derived interpretation/output.

### Canonical boundary

```text
Research
→ canonical Source / Evidence

Analyzer
→ analysis run
→ derived interpretation
→ output
```

Analyzer must not create a competing canonical source/evidence system.

### Remaining decision

Define treatment of raw concepts/media/documents that enter Analyzer before a Research entity exists:

1. persist as a Research-owned raw source/input;
2. persist temporarily in Analyzer then promote to Research; or
3. another explicitly documented lifecycle.

**Owner decision required.**

---

## CR-005 — Subscription authority/lifecycle

**Proposed disposition: NEW AUTHORITATIVE CONTRACT REQUIRED.**

The Final Business Decision Register already defines important subscription behavior:

```text
Cancelled
→ Active Until End Date
→ Expired
```

Purchased packages remain owned but locked while subscription is inactive; new package purchases are blocked while inactive.

However, no single dedicated Subscription entity/state-machine source currently consolidates identity, state, transitions, renewal, cancellation, expiry, reactivation, dates, actors and side effects.

### Proposed action

Create a dedicated authoritative `Subscription` contract/state machine under Core Contracts, while keeping commercial product definition in Contract #4.

This is a documentation/authority completion, not a new commercial policy.

**Owner decision required:** approve creation/elevation of the Subscription authority.

---

## CR-006 — Security & Content Protection

**Proposed disposition: AUTHORITATIVE SOURCE REQUIRED.**

The PRD explicitly references a dedicated Security & Content Protection source while the original inventory does not contain that source.

The Final Business Decision Register already locks the business-level policy:

- protected content defaults to ON;
- only protected areas are auto-blurred on focus loss;
- no special protection indicator is required;
- protection is deterrence, not an absolute OS-level guarantee.

### Proposed action

Create the dedicated Security & Content Protection technical contract/specification. It should translate the locked business policy into technical controls, threat assumptions, limitations, acceptance criteria, and ownership.

**Owner decision required:** approve creation and authoritative ownership.

---

## CR-007 — Asset / Editor / Export contract coverage

**Proposed disposition: ELEVATE EXISTING IMPLEMENTATION/ARCHITECTURE AUTHORITY OR CREATE DEDICATED CONTRACTS.**

The audit confirms these production domains are required, and existing architecture/implementation material already contains substantial definitions. The absence of three separately numbered contracts is therefore not proof that the requirements are absent.

### Proposed default

Do **not** create three new contracts automatically.

Instead:

1. identify the current authoritative sections for Asset Preparation, Editor and Export;
2. ensure each has owner, entity/state, command/query boundary, API/event contract and DoD;
3. create dedicated contracts only where the existing authoritative material cannot meet that standard.

**Owner decision required:** approve this contract-coverage approach.

---

## CR-008 — PRD feature-access wording

**Proposed disposition: CORRECT DOWNSTREAM WORDING TO MATCH EXISTING BUSINESS AUTHORITY.**

The PRD is authoritative for product/business requirements, but its current wording can be read as:

```text
membership OR role → feature access
```

That is unsafe because the locked business rule is:

```text
Role → permission/access control
Membership/Product/Entitlement → commercial access
```

### Required correction principle

Use terminology such as:

```text
User must have the required entitlement
AND
user must have permission to perform the action
```

rather than treating Role as an entitlement source.

**Owner decision required:** NO new business rule; confirm correction against the existing Final Business Decision Register.

---

# 3. High Reconciliation — Proposed Defaults

The following items appear to be primarily authority/contract consolidation rather than unresolved product strategy. Proposed defaults are recorded so implementation does not invent behavior.

| Area | Proposed default | Owner confirmation |
|---|---|---|
| Capability registry | Contract/domain capability definitions remain authoritative; add a canonical index/registry for discovery and mapping | Required |
| Permission catalog | Contract #2 remains semantic authority; add canonical permission catalog/index | Required |
| Configuration keys | Contract #3 remains semantic authority; add registry for key/schema/scope/default/owner | Required |
| State machine index | Individual domain contracts own state; add cross-domain index | Required |
| Event catalog | Event-owning domains own semantics; add central catalog for names/version/payload/producer/consumer | Required |
| API registry | Domain contracts own API semantics; add central operation/error/idempotency index | Required |
| Entity ownership | Architecture/domain contracts own ownership; add entity-level ownership registry | Required |
| Per-slice specs | Create concrete specification for every P0 slice before implementation begins | Required |
| Configuration/security | Configuration cannot override authorization, tenant isolation, or protected security boundaries | Required |
| Planner/Content Slot | Content Context owns Content Slot; Planner owns planning commands through explicit application boundary | Required |
| Entitlement reversal | Use explicit reservation → commit → release/reversal semantics with idempotency | Required |
| Purchase eligibility | Central decision matrix using Product Active + Price Active + Market Allowed + Eligibility + Payment Method | Required |
| Refund/reversal | Payment owns financial refund; Entitlement owns entitlement reversal; Referral owns commission consequence | Required |
| Provider failure | Explicit ambiguous-success, timeout, retry and reconciliation states; no double consumption | Required |
| Privacy deletion | Separate business deletion/anonymization policy from storage retention and financial/audit retention | Required |
| Backup/DR | Define measurable RPO/RTO and restore acceptance tests before production | Required |
| Observability | Central minimum telemetry standard; domain teams own domain metrics | Required |
| Analytics handoff | Analytics owns performance ingestion/calculation; Research consumes derived signals through explicit contract | Required |
| White-label activation | Tenant/White-label foundation remains core-ready; full UI/operations remain future phase | Required |
| Protected configuration | Security-sensitive keys require explicit approval/actor separation | Required |
| Time authority | Platform clock in UTC; user/market timezone is presentation/business-calendar context unless domain contract says otherwise | Required |
| Entitlement remaining | Prefer derived `remaining = granted - consumed` with reconciliation, unless a performance projection is explicitly defined | Required |
| Fulfillment recovery | Payment success must not imply fulfillment success; use explicit fulfillment state/retry/reconciliation | Required |
| Notification state | Delivery state and recipient read state are separate state dimensions | Required |
| Capability vocabulary | Distinguish Provider Capability from Product Capability from Entitlement | Required |

---

# 4. Items Already Settled by Existing Business Decisions

These should **not** be reopened merely because the audit found documentation gaps:

- referral attribution and commission rules;
- package retention/locking;
- subscription cancellation through end date;
- manual transfer approval trigger;
- payment gateway configurability;
- product/add-on configurability;
- IDR/USD core currency;
- support SLA and ticket rules;
- single-login session policy;
- protected-content business defaults;
- Indonesia/English minimum i18n;
- Agency markup/fixed pricing;
- Agency wholesale settlement model;
- White-label core-ready/not-full-build status;
- normal member billing without Top Up/PAYG/Deposit Balance;
- Admin flexibility for roles, permissions, products, pricing and providers.

These are already marked final in the Business Decision Register and should flow downward rather than be re-decided.

---

# 5. Decision Queue — Owner Input Still Required

Only these decisions should currently block final Source-of-Truth reconciliation:

### D-01 — Agency commercial naming/type

Confirm `Agency Mode` is a commercial membership/product concept, with separate authorization roles.

### D-02 — Analyzer raw-input lifecycle

Choose ownership/persistence behavior for raw inputs that enter Analyzer before a Research entity exists.

### D-03 — Subscription authority

Approve creation/elevation of a dedicated Subscription entity + lifecycle contract.

### D-04 — Security source

Approve creation of the dedicated Security & Content Protection technical source.

### D-05 — Asset/Editor/Export authority

Approve elevation of existing authoritative implementation sections versus creation of dedicated contracts where necessary.

### D-06 — Canonical registry strategy

Approve central indexes/registries while preserving domain contracts as semantic owners.

### D-07 — High-detail lifecycle policies

Approve the proposed defaults for reversal, provider failure, fulfillment recovery, privacy, DR, observability, time, notification and remaining entitlement.

---

# 6. Phase 4 Pass 2 Status

```text
Phase 1–3 audit                         COMPLETE
Phase 4 Pass 1 decision matrix          COMPLETE — superseded by this proposal
Phase 4 Pass 2 evidence reconciliation COMPLETE
Critical existing business rules       RECONCILED
Critical unresolved decisions           7 decision groups
Original source correction              NOT STARTED
original/                               IMMUTABLE
```

## Gate

No `original/` source should be edited until D-01 through D-07 are explicitly approved or rejected by the project owner.

After that:

```text
Owner Decisions
      ↓
Final Source-of-Truth Decision Register
      ↓
Controlled Change Plan
      ↓
Corrections outside original/
      ↓
Final Verification Audit
```
