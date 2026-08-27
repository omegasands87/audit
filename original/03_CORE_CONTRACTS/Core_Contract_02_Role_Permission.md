# Core Contract #2 — Role & Permission

## Status

**Draft for Core Design — based on finalized PRD and Final Business Decision Register**

Dokumen ini mendefinisikan kontrak core untuk:

```text
Role
Permission
Scope
Role Assignment
Authorization
Admin Authority
```

Contract ini dibangun di atas:

> **Core Contract #1 — Identity, User & Session**

Identity menjawab:

> "Siapa user ini?"

Role & Permission menjawab:

> "Apa yang boleh dilakukan user ini?"

---

# 1. Scope

Contract ini mencakup:

- role;
- permission;
- permission set;
- role assignment;
- permission scope;
- role hierarchy/inheritance;
- membership vs role;
- admin/super admin;
- authorization decision;
- authorization API;
- audit requirements;
- configuration boundaries;
- future tenant-aware authorization.

Tidak mencakup:

- authentication/session;
- product;
- entitlement;
- billing;
- payment;
- full white-label;
- feature implementation.

---

# 2. Source Principles

PRD menetapkan **Role & Permission** sebagai bagian dari Shared Platform Infrastructure yang dipakai oleh Research Intelligence, Content Engine, dan Analytics Engine. Role dan provider/infra dikelola terpusat melalui Admin. fileciteturn12file0L7-L12

PRD juga menetapkan bahwa Admin dapat membuat role baru tanpa batas dan menyediakan matrix granular untuk akses modul, kuota, export, multi-workspace, serta konfigurasi default Analyzer per role. fileciteturn12file3L87-L94

Final Business Decisions menetapkan pemisahan:

```text
Membership
→ entitlement / package / product benefit

Role
→ permission / access control
```

Pemisahan ini adalah invariant arsitektur.

---

# 3. Core Principle — Role ≠ Membership

Jangan menggabungkan:

```text
Starter
Growth
Agency
```

dengan:

```text
Admin
Support
Finance
Content Manager
Super Admin
```

Model:

```text
User
├── Membership
│     └── Product / Entitlement Benefit
│
└── Role(s)
      └── Permission(s)
```

Contoh:

```text
User A
Membership = Growth
Role = Content Manager
```

User lain:

```text
User B
Membership = Starter
Role = Support Staff
```

Membership dapat berubah tanpa otomatis mengubah system permission.

Role dapat berubah tanpa otomatis mengubah membership.

---

# 4. Role Entity

## 4.1 Minimum Role Entity

```text
Role
```

| Field | Purpose |
|---|---|
| `role_id` | immutable role identity |
| `name` | display/administrative name |
| `slug` | stable machine-readable identifier |
| `description` | purpose of role |
| `role_type` | classification |
| `status` | active / inactive / archived |
| `is_system_role` | protects built-in roles |
| `is_assignable` | whether Admin can assign it |
| `created_at` | timestamp |
| `updated_at` | timestamp |

---

# 5. Role Type

Role type is a classification aid, not a fixed permission model.

Suggested types:

```text
Member
Admin
Staff
Agency
Internal
Custom
```

Admin can create custom roles.

Preset roles are templates, not structural limits.

---

# 6. Unlimited Custom Roles

The core must support:

> **Admin dapat membuat role baru tanpa batas praktis.**

Examples:

```text
Creator Pro
Content Manager
Research Analyst
Support Staff
Finance Viewer
Finance Manager
Operations
Agency Manager
Read-only Analyst
Custom Member
```

These are examples only.

Admin is not restricted to these names or categories.

---

# 7. Permission Entity

```text
Permission
```

Minimum fields:

| Field | Purpose |
|---|---|
| `permission_id` | immutable identifier |
| `resource` | target resource/domain |
| `action` | operation |
| `scope_type` | ownership/data scope |
| `description` | human-readable meaning |
| `status` | active/inactive |
| `created_at` | timestamp |

