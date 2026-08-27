# Phase 4 — Final Source-of-Truth Decisions

## Status

**FINAL — Phase 4 reconciliation complete.**

Dokumen ini menetapkan keputusan Source-of-Truth berdasarkan evidence audit Phase 1–3 dan Business Decision Register yang sudah final. Dokumen ini tidak mengubah konsep bisnis/platform; fungsinya menghilangkan ambiguity dan menetapkan authority teknis yang diperlukan agar implementasi tidak menebak.

`original/` tetap immutable.

## 1. Authority Hierarchy

1. Final Business Decision Register — business decisions.
2. Final PRD — product requirements and business rules, sepanjang tidak bertentangan dengan keputusan bisnis final.
3. Core Contracts — domain semantics, entities, invariants, lifecycle and API/event ownership.
4. Core Architecture — system/domain boundaries and infrastructure topology.
5. Implementation Specifications — concrete slice behavior and acceptance criteria.
6. Code — implementation of approved specifications; code does not override specification.

Jika terdapat konflik, authority tingkat lebih tinggi berlaku dan dokumen downstream harus dikoreksi.

## 2. Canonical Decisions

### D-01 — Agency Mode

`Agency Mode` adalah konsep commercial membership/product mode, bukan System Role.

- Commercial access → Membership/Product/Entitlement.
- Authorization → Role/Permission.
- Role seperti Agency Admin/Operator mengatur tindakan yang boleh dilakukan di tenant/workspace.
- Tidak ada role yang secara mandiri memberikan commercial entitlement.

### D-02 — Analyzer Raw Input

Research menjadi pemilik canonical source/evidence truth.

Raw input yang masuk melalui Analyzer harus dipersist sebagai input/source yang dapat ditelusuri dan kemudian direpresentasikan dalam canonical Research model. Analyzer memiliki analysis run dan derived interpretation, bukan canonical source kedua.

Default lifecycle:

`Raw Input → Research Source/Input → Analyzer Run → Derived Interpretation/Output`

### D-03 — Subscription Authority

Subscription menjadi domain entity yang memiliki dedicated authoritative lifecycle specification.

State minimum:

`ACTIVE → CANCELLED_PENDING_END → EXPIRED`

Reactivation/renewal dan tanggal efektif harus mengikuti state machine Subscription. Product/Package definition tetap berada pada Product/Membership authority; Subscription tidak mengambil ownership Product.

Business rules yang sudah final tetap berlaku:
- cancellation tidak langsung menghapus benefit sampai end date;
- package yang sudah dibeli tetap dimiliki tetapi terkunci ketika subscription inactive;
- pembelian package baru diblokir ketika subscription inactive.

### D-04 — Security & Content Protection

Dibutuhkan dedicated authoritative technical specification untuk Security & Content Protection.

Business policy yang tidak berubah:
- protected content default ON;
- hanya protected area yang terkena auto-blur pada focus loss;
- tidak memerlukan indikator khusus;
- protection adalah deterrence, bukan jaminan OS-level absolute protection.

Technical specification harus mendefinisikan ownership, threat assumptions, controls, limitations dan acceptance criteria.

### D-05 — Asset / Editor / Export

Tidak membuat tiga contract baru secara otomatis.

Authority yang sudah tersebar pada Architecture/Implementation/Design harus dielevasi menjadi specification authoritative untuk masing-masing domain bila memenuhi minimum:
- owner;
- entity;
- state/lifecycle;
- command/query boundary;
- API/event behavior;
- invariants;
- acceptance/DoD.

Contract terpisah hanya dibuat jika coverage tersebut tidak dapat dipenuhi oleh authority existing.

### D-06 — Canonical Registries

Registry/index digunakan sebagai governance/discovery layer, bukan sebagai pengganti semantic authority.

- Capability → domain contract owns semantics; registry indexes vocabulary/mapping.
- Permission → Contract #2 owns semantics; registry indexes permissions.
- Configuration → Contract #3 owns semantics; registry indexes key/schema/scope/default/owner.
- State Machine → domain contract owns transitions; cross-domain index maps states.
- Event → producing domain owns semantics; catalog indexes event name/version/payload/producer/consumer.
- API → domain contract owns operation semantics; registry indexes operation/error/idempotency.
- Entity Ownership → Architecture/contracts own ownership; registry provides cross-domain index.

### D-07 — Cross-Domain Lifecycle Policies

Canonical technical defaults:

- Entitlement consumption uses explicit reservation → commit → release/reversal semantics and idempotency.
- Payment success does not equal fulfillment success.
- Payment owns financial refund; Entitlement owns entitlement reversal; Referral owns commission consequence.
- Provider timeout/ambiguous success requires explicit reconciliation and must prevent double consumption.
- Order fulfillment requires explicit recovery/retry/reconciliation states.
- Notification delivery state and recipient read state are separate.
- Platform clock is UTC; timezone is business/presentation context where applicable.
- `remaining` entitlement is derived from granted minus consumed, with reconciliation; a persisted projection may exist only as a performance optimization and is not an independent authority.
- Privacy deletion/anonymization is distinct from financial/audit retention obligations.
- Backup/DR requires measurable RPO/RTO and restore acceptance tests before production.
- Observability has a platform minimum standard while domains own domain-specific metrics.
- Configuration cannot override authorization, tenant isolation, or security boundaries.
- Planner owns planning commands; Content Context owns Content Slot state/data.

## 3. Decisions Already Locked by Existing Business Authority

The following are not reopened by Phase 4:

- Role vs Membership/Entitlement separation.
- Member billing model.
- Package retention/locking.
- Subscription cancellation through end date.
- Manual Transfer approval flow.
- Payment gateway configurability.
- Product/add-on configurability.
- IDR/USD core currency.
- Support SLA and ticket rules.
- Single-login session policy.
- Content Protection business defaults.
- Indonesia/English minimum i18n.
- Agency markup/fixed pricing.
- Agency wholesale settlement model.
- White-label core-ready/not-full-build status.
- Normal member billing without Top Up/PAYG/Deposit Balance.
- Admin flexibility for roles, permissions, products, pricing and providers.

## 4. Required Downstream Corrections

The following are documentation corrections, not concept changes:

1. Remove wording that makes Role an entitlement source.
2. Define Agency Mode consistently as commercial mode/product and keep authorization roles separate.
3. Add/raise Subscription lifecycle authority.
4. Add Security & Content Protection technical authority.
5. Consolidate Asset/Editor/Export specifications where current authority is fragmented.
6. Add canonical indexes/registries without moving semantic ownership.
7. Add concrete P0 per-slice specifications before implementation.
8. Add cross-domain transition/recovery matrices required by lifecycle findings.

## 5. Phase 4 Closure Criteria

Phase 4 is considered complete when:

- verified findings have an explicit disposition;
- existing final business decisions are preserved;
- no new business concept is invented to close a documentation gap;
- authority is assigned for every critical decision area;
- downstream correction requirements are explicit;
- `original/` remains immutable;
- Phase 5 has a deterministic change list.

All criteria above are satisfied by this decision record.

## 6. Next Phase

Phase 4 → **COMPLETE**.

Next is Phase 5 — Controlled Corrections:

`Final SoT Decisions → Change Plan → Working/Corrected Documents → Cross-Reference Synchronization → Final Verification`

No correction may modify `original/`.
