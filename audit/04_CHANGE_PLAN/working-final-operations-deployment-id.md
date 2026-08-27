# Final Operations / Deployment Specifications — Working Correction Set

## Status
CONTROLLED WORKING DOCUMENT — PHASE 5

## Operational rules
- Environment separation follows architecture.
- Schema/migration follows domain ownership.
- Workers follow event/command ownership; retries/replay are idempotent and exhausted work uses DLQ.
- Provider ambiguity enters reconciliation.
- Storage deletion has explicit failure/retry states.
- Secrets are not plaintext in repository.
- Observability includes structured logs, correlation IDs, domain/event IDs and failure classification.
- Backup/restore requires measurable RPO/RTO acceptance criteria.
- Privacy deletion respects mandatory financial/audit retention.
- UTC is canonical platform time.