Canonical permission should be expressed conceptually as:

```text
resource + action + scope
```

Example:

```text
support_ticket.view.assigned
finance_transaction.view.all
planner.edit.own
```

---

# 8. Permission Actions

Minimum action vocabulary:

```text
View
Create
Edit
Delete
Execute
Approve
Export
Manage
Configure
```

Additional actions may be added later when a domain requires them.

Examples:

```text
planner.generate
analyzer.execute
payment.approve
refund.approve
provider.configure
product.manage
```

---

# 9. Permission Resource

Permissions should map to domain/resource concepts.

Examples:

```text
User
Session
Role
Research
Planner
Analyzer
Script
Asset
Editor
Storage
Analytics
Support
Referral
Product
Entitlement
Order
Payment
Finance
Provider
Configuration
FeatureFlag
Tenant
```

The resource catalog should be extensible.

---

# 10. Permission Scope

Scope determines **which data** the permission applies to.

Minimum scope types:

```text
Own
Assigned
Team
Workspace
Tenant
All
```

Examples:

```text
support_ticket.view.assigned
finance_transaction.view.all
content_slot.edit.own
workspace.view.workspace
```

Not every resource needs every scope.

The authorization service must validate that the scope is valid for the resource.

---

# 11. Own Scope

Example:

```text
planner.edit.own
```

User can modify only records owned by that user.

---

# 12. Assigned Scope

Example:

```text
support_ticket.view.assigned
```

Support staff can access tickets explicitly assigned to them.

---

# 13. Team Scope

Example:

```text
support_ticket.view.team
```

A manager may access tickets belonging to their support team.

---

# 14. Workspace Scope

Used for future multi-workspace behavior:

```text
content.view.workspace
```

User can access content inside the active workspace.

---

# 15. Tenant Scope

Prepared for White-label:

```text
product.view.tenant
customer.view.tenant
order.view.tenant
```

This is core-ready only.

Full White-label implementation is outside the current phase.

---

# 16. All Scope

Administrative access:

```text
user.view.all
finance_transaction.view.all
```

Only roles explicitly granted this scope can use it.

---

# 17. Permission Set

A `Permission Set` groups permissions into reusable bundles.

Example:

```text
Support Basic
├── support_ticket.view.assigned
├── support_ticket.reply.assigned
└── support_ticket.status.update.assigned
```

Another:

```text
Finance Operations
├── finance_transaction.view.all
├── payment.view.all
├── refund.create.all
└── referral_payout.view.all
```

Permission Sets simplify Role Builder.

---

# 18. Role-Permission Relationship

Relationship:

```text
Role
   ↓
Role Permissions
   ↓
Permission
```

One role may have many permissions.

One permission may belong to many roles.

---

# 19. Role Inheritance

Core should be designed to support optional inheritance.

Example:

```text
Support Staff
      ↓
Support Manager
```

Support Manager inherits Support Staff permissions and adds:

```text
assignment.manage
support_ticket.view.team
support_ticket.priority.manage
```

Role inheritance is recommended as P1 if not required for initial vertical slice.

---

# 20. Multiple Roles per User

Core should support multiple roles:

```text
User X
├── Content Manager
├── Support Staff
└── Finance Viewer
```

Effective permissions are the union of permissions from assigned roles, subject to deny/safety rules defined by authorization policy.

For normal member UX, the interface can still show a primary membership/role label.

---

# 21. Deny vs Allow

Recommended default:

> **Explicit Allow permissions are required; absence of permission means Deny.**

Use:

```text
Default = Deny
```

Do not rely on "hidden UI" as authorization.

Backend must enforce permission.

If explicit Deny is introduced later, conflict resolution must be deterministic.

---

# 22. Safety / Non-delegable Permissions

Some system capabilities should not be freely delegated even by normal Admin roles.

Examples:

```text
system.audit.delete
security.secret.read_plaintext
super_admin.assign
```

These should be protected by system-level rules.

