# Phase 5 — Step 2: Authority & Terminology Corrections

## Status
**COMPLETE — WORKING CORRECTION SPECIFICATION**

## Scope
Step ini menormalkan authority dan terminology berdasarkan Phase 4 Source-of-Truth. Tidak mengubah konsep website maupun keputusan bisnis final.

## Canonical Rules

| Konsep | Aturan final |
|---|---|
| Role | Authorization identity/assignment |
| Permission | Authorization capability |
| Membership / Product | Commercial access basis |
| Entitlement | Hak/manfaat yang dimiliki/dapat digunakan |
| Agency Mode | Commercial membership/product mode |
| Agency Role | Authorization melalui role/permission |
| Product | Sesuatu yang dapat dijual |
| Order | Purchase/order state |
| Payment | Financial settlement state |
| Fulfillment | Delivery/grant execution state |
| Research | Canonical source/evidence truth |
| Analyzer | Analysis run + derived interpretation |
| Workspace | Operational content context |
| Tenant | Organizational/White-label boundary |
| Content Slot | Owned by Content Context |
| Planner | Planning decisions; requests changes through approved boundary |
| Subscription | Authoritative subscription entity/lifecycle |

## Mandatory Correction Rules

1. Tidak boleh ada dokumen downstream yang menyatakan Role sebagai sumber entitlement komersial.
2. Tidak boleh ada dokumen yang menyamakan Agency Mode dengan System Role.
3. Tidak boleh ada dua canonical Research Source/Evidence models.
4. Analyzer tidak menjadi owner canonical Research Source/Evidence.
5. Order, Payment, dan Fulfillment tidak boleh direpresentasikan sebagai satu state machine tunggal.
6. Content Slot tetap dimiliki Content Context; Planner tidak mengambil ownership.
7. Workspace tidak disamakan dengan Tenant.
8. Provider infrastructure tidak mengambil business-state ownership dari consuming domain.
9. Configuration tidak menjadi universal business-logic owner dan tidak dapat override authorization/security/tenant isolation.
10. Satu persistent business entity memiliki satu authoritative owner.

## Status/Authority Normalization

Dokumen yang masih Draft/Baseline tidak boleh dirujuk sebagai Approved/Final authority sebelum benar-benar disetujui.

`Final Business Decision Register` tetap menjadi authority tertinggi untuk locked business decisions.

## Non-Changes

- Tidak mengubah product vision.
- Tidak menghapus fitur.
- Tidak mengubah pricing/business model.
- Tidak mengubah existing user journeys kecuali wording yang diperlukan untuk menghilangkan contradiction.
- Tidak mengedit `original/`.

## Verification Gate

Setelah koreksi downstream dibuat, lakukan pencarian lintas seluruh working/final documents untuk istilah berikut dan verifikasi konteksnya:

`role entitlement`, `agency mode role`, `research source`, `analyzer source`, `order payment`, `payment fulfillment`, `workspace tenant`, `content slot planner`, `configuration authorization`.

**Step 2 selesai secara administratif; penerapan ke Final Documents dilakukan pada correction sequence sesuai urutan Phase 5.**
