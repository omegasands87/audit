# Security & Content Protection — Authoritative Technical Specification

## Status
**FINAL VERIFIED**

## Authority
Final Business Decision Register → Phase 4 Source of Truth → this technical specification → implementation specifications.

## Scope
Authentication/session enforcement, server-side authorization, tenant isolation, security-sensitive configuration, content protection, auditability, secrets handling and operational security controls.

## Ownership
- Identity owns authentication/session state.
- Authorization owns role/permission semantics and server-side decisions.
- Tenant/Platform owns tenant isolation boundary.
- Configuration owns configuration values but cannot bypass security enforcement.
- Storage owns protected object lifecycle.
- Security/Platform owns cross-cutting controls, threat assumptions and verification.

## Threat Assumptions
- Client/browser is untrusted.
- API requests may be replayed.
- Provider callbacks may be duplicated or delayed.
- Credentials and secrets may leak if logged or committed.
- Cross-tenant identifiers may be intentionally substituted.
- Protected media may be copied by a determined user; content protection is deterrence, not absolute OS-level protection.

## Mandatory Controls
1. Authorization is server-side for every protected operation.
2. Permission checks include resource/workspace/tenant scope where applicable.
3. Commercial entitlement is checked separately from authorization.
4. Tenant isolation is enforced server-side and cannot be disabled through ordinary configuration.
5. Security-sensitive configuration requires controlled approval and actor separation.
6. Secrets are never stored in repository plaintext or emitted into logs.
7. Provider callbacks require authentication/integrity validation and idempotent processing.
8. Retryable privileged operations are idempotent.
9. Audit records are immutable from normal application paths and include actor, action, target, before/after where applicable, reason, timestamp and correlation identity.
10. Content Protection defaults ON according to locked business policy; protected area only receives the approved focus-loss behavior.
11. No implementation may claim absolute screen/OS-level capture prevention.
12. Privacy deletion is separate from mandatory financial/audit retention.

## Security-Sensitive Configuration
Changes to security policy, tenant isolation, provider credentials, content protection defaults or privileged access configuration require authenticated privileged actor, explicit permission, validation, audit trail and rollback/version visibility.

## Acceptance Criteria
- Unauthorized direct API access is rejected.
- Cross-tenant resource substitution is rejected.
- Permission and commercial entitlement are evaluated independently.
- Duplicate privileged callback does not duplicate business effect.
- Secrets are absent from repository and structured logs.
- Security-sensitive configuration changes are audited.
- Protected content behavior matches the locked business policy.
- Security controls cannot be bypassed by feature flags/configuration.
- Retained financial/audit records remain governed after privacy deletion.

## Limitation
Content protection is a deterrence/protection mechanism and not an absolute guarantee against OS-level capture or determined extraction.
