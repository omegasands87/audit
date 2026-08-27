# Phase 5 — Step 4: Lifecycle & Cross-Domain Corrections

## Status
**COMPLETE — WORKING SPECIFICATION**

## Guardrails
- Tidak mengubah konsep website.
- Tidak mengubah Final Business Decision Register.
- `original/` immutable.
- Setiap domain mempertahankan ownership masing-masing.

## 1. Order → Payment → Fulfillment

Ketiganya adalah state boundary berbeda:

```text
Order
  ↓
Payment
  ↓
Fulfillment
  ↓
Entitlement / Delivery
```

- Order memiliki order lifecycle.
- Payment memiliki financial settlement lifecycle.
- Fulfillment memiliki delivery/grant lifecycle.
- Satu domain tidak boleh menulis state authority domain lain secara langsung.

## 2. Payment → Entitlement

Payment yang berhasil menjadi input untuk proses grant/fulfillment sesuai aturan produk.

Entitlement authority tetap memiliki:
- grant;
- reservation;
- consumption;
- release;
- reversal.

Payment tidak boleh menyimpan entitlement sebagai state authority.

## 3. Reservation / Commit / Release / Reversal

```text
AVAILABLE
   ↓ reserve
RESERVED
   ├─ commit → CONSUMED
   └─ release → AVAILABLE

CONSUMED
   ↓ approved reversal
REVERSED
```

Semua operasi harus idempotent dan memiliki audit trail.

## 4. Refund

Refund adalah financial operation milik Payment authority.

Jika refund berdampak pada entitlement yang sudah diberikan:

```text
Payment refund
      ↓ event/command boundary
Entitlement reversal
```

Refund tidak langsung memodifikasi entitlement database.

## 5. Provider Failure / Ambiguous Success

Provider timeout tidak boleh dianggap otomatis sebagai payment failure.

```text
REQUESTED
   ↓
PROVIDER_PENDING
   ├─ confirmed success → PAID
   ├─ confirmed failure → FAILED
   └─ ambiguous → RECONCILIATION_REQUIRED
```

Reconciliation harus idempotent dan tidak menggandakan grant.

## 6. Events / Retry / DLQ / Replay

- Domain state transition adalah authority domain.
- Event menyampaikan fakta perubahan.
- Retry tidak boleh menyebabkan duplicate side effect.
- Setelah retry exhaustion, event masuk DLQ.
- Replay harus aman/idempotent.
- Consumer harus menyimpan idempotency/deduplication key sesuai kebutuhan.

## 7. Content Production Lifecycle

Canonical handoff:

```text
Content Slot
   ↓
Blueprint
   ↓
Asset Preparation / Generation
   ↓
Editor
   ↓
Export
```

Ownership tetap terpisah dari command/request boundary.

- Content Context owns Content Slot.
- Planner owns planning decisions.
- Blueprint/production authority owns production artifacts/state.
- Editor owns editing state.
- Export owns export job/output state.

## 8. Research → Analyzer

```text
Research Source / Evidence
          ↓
       Analyzer
          ↓
Derived Interpretation / Result
```

Research tetap canonical source/evidence authority. Analyzer tidak menciptakan source of truth kedua.

## 9. Workspace → Tenant

Workspace adalah operational/content context.
Tenant adalah organizational/white-label isolation boundary.

Workspace tidak boleh digunakan sebagai pengganti tenant isolation.

## 10. Storage / Purge Recovery

Storage lifecycle harus memisahkan:
- active object;
- deletion requested;
- deletion processing;
- purged;
- purge failed/retryable.

Business domains tidak boleh menganggap purge sukses hanya karena delete request dikirim.

## 11. Time Authority

Platform menggunakan UTC sebagai canonical persisted/system time. UI melakukan localization untuk presentation.

## 12. Delivery vs Read State

Delivery status dan read status adalah dua state berbeda.

Contoh:
- delivered ≠ read;
- sent ≠ acknowledged.

## 13. Privacy vs Financial/Audit Retention

Data deletion/privacy policy tidak boleh secara otomatis menghapus financial records atau audit evidence yang masih diwajibkan untuk retention.

## 14. Acceptance Criteria

- Tidak ada cross-domain direct state mutation yang melanggar ownership.
- Payment/Order/Fulfillment terpisah.
- Entitlement reversal dapat ditelusuri ke financial event.
- Provider ambiguity dapat direkonsiliasi tanpa duplicate grant.
- Event retry/replay idempotent.
- Production pipeline memiliki state/handoff yang jelas.
- Research tetap source authority.
- Workspace dan Tenant tidak conflated.
- Storage purge memiliki failure state.
- UTC menjadi time authority.
- Delivery/read state terpisah.
- Privacy deletion tidak merusak retention wajib.

## Non-Changes

Tidak ada perubahan terhadap product concept, business model, pricing, atau user journey yang sudah final.
