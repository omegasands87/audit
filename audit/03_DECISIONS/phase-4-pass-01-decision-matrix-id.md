# Phase 4 — Pass 1 Matriks Keputusan (DRAF)

## Status

**DRAF — MENUNGGU KEPUTUSAN PEMILIK PROYEK**

Dokumen ini menerjemahkan analisis Phase 4 Pass 1 menjadi matriks keputusan yang dapat ditelusuri. Dokumen ini hanya merupakan dokumen kerja. Dokumen ini tidak menetapkan Source-of-Truth final dan tidak mengizinkan perubahan korektif pada `original/`.

## Aturan

- `original/` tetap immutable/tidak boleh diubah.
- Tidak ada baris yang dianggap sebagai keputusan yang telah disetujui.
- Rekomendasi adalah hasil analisis, bukan keputusan pemilik proyek.
- Finding yang sudah ada tetap dipertahankan; hubungan reinforcement tidak dihitung sebagai masalah independen baru.
- Kandidat authority adalah hipotesis yang harus diverifikasi, bukan penetapan authority final.
- Jika evidence tidak cukup, matriks secara eksplisit menyatakannya dan tidak melakukan asumsi.

## Hierarki authority kandidat yang saat ini tercatat dalam audit

1. Final Business Decision Register
2. PRD Final
3. Core Contracts
4. Core Architecture
5. Implementation

Hierarki ini sendiri masih harus diverifikasi/direkonsiliasi pada Phase 4.

## Matriks Keputusan

