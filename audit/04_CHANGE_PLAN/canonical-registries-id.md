# Phase 5 — Canonical Registries / Indexes

## Status
**APPROVED — DISCOVERY / GOVERNANCE LAYER**

Registries provide cross-domain discovery and governance only. Semantic ownership remains with the authoritative domain contract.

| Registry | Records | Semantic authority |
|---|---|---|
| Capability Registry | capability ID, owner, scope, contract ref | owning domain |
| Permission Registry | permission ID, authorization owner | Authorization / Contract #2 |
| Configuration Registry | key, schema, scope, version, owner | Configuration / Contract #3 |
| State Machine Index | state machine, domain owner, transitions ref | owning domain |
| Event Catalog | event, producer, consumer, version, retry policy | producer domain |
| API Registry | endpoint, owner, version, auth, idempotency/error ref | domain/API contract |
| Entity Ownership Registry | persistent entity, owner, lifecycle ref | owning domain |
| API Error Registry | error/code, contract, retryability, user-safe classification | API/domain contract |
| Event Partition/Aggregate Catalog | aggregate/partition key, producer | event/domain authority |

## Rules

1. Registry entries cannot create a new business rule.
2. Registry entries cannot transfer domain ownership.
3. A registry may point to a contract but cannot replace it.
4. Duplicate canonical entities are prohibited.
5. Changes to a registry require validation against its referenced authority.
6. Cross-domain consumers must reference registry identifiers consistently.

## Non-Changes
No capability, entitlement, pricing, or user journey is added or removed.
