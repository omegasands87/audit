# Core Contract #1 — Identity, User & Session

## Status
**Draft for Core Design — based on the finalized PRD and Final Business Decision Register**

## 1. Scope

This contract defines the first shared-core boundary:

```text
Identity
   ↓
User
   ↓
Session
```

It covers:
- stable user identity;
- account lifecycle;
- authentication boundary;
- session lifecycle;
- single-login policy;
- logout/revocation;
- basic security/audit hooks;
- market/language/currency references;
- downstream references required by Membership, Role, Billing, Referral, Support, Analytics and future Tenant systems.

It does **not** yet define full Role & Permission, Product, Entitlement, Order, Payment, Provider, Storage, Notification, or White-label behavior. Those are subsequent contracts.

## 2. PRD Basis

The PRD places **Auth** inside Shared Platform Infrastructure used by Research Intelligence, Content Engine and Analytics Engine. fileciteturn12file0L7-L12

The PRD also requires persisted work to survive page changes, refresh, logout, and later login, so identity must remain stable across sessions. fileciteturn12file2L58-L66

Final business decisions establish:
- one account = one active session;
- a new login immediately revokes the previous session;
- language follows market;
- membership and role are separate concepts;
- purchased packages remain owned but are locked when subscription is inactive.

## 3. Core Principles

### 3.1 Stable Identity

Use an immutable internal identifier:

```text
user_id
```

It must not depend on email, display name, membership, role, device, or market.

### 3.2 Authentication ≠ Authorization

```text
Authentication
    ↓
User Identity
    ↓
Session
    ↓
Authorization
```

Core Contract #1 answers **who is this?** and **is this session valid?**. Contract #2 answers **what may this user do?**

### 3.3 Membership ≠ Role

```text
User
├── Membership → product / entitlement benefits
└── Role       → permissions
```

Example:

```text
Membership: Growth
Role: Content Manager
```

## 4. User Entity

Minimum conceptual fields:

| Field | Purpose |
|---|---|
| `user_id` | immutable internal identity |
| `email` | login/contact identity |
| `email_verified_at` | verification state |
| `password_hash` | credential reference if password auth is used |
| `display_name` | user-facing name |
| `avatar_url` | profile image reference |
| `status` | Active / Suspended / Blocked |
| `market` | market context |
| `language` | user language preference |
| `currency` | preferred/display currency |
| `created_at` | creation time |
| `updated_at` | last update |
| `last_login_at` | last successful login |
| `last_login_session_id` | last session reference |

Exact auth-provider fields remain implementation-specific.

## 5. User ID

`user_id` must be:
- unique;
- immutable;
- non-semantic;
- independent of email;
- safe as a relational reference.

## 6. Email

If email is the login identifier:
- normalize it;
- enforce uniqueness;
- track verification state;
- changing email must not create a new `user_id`.

## 7. Account Status

Minimum states:

```text
Active
Suspended
Blocked
```

When a user becomes Suspended or Blocked, active usable sessions must be revoked.

## 8. Account Lifecycle

```text
Created
   ↓
Active
   ↓
Suspended / Blocked
   ↓
Reactivated
```

Reactivation does not restore old sessions; the user authenticates again.

## 9. Session Entity

Minimum conceptual fields:

| Field | Purpose |
|---|---|
| `session_id` | unique session identifier |
| `user_id` | session owner |
| `created_at` | creation time |
| `last_activity_at` | latest activity |
| `expires_at` | expiry |
| `status` | Active / Expired / Revoked |
| `revoked_at` | revocation time |
| `revoked_reason` | reason |
| `device_fingerprint` | device/session context |
| `user_agent` | browser context |
| `ip_address` | security context |

Sensitive session tokens must not be stored in plaintext.

## 10. Single-Login Rule

Final business rule:

> **One account may have only one active session.**

Example:

```text
Device A → Session A = Active
Device B login
    ↓
Session A = Revoked
Session B = Active
```

## 11. New Login Flow

```text
Login Request
    ↓
Authenticate
    ↓
Validate User Status
    ↓
Find Existing Active Session
    ↓
Revoke Existing Session
    ↓
Create New Session
    ↓
Return Authenticated Context
```

The backend must never leave two `Active` sessions for one user.

## 12. Concurrency Invariant

The single-login rule must be enforced atomically so simultaneous login requests cannot produce:

```text
Session A = Active
Session B = Active
```

The core invariant is:

```text
COUNT(sessions WHERE user_id = X AND status = Active) <= 1
```

## 13. Logout

```text
Active Session
   ↓
Logout
   ↓
Revoked
```

Logout does not delete the User.

## 14. Forced Session Revocation

Sessions may be revoked because of:
- new login;
- user logout;
- Admin action;
- account suspension/block;
- security incident;
- credential reset.

Every revocation stores a reason.

## 15. Session Expiration

Sessions may expire according to the Security Architecture policy:

```text
Active → Expired
```

Exact duration is not fixed by the PRD and should remain configurable by security policy.

## 16. Authenticated Context

Every authenticated request should resolve to a context similar to:

```text
AuthenticatedContext
├── user_id
├── session_id
├── account_status
├── membership_id
├── role_context
└── tenant_context (future/core-ready)
```

`role_context` and `tenant_context` are references for later contracts.

## 17. Membership Reference

Identity should reference the user's membership without embedding billing logic:

```text
User
  ↓
Membership Reference
```

Membership/Entitlement rules are defined later.

## 18. Market / Language / Currency