Admin flexibility must not override core security invariants.

---

# 23. Super Admin

`Super Admin` is a protected system role.

It may have access to:

- role architecture;
- permission architecture;
- security configuration;
- payment credentials;
- provider configuration;
- product catalog;
- finance;
- global configuration;
- user management.

Super Admin is not simply "Admin + more checkboxes"; it is the highest protected authority boundary.

---

# 24. Admin

`Admin` is not necessarily full system authority.

Its permissions are explicitly assigned.

Example:

```text
Admin
├── product.manage.all
├── user.manage.all
├── support.manage.all
└── finance.view.all
```

but no:

```text
security.secrets.manage
```

unless explicitly granted and permitted by system safety rules.

---

# 25. Staff Roles

Examples:

```text
Support Staff
Finance Staff
Content Staff
Research Staff
Operations Staff
```

Each is built from permissions, not hard-coded business behavior.

---

# 26. Role Assignment

User-role relationship:

```text
User
   ↓
User Role Assignment
   ↓
Role
```

Assignment fields should include:

| Field | Purpose |
|---|---|
| `assignment_id` | unique assignment |
| `user_id` | target user |
| `role_id` | assigned role |
| `assigned_by` | actor |
| `assigned_at` | timestamp |
| `expires_at` | optional temporary role |
| `status` | active/revoked |

---

# 27. Temporary Role

Core may support temporary roles:

```text
Role
→ assigned_at
→ expires_at
```

Example:

```text
Emergency Support Access
valid:
2 hours
```

When expired:

```text
Assignment
→ inactive
```

Permission disappears automatically.

---

# 28. Role Change Behavior

When role changes:

```text
Old Role
   ↓
Role Removed / Replaced
   ↓
New Role
   ↓
Authorization Re-evaluated
```

No new login should be required unless the security policy explicitly requires it.

---

# 29. Membership Change Behavior

Membership changes:

```text
Starter
→ Growth
```

must not automatically grant system permissions unless business policy explicitly maps membership to a role.

Membership primarily changes:

```text
Entitlements
Packages
Feature Availability
```

Role primarily changes:

```text
Permissions
Access
```

---

# 30. Analyzer Role Configuration

PRD supports role-specific Analyzer defaults such as:

- default AI count 1/2/3;
- default result display;
- Deep Source Intelligence included/not included;
- Media Intelligence included/not included;
- Cross-Source Analysis included/not included;
- max source count. fileciteturn11file0L9-L15

These are **entitlement/configuration properties**, not generic permission actions.

Therefore:

```text
Role
├── Permissions
└── Default Entitlement / Feature Configuration
```

The detailed Entitlement Contract will define the second branch later.

---

# 31. Authorization Decision

Every protected API action should produce an authorization decision:

```text
ALLOW
or
DENY
```

Inputs:

```text
user_id
session_id
resource
action
resource_id
scope
tenant_id (future)
```

Conceptually:

```text
AuthorizationService.check(...)
```

---

# 32. Authorization Flow

```text
API Request
    ↓
Authenticate Session
    ↓
Resolve User
    ↓
Resolve Roles
    ↓
Resolve Permissions
    ↓
Resolve Resource
    ↓
Evaluate Scope
    ↓
Safety Rules
    ↓
ALLOW / DENY
```

UI hiding is optional UX.

Authorization must occur server-side.

---

# 33. Resource Ownership Evaluation

Example:

```text
User A
permission = planner.edit.own

Request:
Edit Content Slot X
```

Authorization service checks:

```text
Content Slot X.owner_id == User A
```

If yes:

```text
ALLOW
```

If no:

```text
DENY
```

---

# 34. Assigned Ticket Evaluation

Example:

```text
support_ticket.view.assigned
```

System checks:

```text
ticket.assigned_to == user_id
```

Only then:

```text
ALLOW
```

---

# 35. Tenant-aware Evaluation

Future White-label:

```text
product.view.tenant
```

System checks:

