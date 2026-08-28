# Operations / Deployment Acceptance Specification — Phase 6

## Status
**FINAL VERIFIED**

## Environment Boundary
Development, staging and production are separated. Production secrets/configuration are never committed to repository. Deployment artifacts are versioned and auditable.

## Migration / Rollback
Every schema migration must have: owner, forward migration, compatibility window, validation, rollback/forward-fix decision, backup prerequisite and acceptance test. Destructive migration requires explicit approval and tested recovery path.

## Worker / Queue Recovery
Each asynchronous job defines timeout, retry limit/backoff, idempotency key, terminal failure state, DLQ behavior and replay procedure. Replay preserves original event/job identity and cannot duplicate irreversible business effects.

## Provider Reconciliation
Ambiguous provider results enter reconciliation. Operators must be able to inspect provider reference, internal attempt, expected amount/currency, observed status and resolution outcome before fulfillment/entitlement is committed.

## Storage Recovery
Storage lifecycle is REQUESTED → PROCESSING → PURGED/PURGE_FAILED. Purge failure is retryable and must not delete the parent business record. Retention policies are authoritative and auditable.

## Backup / DR
Required measurable targets must be explicitly configured before production release:
- RPO: maximum tolerated data loss window, approved per environment.
- RTO: maximum tolerated restoration window, approved per environment.
- Restore acceptance: restore into isolated environment, verify schema consistency, application boot, authentication, representative business record integrity, event/outbox consistency and storage-object references.

No production release is accepted until RPO/RTO values are approved and a restore test passes.

## Observability
Platform minimum: structured logs, request ID, correlation ID, event ID, operation, duration, outcome and failure classification. Domain metrics remain owned by domain services. Secrets, passwords and private credentials are never logged.

## Privacy / Retention
Deletion/anonymization workflows must respect mandatory financial and audit retention. Retained records must be access-controlled and excluded from ordinary user-visible datasets where applicable.

## UTC / Time
UTC is canonical platform time. User timezone is presentation/business-calendar context. Persist timestamps with unambiguous timezone semantics.

## Operational Acceptance Gates
- deployment rollback/recovery tested;
- migration validation tested;
- worker retry/DLQ/replay tested;
- provider reconciliation tested;
- storage purge retry tested;
- backup restore tested;
- observability correlation verified;
- security-sensitive configuration approval audited;
- privacy deletion/retention exception tested.