Final business decisions:
- language follows market by default;
- Indonesia and English are core-ready;
- more languages can be added later;
- currency is configurable per market;
- IDR and USD are minimum core-ready currencies.

These are references to i18n and Billing contracts, not identity logic.

## 19. Future Tenant Context

White-label is core-ready but not being built now.

Identity should therefore be able to carry a future tenant reference without implementing the full white-label product:

```text
User
├── user_id
└── tenant_id (future / nullable in current platform)
```

Detailed tenant rules belong to the White-label/Core Architecture contract.

## 20. Authentication Events

Core should emit:

```text
UserCreated
UserActivated
UserSuspended
UserBlocked
LoginSucceeded
LoginFailed
SessionCreated
SessionRevoked
SessionExpired
Logout
```

These may be consumed by Audit, Security, Notification and operations analytics.

## 21. Audit Requirements

Identity/session actions must be auditable. Minimum conceptual fields:

```text
audit_id
actor_user_id
target_user_id
action
timestamp
source
ip
session_id
metadata
```

Example:

```text
Actor: Admin A
Action: BLOCK_USER
Target: User B
Before: Active
After: Blocked
Reason: Security policy
```

## 22. Security Events

Keep security events distinct and queryable:

```text
security.login_success
security.login_failure
security.session_revoked
security.new_device_login
security.account_blocked
security.account_unblocked
```

## 23. Permission Boundary

Detailed permissions are deferred to Core Contract #2. Identity already assumes authorization exists for privileged operations.

Examples:

```text
User → logout self
Admin → may revoke another user's session
Super Admin → may manage privileged security policy
```

## 24. API Contract — Initial Boundary

Conceptual initial endpoints:

```text
POST /auth/login
POST /auth/logout
POST /auth/refresh
GET  /me
PATCH /me
GET  /me/sessions
POST /me/sessions/revoke
```

Exact endpoint naming can change during API architecture, but the capability boundary should remain stable.

## 25. Login Response

Conceptual shape:

```json
{
  "user": {
    "user_id": "…",
    "email": "…",
    "status": "active"
  },
  "session": {
    "session_id": "…",
    "expires_at": "…"
  }
}
```

Do not expose secrets.

## 26. Error Categories

Stable categories should include:

```text
INVALID_CREDENTIALS
ACCOUNT_SUSPENDED
ACCOUNT_BLOCKED
SESSION_EXPIRED
SESSION_REVOKED
EMAIL_UNVERIFIED
RATE_LIMITED
AUTH_REQUIRED
```

Localized user-facing messages belong to i18n.

## 27. Validation

Minimum server-side checks:
- normalized/valid email;
- uniqueness when email is the login identity;
- valid account status;
- valid session ownership;
- valid session state;
- authorization before privileged session actions.

## 28. Rate Limiting

Rate limiting is required for authentication-sensitive operations such as:

```text
Login
Password Reset
Verification
Session Creation
```

Numeric thresholds remain a Security Architecture decision.

## 29. Login Attempt Tracking

Conceptual record:

```text
attempt_id
identifier
user_id (if resolved)
timestamp
success/failure
reason
ip
user_agent
```

## 30. Deactivation Behavior

When account status changes to Suspended or Blocked:

```text
Account Status Change
        ↓
Revoke Active Session
        ↓
Emit Security Event
        ↓
Audit
```

## 31. Re-activation

When account becomes Active:

```text
Previously Revoked Sessions remain Revoked
```

The user must authenticate again.

## 32. Credential Reset

Credential reset should:
- invalidate affected sessions according to security policy;
- emit security/audit events;
- require re-authentication where appropriate.

Detailed reset/MFA policy belongs to Security Architecture.

## 33. Admin Impersonation

Full "login as user" is not part of this contract. If added later it must require explicit permission, reason, audit, visible privileged context, automatic expiry and session isolation.

## 34. Definition of Done

Core Contract #1 is complete when the core supports:

- stable immutable `user_id`;
- account lifecycle;
- authentication;
- session creation;
- one active session per user;
- immediate prior-session revocation on new login;
- logout;
- session expiry;
- forced session revocation;
- suspended/blocked behavior;
- market/language/currency references;
- audit/security events;
- current-user API;
- basic session API;
- server-side validation;
- concurrency-safe single-login invariant.

## 35. Core Invariants

```text
1. user_id is immutable.
2. One user has at most one Active session.
3. Revoked/Expired sessions cannot authenticate requests.
4. Suspended/Blocked users cannot retain usable active sessions.
5. Authentication and authorization remain separate.
6. Membership does not define system permissions.
7. Identity/session audit history is not silently rewritten.
8. Identity references remain stable across membership, role, billing,
   referral, support, analytics and future tenant relationships.
```

## 36. Dependencies

Core Contract #1 is foundational to:

```text
Role & Permission
Membership
Product / Entitlement
Billing
Referral
Support
Analytics
Storage
Audit
Notification
Tenant / White-label
```

## 37. Next Contract Boundary

The next document should be:

> **Core Contract #2 — Role & Permission**

It will define:

```text
Role
Permission
Permission Set
Role Assignment
Permission Scope
Role Inheritance
Membership vs Role
Admin / Super Admin
Authorization API
Audit
```

## 38. Summary

The first core boundary is intentionally small:

```text
IDENTITY
   ↓
USER
   ↓
SESSION
```

with the critical invariant:

```text
ONE USER = ONE ACTIVE SESSION
```

Everything else is built on top of this foundation.