```text
resource.tenant_id == session.tenant_id
```

Tenant mismatch:

```text
DENY
```

This tenant check is a security boundary, not simply a UI filter.

---

# 36. Cross-tenant Access

By default:

> A role scoped to a tenant cannot access another tenant.

Global access requires an explicit privileged scope such as:

```text
resource.view.all
```

and appropriate Admin/Super Admin authority.

---

# 37. Permission Resolution

Recommended resolution:

```text
1. Validate session
2. Load active role assignments
3. Load active roles
4. Resolve role inheritance
5. Resolve permissions
6. Resolve scope
7. Apply safety/system restrictions
8. Return ALLOW / DENY
```

No client-provided permission claim should be trusted as the sole authorization source.

---

# 38. Authorization Cache

Authorization results may be cached for performance, but cache invalidation is mandatory after:

- role change;
- role assignment change;
- permission change;
- user suspension/block;
- security policy change.

A stale cache must not allow access beyond current permission.

---

# 39. Permission Change Propagation

If Admin edits a role:

```text
Role Permission Updated
      ↓
Existing Users with Role
      ↓
Effective Permission Recomputed
```

No manual per-user update should be necessary.

---

# 40. Audit Requirements

The following must be auditable:

```text
Role Created
Role Updated
Role Archived
Permission Changed
Role Assigned
Role Revoked
Temporary Role Expired
Authorization-sensitive Admin Action
```

Audit fields should include:

```text
actor_user_id
target_user_id
role_id
permission_id
before
after
timestamp
reason
session_id
```

---

# 41. Authorization Audit

Not every successful `View` request needs a full audit record.

However, sensitive decisions should be auditable.

Examples:

```text
Refund
Payment Approval
Role Change
Super Admin Assignment
Provider Credential Change
Security Configuration
```

A security/audit policy can determine what is sampled vs fully logged.

---

# 42. API Contract

Core authorization API should provide a central boundary.

Conceptually:

```text
POST /authz/check
```

or internal service call:

```text
AuthorizationService.check(
    subject,
    resource,
    action,
    scope,
    resource_id
)
```

Domain services should not duplicate permission rules independently.

---

# 43. Role API

Conceptual administrative endpoints:

```text
GET    /admin/roles
POST   /admin/roles
GET    /admin/roles/:id
PATCH  /admin/roles/:id
POST   /admin/roles/:id/archive

GET    /admin/permissions
GET    /admin/permission-sets

POST   /admin/users/:id/roles
DELETE /admin/users/:id/roles/:roleId
```

Exact REST paths may be refined in API Architecture.

---

# 44. Permission API

Permission catalog should be system-defined or controlled by privileged Admin architecture.

Admin should normally **compose roles from available permissions**, not invent arbitrary executable permission strings that have no backend implementation.

Therefore:

```text
Admin can create arbitrary ROLE
but
Permission capabilities come from the CORE
```

This mirrors the finalized product rule:

> Admin can create new products as long as the capability already exists in core.

The same principle applies to authorization:

> Admin can create new roles as long as the permission capability exists in core.

---

# 45. Admin Role Builder UX Contract

Godmode should provide:

```text
Create Role
    ↓
Role Details
    ↓
Permission Matrix
    ↓
Scope
    ↓
Optional Role Inheritance
    ↓
Default Feature / Entitlement Mapping
    ↓
Review
    ↓
Save
```

Admin should see warnings for high-risk permissions.

---

# 46. High-risk Permission Classification

Permissions may carry risk metadata:

```text
Low
Medium
High
Critical
```

Examples:

```text
support_ticket.reply → Low
refund.create → High
role.assign → High
payment.configure → Critical
security.configure → Critical
super_admin.assign → Critical
```

This supports future approval workflows.

---

# 47. Permission Bundles / Presets

Godmode may provide presets:

```text
Support Basic
Support Manager
Finance Viewer
Finance Manager
Content Manager
Research Analyst
Operations
Read-only Analyst
```

