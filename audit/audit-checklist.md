# Audit Checklist — Repository `original`

## Tujuan
Memastikan seluruh dokumen di `original/` konsisten, tidak saling bertentangan, memiliki ownership yang jelas, dan dapat digunakan sebagai acuan implementasi AI Builder.

## Aturan Audit
- `original/` adalah baseline dan **tidak boleh diubah** selama audit.
- Audit dilakukan lintas-file, bukan hanya per dokumen.
- Setiap konflik harus menunjuk lokasi sumber yang bertentangan.
- Setiap konflik harus memiliki keputusan Source of Truth.
- Jangan memperbaiki dokumen sebelum keputusan konflik ditetapkan.
- Setelah revisi, lakukan cross-check ulang.

## Checklist Utama

### 1. Inventory
- [ ] Daftar seluruh file dalam `original/`
- [ ] Kelompokkan berdasarkan Governance / Product / Architecture / Core Contracts / Implementation / Design / Operations
- [ ] Catat status setiap dokumen: Draft / Baseline / Final
- [ ] Identifikasi dokumen yang menyatakan dirinya sebagai Source of Truth

### 2. Governance & Source of Truth
- [ ] Periksa hirarki otoritas dokumen
- [ ] Periksa apakah istilah `Final`, `Baseline`, dan `Draft` konsisten
- [ ] Pastikan tidak ada dua dokumen yang sama-sama menjadi authority untuk keputusan yang sama
- [ ] Pastikan perubahan bisnis dan keputusan teknis memiliki tempat yang jelas

### 3. Product / PRD
- [ ] Audit requirement terhadap Business Decision Register
- [ ] Audit feature scope
- [ ] Audit terminology
- [ ] Audit lifecycle/state
- [ ] Audit pricing, billing, entitlement, quota
- [ ] Audit Research / Planner / Analyzer / Blueprint
- [ ] Audit White-label / Agency
- [ ] Audit localization / market / currency

### 4. Core Contracts #1–#13
Untuk setiap contract:
- [ ] Scope jelas
- [ ] Ownership jelas
- [ ] Entity jelas
- [ ] State/lifecycle jelas
- [ ] API boundary jelas
- [ ] Event boundary jelas
- [ ] Dependency jelas
- [ ] Invariants jelas
- [ ] Definition of Done konsisten
- [ ] Tidak mengambil ownership contract lain

### 5. Cross-Contract Consistency
- [ ] Identity ↔ Role & Permission
- [ ] Role ↔ Membership
- [ ] Product ↔ Entitlement
- [ ] Order ↔ Payment
- [ ] Payment ↔ Referral
- [ ] Payment ↔ Entitlement
- [ ] Provider ↔ consuming domains
- [ ] Storage ↔ business domains
- [ ] Event ↔ Audit ↔ Notification
- [ ] Workspace ↔ Tenant
- [ ] Research ↔ Analytics
- [ ] Research ↔ Analyzer
- [ ] Planner ↔ Content Slot
- [ ] Content Slot ↔ Blueprint
- [ ] Blueprint ↔ Asset Generation
- [ ] Configuration ↔ Product
- [ ] Configuration ↔ Permission
- [ ] Support ↔ Payment
- [ ] Agency ↔ normal member billing

### 6. Architecture
- [ ] Domain ownership konsisten dengan Core Contracts
- [ ] Engine vs Domain distinction jelas
- [ ] Dependency graph tidak menciptakan dependency palsu
- [ ] Database ownership jelas
- [ ] Cross-domain mutation rules konsisten
- [ ] Event/outbox strategy konsisten
- [ ] Worker boundaries konsisten
- [ ] Provider adapter boundary konsisten
- [ ] Security boundary konsisten
- [ ] Tenant isolation konsisten
- [ ] Vertical-slice strategy konsisten

### 7. Implementation
- [ ] Struktur implementation mengikuti architecture
- [ ] Tidak ada business rule yang bertentangan dengan Core Contracts
- [ ] Tidak ada duplicate entity/model
- [ ] Tidak ada duplicate service ownership
- [ ] API sesuai contract
- [ ] State machine sesuai contract
- [ ] Event names/payloads sesuai contract
- [ ] Config keys sesuai configuration contract

### 8. Design / UI
- [ ] UI terminology sesuai PRD
- [ ] Role/permission UI sesuai authorization model
- [ ] Membership/product UI sesuai entitlement model
- [ ] Research/Planner/Analyzer/Blueprint flow sesuai architecture
- [ ] Tidak ada UI yang mengimplikasikan capability yang belum ada
- [ ] State dan error UI sesuai backend contract

### 9. Operations
- [ ] Deployment assumptions sesuai architecture
- [ ] Monitoring sesuai domain boundaries
- [ ] Logging/audit sesuai security requirements
- [ ] Backup/recovery sesuai data ownership
- [ ] Retention/purge sesuai policy
- [ ] Provider failure/retry behavior konsisten
- [ ] Operational runbooks sesuai implementation

### 10. Terminology
- [ ] Satu istilah untuk satu konsep
- [ ] Tidak ada istilah berbeda untuk entity yang sama
- [ ] Tidak ada satu istilah yang berarti dua entity berbeda
- [ ] Status/state names konsisten
- [ ] Capability/add-on/package/product/entitlement tidak tercampur

### 11. Severity
Setiap temuan diberi severity:
- [ ] 🔴 Critical — berpotensi membuat sistem salah secara fundamental
- [ ] 🟠 High — berpotensi menghasilkan implementasi berbeda antar-modul
- [ ] 🟡 Medium — ambiguity atau inkonsistensi yang perlu dibereskan
- [ ] 🟢 Minor — wording, naming, atau dokumentasi

### 12. Final Verification
- [ ] Semua konflik memiliki Source of Truth
- [ ] Semua perubahan memiliki alasan
- [ ] `original/` tetap tidak berubah
- [ ] Dokumen hasil revisi dipisahkan dari original
- [ ] Cross-reference diperbarui
- [ ] Tidak ada konflik lama yang tersisa
- [ ] Architecture kembali diaudit setelah PRD/Core Contract berubah
- [ ] Implementation kembali diaudit setelah architecture berubah

## Output Audit
Audit final harus menghasilkan:

```text
audit/
├── audit-checklist.md
├── consistency-report.md
├── source-of-truth.md
└── change-plan.md
```

Setelah itu barulah dokumen hasil revisi dibuat di lokasi terpisah dari `original/`.