| ID | Finding | Evidence / dasar audit | Kandidat Source-of-Truth | Konflik / Gap | Rekomendasi | Keputusan yang Diperlukan | Status |
|---|---|---|---|---|---|---|---|
| PH3-001 | Canonical Capability Registry | Completeness finding; memperkuat finding capability/vocabulary/role-entitlement | Registry kanonik baru atau Core Contract yang ditunjuk secara eksplisit | Tidak ada registry authoritative tunggal | Tetapkan satu canonical capability registry | Setujui authority, field, dan lokasi | DRAF |
| PH3-002 | Canonical Permission Registry | Completeness finding; memperkuat pemisahan role/permission | Registry kanonik baru atau Core Contract | Katalog permission belum terpusat | Tetapkan canonical permission registry | Setujui authority, field, dan lokasi | DRAF |
| PH3-003 | Canonical Configuration Key Registry | Completeness finding; memperkuat configuration findings | Registry baru; Architecture + Operations dapat menentukan batas teknis | Key/scope/precedence belum terpusat | Tetapkan registry dengan owner, schema, scope, default, dan precedence | Setujui authority dan model precedence | DRAF |
| PH3-004 | Canonical State Machine Index | Memperkuat LIFECYCLE-001..007 | Architecture / canonical lifecycle registry | State machine tersebar di berbagai contract | Tetapkan canonical state-machine index | Setujui dokumen pemilik dan field wajib | DRAF |
| PH3-005 | Canonical Event Catalog | Memperkuat event findings | Core Architecture / canonical event registry | Ownership/payload/version/retry event belum terpusat | Tetapkan canonical event catalog | Setujui authority dan model ownership event | DRAF |
| PH3-006 | Canonical API Contract Registry | Memperkuat API ownership findings | Core Contracts / canonical API registry | Operations/error/idempotency/versioning tersebar | Tetapkan canonical API contract registry | Setujui authority dan aturan versioning | DRAF |
| PH3-007 | Canonical Entity Ownership Registry | Memperkuat cross-contract findings | Core Architecture / entity ownership registry | Ownership masih disimpulkan dari contract individual | Tetapkan satu ownership matrix authoritative | Setujui authority dan aturan resolusi konflik | DRAF |
| PH3-008 | Concrete per-slice specifications | Existing completeness finding | Layer implementation specification di bawah authority product/architecture yang disetujui | Framework ada tetapi concrete slice belum lengkap | Wajibkan concrete slice spec sebelum implementasi | Setujui field wajib dan gate slice | DRAF |
| PH3-009 | Subscription Entity + Lifecycle Contract | TERM-001 + LIFECYCLE-001 | Core Contract + canonical lifecycle authority | Konsep/lifecycle subscription belum cukup canonical | Definisikan satu canonical subscription entity dan lifecycle | Setujui terminology, owner, dan lifecycle authority | DRAF |
| PH3-010 | Support Contract / explicit minimal P0 Support ownership | Cross-contract/support sequencing finding | Product/Operations/Core Contract tergantung keputusan bisnis | Batas P0 support belum terkonsolidasi | Definisikan ownership P0 support dan escalation | Setujui scope dan owner P0 | DRAF |
| PH3-011 | Referral/Milestones Contract | Completeness finding | Product/Business Decision + Core Contract | Business rules belum terkonsolidasi | Definisikan authoritative referral/milestone contract | Setujui owner dan scope contract | DRAF |
| PH3-012 | Analytics Contract | Memperkuat analytics ownership finding | Core Architecture/Product contract | Ownership/interface analytics belum lengkap | Definisikan canonical analytics contract dan owner | Setujui ownership dan boundary contract | DRAF |
| PH3-013 | Asset Preparation Contract | Production contract coverage finding | Core Contract / Architecture | Boundary asset preparation belum lengkap | Definisikan canonical asset-preparation contract | Setujui owner dan lifecycle boundary | DRAF |
| PH3-014 | Editor Contract | Production contract coverage finding | Core Contract / Architecture | Editor contract belum eksplisit sepenuhnya | Definisikan canonical editor contract | Setujui owner, input/output, lifecycle | DRAF |
| PH3-015 | Export Contract | Production contract coverage finding | Core Contract / Architecture | Export contract belum eksplisit sepenuhnya | Definisikan canonical export contract | Setujui owner dan output semantics | DRAF |
| PH3-016 | Tenant/White-label Contract | White-label boundary finding | Business Decision + Core Contract | Semantik tenant/white-label belum terkonsolidasi | Definisikan canonical tenant/white-label contract | Setujui activation dan ownership boundary | DRAF |
| PH3-017 | Security/Content Protection Contract | Missing dedicated source finding | Security authority / Core Contract | Aturan security/content protection tidak memiliki satu sumber authoritative | Buat explicit security/content-protection authority | Setujui authority, scope, controls | DRAF |
| PH3-018 | Market/Localization/Currency Contract | Completeness finding | Product/Business Decision + Core Contract | Aturan market/localization/currency belum terpusat | Definisikan canonical market/localization/currency contract | Setujui scope dan authority | DRAF |
| PH3-019 | Subscription/package allocation schedule | Product/lifecycle finding | Business Decision / PRD Final | Timing dan aturan allocation belum canonical | Definisikan allocation schedule dan lifecycle semantics | Setujui timing/aturan allocation | DRAF |
| PH3-020 | Product purchase eligibility matrix | Eligibility finding | Business Decision / PRD Final | Eligibility rules belum terpusat | Tetapkan canonical purchase eligibility matrix | Setujui authority eligibility | DRAF |
| PH3-021 | Refund ↔ Entitlement reversal policy | Refund workflow finding | Business Decision + Core Contract | Refund dan entitlement reversal belum terkonsolidasi | Definisikan canonical reversal policy | Setujui timing reversal dan ownership | DRAF |
| PH3-022 | Provider failure ↔ entitlement reservation/commit/release model | Payment/entitlement lifecycle finding | Core Architecture/Core Contracts | Semantik provider failure lintas domain belum lengkap | Definisikan reservation/commit/release model | Setujui failure dan compensation semantics | DRAF |
| PH3-023 | Event aggregate/partition key catalog | Event contract finding | Architecture/event registry | Semantik partition/aggregate belum terpusat | Definisikan canonical key catalog | Setujui authority dan invariants | DRAF |
| PH3-024 | API error/code registry | API completeness finding | Core API contract registry | Error semantics belum terpusat | Tetapkan canonical API error/code registry | Setujui ownership dan versioning code | DRAF |
| PH3-025 | Account/data deletion & privacy lifecycle | Privacy lifecycle finding | Business Decision + Security/Operations authority | Deletion lifecycle belum canonical sepenuhnya | Definisikan deletion/privacy lifecycle | Setujui retention, deletion, ownership | DRAF |
| PH3-026 | Backup/restore & disaster recovery acceptance criteria | OPS-010; Operations audit | Operations | RPO/RTO dan acceptance criteria belum sepenuhnya diputuskan | Definisikan DR acceptance criteria yang terukur | Setujui RPO/RTO dan restore-test requirements | DRAF |
| PH3-027 | Cross-domain observability matrix | OPS-009; UI/Operations findings | Operations | Signal, threshold, owner, action belum terpusat | Tetapkan observability matrix | Setujui severity/threshold ownership | DRAF |
| PH3-028 | Research Source vs raw concept/media persistence rule | Research/Analyzer boundary finding | Core Architecture/Core Contract | Canonical research evidence vs raw input belum jelas | Pisahkan secara eksplisit canonical evidence dari derived/raw input | Setujui persistence dan authority boundary | DRAF |
| PH3-029 | Own Content Intelligence / Analytics ownership boundary | Analytics ownership finding | Product + Architecture | Ownership antara intelligence dan analytics belum lengkap | Definisikan ownership dan data boundary | Setujui owner dan interface | DRAF |
| PH3-030 | White-label activation boundary | Tenant/white-label finding | Business Decision + Core Contract | Activation authority belum sepenuhnya didefinisikan | Definisikan activation boundary dan authority | Setujui siapa/apa yang mengaktifkan white-label | DRAF |
| PH3-031 | Security-sensitive configuration approval workflow | Security/configuration finding | Security + Operations | Approval flow configuration yang memengaruhi security belum lengkap | Definisikan approval, audit, rollback workflow | Setujui protected keys dan approval authority | DRAF |
| PH3-032 | Platform-wide time/clock authority | Completeness finding | Core Architecture | Time authority belum terpusat | Definisikan canonical platform clock/timezone rules | Setujui time source dan timezone semantics | DRAF |
| PH3-033 | Entitlement remaining_amount source-of-truth rule | Entitlement finding | Core Contract / canonical entitlement authority | Authority remaining amount belum eksplisit | Definisikan satu authoritative value dan mutation rules | Setujui source dan reconciliation rule | DRAF |
| PH3-034 | Order fulfillment failure/recovery state machine | LIFECYCLE-003 + Operations | Architecture + Operations | Failure/recovery transition belum terkonsolidasi | Definisikan canonical fulfillment failure/recovery machine | Setujui state, owner, recovery action | DRAF |
| PH3-035 | Refund-after-fulfillment workflow | Refund/fulfillment finding | Business Decision + Core Contract | Post-fulfillment refund semantics belum lengkap | Definisikan canonical workflow dan reversal semantics | Setujui business rule dan ownership | DRAF |
| PH3-036 | Notification delivery vs read-state separation | UI-015 / event finding | Product/UI + Core Contract | Responsibility delivery dan read-state belum sepenuhnya terpisah | Definisikan dua state/contract yang berbeda | Setujui state ownership dan API semantics | DRAF |
| PH3-037 | Provider/Product/Entitlement capability vocabulary | TERM-006 / CC-008 | Product/Business Decision + Core Contracts | Vocabulary capability melintasi boundary commercial/provider | Tetapkan canonical vocabulary dan ownership | Setujui definitions dan mapping rules | DRAF |
| PH3-038 | Subscription lifecycle state machine | LIFECYCLE-001 | Core Architecture/Core Contract | Tidak ada satu canonical subscription state machine | Tetapkan machine canonical dan referensikan di seluruh dokumen | Setujui state, transition, authority | DRAF |
| PH3-039 | Entitlement reservation/commit/release/reversal state model | LIFECYCLE-002 | Core Architecture/Core Contract | Consumption lifecycle belum lengkap | Tetapkan canonical entitlement state model | Setujui transition authority dan compensation rules | DRAF |
| PH3-040 | Order/Payment/Fulfillment cross-domain transition matrix | LIFECYCLE-003 / CC-005 | Core Architecture + Core Contracts | Cross-domain transitions belum lengkap | Tetapkan authoritative transition matrix | Setujui domain ownership dan legal transitions | DRAF |
| PH3-041 | Production pipeline cross-domain state matrix | LIFECYCLE-004 / UI-007/UI-008 | Core Architecture + Core Contracts | Production state dan ownership tersebar | Tetapkan canonical production state matrix | Setujui lifecycle authority dan transitions | DRAF |
| PH3-042 | Event retry/DLQ/replay resolution matrix | LIFECYCLE-005 / OPS-006 | Operations + Architecture | Retry/DLQ/replay lifecycle belum lengkap | Tetapkan operational event recovery matrix | Setujui retry/backoff/DLQ/replay authority | DRAF |
| PH3-043 | Storage purge failure recovery policy | LIFECYCLE-006 / OPS-005 | Operations | PURGE_FAILED recovery belum lengkap | Definisikan retry/manual/terminal handling | Setujui retention dan recovery policy | DRAF |
| PH3-044 | Workspace/Content Plan/Content Slot transition authority matrix | LIFECYCLE-007 / CC-010 | Core Architecture + Product/Contracts | Entity ownership vs command authority belum terkonsolidasi | Pisahkan entity ownership dan command authority | Setujui ownership dan mutation boundaries | DRAF |

## Kelompok Keputusan Lintas Finding

44 PH3 ID tidak otomatis berarti 44 keputusan owner yang independen. Mereka dapat dikelompokkan menjadi:

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

## Persyaratan Verifikasi Sebelum Approval Owner

Setiap baris harus diperiksa kembali terhadap evidence `original/` yang relevan sebelum dapat menjadi keputusan Source-of-Truth yang disetujui.

Rekomendasi dapat diubah atau ditolak apabila evidence menunjukkan bahwa:
- authority yang diusulkan sebenarnya sudah ada di tempat lain;
- masalah tersebut hanya merupakan klarifikasi terminology/relationship;
- dua finding sebenarnya merupakan satu keputusan;
- registry/contract yang diusulkan akan menduplikasi sumber authoritative yang sudah ada;
- evidence tidak cukup.

## Status Phase 4 Pass 1

```text
Analisis dilakukan                           SELESAI
Draft matriks disusun                        SELESAI
Keputusan owner                              BELUM DIMULAI
Finalisasi Source-of-Truth                   BELUM DIMULAI
Corrective edits                             BELUM DIMULAI
original/                                    IMMUTABLE
```