Presets are convenience features.

The resulting role still stores explicit permissions.

---

# 48. Role Archive

A role that has active users should generally be:

```text
Archived
```

rather than hard-deleted.

Existing historical role assignments remain auditable.

New assignment to archived role is blocked.

---

# 49. Role Deletion Safety

Hard delete may only be allowed when:

```text
no active assignments
no historical dependency requiring preservation
```

Otherwise:

```text
Archive
```

is preferred.

---

# 50. Permission Deactivation

If a permission capability becomes obsolete:

```text
Permission
→ Inactive
```

Existing roles referencing it must be handled safely.

The system must never silently convert a newly-restricted permission into access.

Default fallback:

```text
Inactive Permission
→ Deny
```

---

# 51. Authorization Error Contract

Recommended errors:

```text
AUTH_REQUIRED
PERMISSION_DENIED
SCOPE_DENIED
TENANT_ACCESS_DENIED
ROLE_INACTIVE
ACCOUNT_SUSPENDED
ACCOUNT_BLOCKED
```

User-facing wording is localized through i18n.

Do not expose sensitive authorization implementation details.

---

# 52. Security Invariants

The following are mandatory:

```text
1. Default permission = Deny.

2. Backend authorization is authoritative.

3. Hidden UI is not security.

4. Membership does not automatically grant system permissions.

5. Tenant scope cannot cross tenant boundary.

6. Revoked/inactive roles cannot grant permission.

7. Archived roles cannot receive new assignments.

8. Permission changes propagate to users using the role.

9. Critical permissions remain subject to system safety rules.

10. Audit history cannot be silently rewritten.
```

---

# 53. Core Contract Boundary

This contract owns:

```text
Role
Permission
Permission Set
Scope
Role Assignment
Authorization Decision
Admin Authority
```

This contract does not own:

```text
Membership Benefit
Product
Entitlement
Payment
Tenant Business Rules
```

Those belong to downstream contracts.

---

# 54. Definition of Done

Core Contract #2 is complete when:

1. Admin can create custom roles.
2. Role count is not structurally limited.
3. Permissions are granular by resource/action.
4. Scope is supported.
5. Permission Sets can compose reusable permissions.
6. Role assignment is auditable.
7. Membership and Role remain separate.
8. Multiple roles can be supported.
9. Role inheritance can be supported.
10. Authorization is evaluated server-side.
11. Default behavior is Deny.
12. Resource ownership can be evaluated.
13. Assigned-scope access can be evaluated.
14. Tenant scope is core-ready.
15. Role changes propagate.
16. Inactive roles cannot grant access.
17. High-risk permissions are identifiable.
18. Audit hooks exist.
19. Authorization has a stable API/service boundary.
20. Role Builder can be implemented without hard-coding specific role names.

---

# 55. Core Invariants

```text
USER
  ↓
ROLES
  ↓
PERMISSIONS
  ↓
SCOPE
  ↓
AUTHORIZATION DECISION
```

And separately:

```text
USER
  ↓
MEMBERSHIP
  ↓
ENTITLEMENTS
```

These two paths must remain separate.

Final invariant:

> **Admin bebas membuat role baru dan menyusun role dari capability permission yang tersedia, tetapi Admin tidak dapat menciptakan capability backend yang belum ada hanya dengan konfigurasi role.**

---

# 56. Dependencies

Depends on:

```text
Core Contract #1
Identity / User / Session
```

Will be used by:

```text
Configuration
Product
Entitlement
Billing
Support
Referral
Storage
Analytics
Provider
Tenant / White-label
```

---

# 57. Next Contract

The next recommended contract is:

> **Core Contract #3 — Configuration**

It should define the centralized configuration system needed by:

- Admin Godmode;
- Product Catalog;
- Feature Flags;
- Payment Provider;
- AI/Data Provider;
- Support SLA;
- Storage policy;
- i18n;
- market/currency policy.

This keeps business configuration out of hard-coded domain logic.
