# Phase 5 — Step 5: Registry / Index & Implementation Synchronization

## Status
**COMPLETE — WORKING SPECIFICATION**

## Guardrails
- Tidak mengubah konsep website.
- Tidak mengubah Final Business Decision Register.
- `original/` tetap immutable.
- Registry/index hanya menjadi discovery dan governance layer; semantic authority tetap berada pada domain owner.

## Canonical Registry Rules

### Capability Registry
Mencatat capability identifier, domain owner, availability/scope, dan referensi kontrak. Tidak menentukan entitlement.

### Permission Registry
Mencatat permission identifier dan authorization owner. Tidak memberikan commercial access.

### Configuration Registry
Mencatat key, schema, scope, version, default/effective value, dan owner. Tidak menjadi business-rule authority.

### State Machine Index
Mengindeks state machine canonical per domain dan menunjuk dokumen authority-nya. Tidak menduplikasi semantic rules.

### Event Catalog
Mengindeks event name, producer, consumer, payload reference, version, dan delivery/retry policy. Domain state tetap authority.

### API Registry
Mengindeks endpoint, owner, contract version, auth requirement, dan lifecycle. API registry tidak menggantikan API contract.

### Entity Ownership Registry
Mengindeks setiap persistent entity dan authoritative owner. Tidak membuat ownership baru.

## Implementation Synchronization Rules

1. Implementation hanya menggunakan approved domain authority.
2. Tidak boleh membuat entity/service duplicate untuk konsep yang sudah memiliki owner.
3. API implementation harus mengikuti API contract.
4. State implementation harus mengikuti canonical state machine.
5. Event implementation harus mengikuti event catalog dan producer ownership.
6. Configuration key harus terdaftar sebelum digunakan lintas domain.
7. Cross-domain write harus melalui command/event boundary yang telah disetujui.
8. Provider adapter tidak boleh memiliki business-state ownership.
9. Vertical slice harus menunjuk source authority untuk setiap domain dependency.
10. Jika implementation menemukan ambiguity, build harus berhenti pada boundary tersebut dan mengacu ke SoT; jangan mengarang behavior.

## Vertical Slice Minimum Traceability

Setiap slice harus dapat menunjuk:

```text
Business Decision
 → PRD requirement
 → Domain owner
 → Contract
 → State/Lifecycle
 → API/Event
 → UI behavior
 → Operational behavior
 → Acceptance criteria
```

## Non-Changes

Registry tidak menambah capability baru, tidak mengubah monetization, tidak mengubah user journey, dan tidak mengubah domain ownership.

## Acceptance Criteria

- Semua canonical concepts dapat ditemukan melalui registry/index.
- Tidak ada registry yang menjadi semantic authority secara tidak sengaja.
- Implementation references authority yang benar.
- Tidak ada duplicate ownership.
- Setiap vertical slice memiliki traceability lengkap sebelum build.
