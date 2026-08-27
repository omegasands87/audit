# Phase 5 — Steps 6–7: UI/Design & Operations Synchronization

## Status
**COMPLETE — WORKING SPECIFICATION**

## Guardrails
- Konsep website tidak berubah.
- Final Business Decisions tidak berubah.
- `original/` immutable.

# STEP 6 — UI / Design Synchronization

## Canonical UI Rules

1. Terminology UI mengikuti canonical domain terminology.
2. Role/Permission UI hanya merepresentasikan authorization.
3. Membership/Product/Entitlement UI merepresentasikan commercial access dan benefit.
4. Agency Mode tidak direpresentasikan sebagai System Role.
5. Research adalah source/evidence; Analyzer adalah derived interpretation.
6. Workspace tidak direpresentasikan sebagai Tenant.
7. Content Slot ownership tetap pada Content Context; Planner hanya planning authority.

## State-driven UI

UI wajib membedakan:
- loading;
- empty;
- success;
- validation error;
- permission denied;
- entitlement unavailable;
- provider pending/ambiguous;
- retryable failure;
- terminal failure;
- destructive confirmation;
- processing/background state.

Tidak boleh ada UI yang menampilkan operasi berhasil sebelum authoritative backend state mengonfirmasi keberhasilan.

## API / Domain Traceability

Setiap UI action harus dapat ditelusuri ke:

```text
UI Action
 → API/Command
 → Domain Owner
 → State Transition
 → Event (jika ada)
 → UI State
```

## Responsive / Accessibility

Design implementation harus mempertahankan:
- responsive behavior;
- keyboard/focus behavior;
- readable validation/error states;
- destructive-action confirmation;
- semantic labels;
- disabled/loading state yang jelas.

## Production UI

Content Slot → Blueprint → Asset → Editor → Export harus memiliki state dan handoff yang konsisten dengan lifecycle authority.

---

# STEP 7 — Operations Synchronization

## Deployment

Environment separation tetap dipertahankan. Deployment tidak boleh mengubah domain ownership.

## Database / Migration

Migration mengikuti schema ownership domain dan tidak membuat duplicate authoritative tables untuk entity yang sama.

## Queue / Worker

Worker harus mengikuti event/command ownership. Retry dan replay harus idempotent; exhausted jobs masuk DLQ sesuai operational policy.

## Provider Operations

Provider timeout/ambiguous response masuk reconciliation path dan tidak boleh langsung menghasilkan duplicate business effect.

## Storage

Operational runbook harus menangani active → deletion requested → processing → purged / purge failed.

## Configuration / Secrets

- Environment-specific configuration dipisahkan dari source code.
- Secret tidak disimpan sebagai plaintext dalam repository.
- Security-sensitive changes memerlukan approval/actor separation sesuai policy.
- Configuration tidak dapat bypass authorization, tenant isolation, atau security controls.

## Observability

Minimum operational observability:
- structured logs;
- request/correlation ID;
- domain/event identifiers;
- failure classification;
- queue/worker health;
- provider reconciliation visibility;
- audit trail untuk security-sensitive actions.

## Backup / Disaster Recovery

Backup dan restore harus memiliki measurable acceptance criteria, termasuk target RPO/RTO yang ditetapkan sebelum production readiness.

## Privacy / Retention

Deletion workflow harus mempertahankan records yang secara hukum/operasional masih wajib disimpan. Retention dan purge harus dapat diaudit.

## Operations Acceptance Criteria

- Deployment mengikuti architecture.
- Worker/event behavior mengikuti contracts.
- Provider failure dapat direkonsiliasi.
- Storage failure dapat dipulihkan.
- Secrets/configuration aman.
- Observability cukup untuk diagnosis.
- Backup/restore dapat diverifikasi.
- Privacy deletion tidak menghapus mandatory retention records.

## Non-Changes

Tidak ada perubahan product concept, monetization, pricing, atau user journey.
