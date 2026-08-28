# Final Contract / API Registry — Phase 6

## Status
**FINAL VERIFIED REFERENCE INDEX**

This registry is a discovery index only. Semantic authority remains in the referenced Core Contract or approved addendum.

## Contract Coverage
| Area | Authority | Required semantic elements |
|---|---|---|
| Identity / Session | Core Contract #1 | User, session, authentication, revocation, single-login |
| Role / Permission | Core Contract #2 | roles, permissions, scope, authorization |
| Configuration | Core Contract #3 | key, schema, scope, version, effective value, audit |
| Product / Pricing / Entitlement | Core Contract #4 | product, price, version, entitlement, grant/consume/lock |
| Order / Payment | Core Contract #5 | order, payment, attempts, refund, fulfillment boundary |
| Provider | Core Contract #6 | provider pool, adapter, health, credentials, reconciliation |
| Storage | Core Contract #7 | object, upload, commit, authorization, retention, purge |
| Audit / Events / Notifications | Core Contract #8 | audit, event, notification, delivery/read separation |
| Workspace / Content Context | Core Contract #9 | workspace, plan, content slot, revision, lock |
| Research | Core Contract #10 | source/evidence, provider result, research run, observations |
| Planner | Core Contract #11 | planning decisions, allocation, calendar, candidate slots |
| Analyzer | Phase 5 authoritative addendum | analysis run, derived interpretation, research-owned raw input |
| Subscription | Phase 5 authoritative addendum | subscription identity, lifecycle, renewal/reactivation |
| Analytics | Phase 5 authoritative addendum | derived measurement/analytics output |
| Asset Preparation | Phase 5 authoritative addendum | preparation/generation job, output, retry |
| Editor | Phase 5 authoritative addendum | editing state and persistence |
| Export | Phase 5 authoritative addendum | export job/output and retention handoff |
| Support | Phase 5 authoritative addendum | bounded service/ticket ownership |
| Referral | Phase 5 authoritative addendum | commission consequence only |
| Tenant / White-label | Phase 4 SoT + architecture | tenant boundary and activation constraints |
| Security / Content Protection | Phase 6 security specification | controls, threat model, content protection |

## API Registry Minimum Schema

Every API operation must have:

`operation_id, route/command, method, owner, contract_ref, actor, permission, commercial_entitlement_if_required, tenant_scope, workspace_scope_if_required, input_schema_ref, output_schema_ref, state_transition_ref, event_ref_if_any, idempotency_ref_if_required, error_codes, retryability, audit_requirement, version, acceptance_ref`.

No operation is considered implementation-ready if one of these fields is unknown. Unknown business behavior is escalated as an open decision rather than invented.

## Canonical Error Registry Categories

`VALIDATION_ERROR`, `UNAUTHENTICATED`, `PERMISSION_DENIED`, `ENTITLEMENT_REQUIRED`, `NOT_FOUND`, `VERSION_CONFLICT`, `INVALID_STATE_TRANSITION`, `IDEMPOTENCY_CONFLICT`, `PROVIDER_TIMEOUT`, `PROVIDER_AMBIGUOUS`, `RECONCILIATION_REQUIRED`, `PAYMENT_FAILED`, `STORAGE_FAILURE`, `JOB_FAILED`, `RATE_LIMITED`, `INTERNAL_ERROR`.

Each error must map to user-safe presentation, retryability, audit behavior and owning contract.

## Verification Rule

This index is complete only when every API operation in the approved implementation slices points to exactly one contract owner and every referenced contract is present in the contract coverage table.
