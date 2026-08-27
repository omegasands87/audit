# Core Contract #9 — Workspace, Content Slot & Project Context

## Status

**Draft for Core Design — based on the finalized PRD, Final Business Decision Register, and Core Contracts #1–#8**

This contract defines the shared **project context layer** that connects the platform's production workflow:

```text
Workspace
   ↓
Content Plan / Project Context
   ↓
Content Slot
   ↓
Research
   ↓
Planner
   ↓
Analyzer
   ↓
Script / Blueprint
   ↓
Asset Preparation
   ↓
Editor
   ↓
Export
   ↓
Analytics
```

The central purpose is to make `content_slot_id` a stable context anchor across the Content Engine and connected systems.

---

# 1. Scope

This contract covers:

- Workspace;
- Workspace membership;
- Project / Content Plan context;
- Content Slot;
- slot ownership;
- slot lifecycle;
- pipeline context;
- references between modules;
- persistence;
- draft/autosave;
- locking;
- concurrent editing boundaries;
- reset behavior;
- cross-session continuity;
- relationship to storage;
- relationship to analytics;
- future multi-workspace / tenant readiness.

It does not define:

- Research business logic;
- Planner business rules;
- Analyzer internals;
- Script generation;
- Asset generation;
- Editor implementation;
- Analytics scoring.

Those domains consume this context contract.

---

# 2. Source Principles

The PRD explicitly identifies the Content Engine as:

```text
Planner
→ Analyzer
→ Script Review
→ Asset Preparation
→ Editor
```

and treats Shared Platform Infrastructure as the common foundation used by all three major engines.

The PRD also requires that working results persist server-side and survive:

- page changes;
- refresh;
- logout;
- later login.

The reset behavior is independent per module, and saved work is associated with the relevant content slot/workspace context. fileciteturn12file2L58-L66

The finalized architecture therefore requires a stable context anchor that survives UI changes and sessions.

---

# 3. Core Principle — Content Slot as Anchor

The recommended primary production context is:

```text
content_slot_id
```

A Content Slot represents one planned/pipeline unit of content.

Example:

```text
Content Slot CS-00123
├── Research References
├── Selected Angle
├── Planner Metadata
├── Analyzer Results
├── Script
├── Blueprint
├── Asset Requirements
├── Editor State
├── Export Records
└── Analytics Attribution
```

This allows every module to reference the same production context without duplicating ownership relationships.

---

# 4. Workspace

## 4.1 Workspace Purpose

A Workspace is the organizational boundary in which users create and manage content.

Current normal member behavior may use one primary workspace.

Future multi-workspace behavior is core-ready.

Conceptually:

```text
User
   ↓
Workspace
   ↓
Content Slots
```

---

# 5. Workspace Entity

Minimum fields:

| Field | Purpose |
|---|---|
| `workspace_id` | stable identity |
| `name` | workspace name |
| `owner_user_id` | owner |
| `status` | active / archived |
| `created_at` | creation |
| `updated_at` | last update |

Future tenant-ready:

```text
tenant_id
```

may be attached when White-label is activated.

---

# 6. Workspace Membership

A workspace may eventually have multiple users.

Conceptual:

```text
WorkspaceMembership
```

Fields:

```text
workspace_membership_id
workspace_id
user_id
role_id / workspace_role
status
joined_at
```

Workspace membership is distinct from global System Role.

Example:

```text
User A
System Role:
Content Manager

Workspace A:
Editor

Workspace B:
Viewer
```

---

# 7. Workspace Scope

Authorization may use:

```text
scope = workspace
```

from Core Contract #2.

Example:

```text
content_slot.edit.workspace
```

means the user can edit slots inside the authorized workspace.

---

# 8. Content Plan / Project Context

A Content Plan is the planning-level grouping for content.

Depending on PRD terminology, this may represent:

```text
Calendar / Plan / Production Context
```

The exact UI representation can remain flexible.

Conceptually:

```text
ContentPlan
```

fields:

```text
content_plan_id
workspace_id
owner_user_id
name
period
status
created_at
updated_at
```

A Content Plan may contain many Content Slots.

---

# 9. Content Slot Entity

Minimum:

```text
ContentSlot
```

| Field | Purpose |
|---|---|
| `content_slot_id` | stable anchor |
| `workspace_id` | workspace |
| `content_plan_id` | planning context |
| `owner_user_id` | primary owner |
| `title` | working title |
| `platform` | target platform |
| `format` | target content format |
| `scheduled_at` | planned publication |
| `status` | content lifecycle |
| `created_at` | creation |
| `updated_at` | last update |
| `locked_at` | optional |
| `locked_by` | optional |

---

# 10. Content Status

The platform content lifecycle currently uses:

```text
Draft
→ Source Needed
→ Script Ready
→ Asset Ready
→ Editing
→ Exported
```

These states belong to Content Slot lifecycle.

They should not be replaced by Support Ticket status or Payment status.

---

# 11. Content Status vs Module State

A module can have its own state without changing the Content Slot lifecycle.

Example:

```text
Content Slot
status = Editing

Analyzer
status = Complete

Support Ticket
status = In Progress
```

These are independent state machines.

---

# 12. Content Slot Ownership

Primary ownership:

```text
owner_user_id
```

Context:

```text
workspace_id
```

Future:

```text
tenant_id
```

Every downstream artifact should be traceable back to the Content Slot where applicable.

---

# 13. Context References

Modules should prefer references:

```text
content_slot_id
```

rather than copying all previous module data.

Examples:

```text
Research Result
→ content_slot_id

Analyzer Result
→ content_slot_id

Blueprint
→ content_slot_id

Asset
→ content_slot_id

Export
→ content_slot_id

Analytics Attribution
→ content_slot_id
```

---

# 14. Research Context

Research outputs associated with a content slot can include:

```text
Research Source
Research Insight
Competitor Result
Trend Result
Keyword Result
Audience Insight
Angle
```

They should reference the relevant:

```text
content_slot_id
```

where the research is slot-specific.

General research can remain outside a slot until explicitly applied.

---

# 15. Planner Context

Planner creates or updates Content Slots.

Conceptual:

```text
Idea
   ↓
Content Plan
   ↓
Content Slot
```

Once a slot exists:

```text
content_slot_id
```

becomes the anchor for downstream production.

---

# 16. Analyzer Context

Analyzer reads context:

```text
content_slot_id
```

and writes results associated with that slot.

Examples:

```text
source
summary
angle
evidence
virality potential
visual opportunities
```

Analyzer should not create a second independent project identity.

---

# 17. Script / Blueprint Context

Script/Blueprint should reference:

```text
content_slot_id
```

and can reference:

```text
analyzer_result_id
```

where required.

Example:

```text
Content Slot
    ↓
Analyzer Result
    ↓
Blueprint
    ↓
Script
```

---

# 18. Asset Preparation Context

Asset requirements should reference:

```text
content_slot_id
```

and the relevant Blueprint/Script.

Example:

```text
Asset Slot
├── content_slot_id
├── blueprint_id
├── asset_type
├── prompt
├── source_reference
└── status
```

---

# 19. Editor Context

Editor references:

```text
content_slot_id
```

and may reference:

```text
asset_id
storage_object_id
blueprint_id
```

The Editor state should remain associated with the slot.

---

# 20. Export Context

Export record should reference:

```text
content_slot_id
storage_object_id
```

Example:

```text
Export
├── export_id
├── content_slot_id
├── storage_object_id
├── format
├── resolution
├── created_at
└── status
```

Storage retention is handled by Core Contract #7.

---

# 21. Analytics Attribution

Performance records should be attributable to:

```text
content_slot_id
```

when the published content can be mapped back to the production slot.

This supports the closed loop:

```text
Published Content
→ Analytics
→ Insight
→ Research
→ Planner
```

without creating disconnected records.

---

# 22. Content Slot State Persistence

Core requirement:

> **Content Slot state must be server-persisted.**

A page refresh must not erase:

```text
Research
Planner
Analyzer
Script
Asset
Editor
```

state.

---

# 23. Autosave

The platform should support autosave for editable workflow state.

Conceptual:

```text
User Changes
→ Draft State
→ Debounced Save
→ Server
```

UI indicator:

```text
Saved
Saving...
Saved
```

The exact debounce interval is an implementation setting.

---

# 24. Cross-session Persistence

Saved state must survive:

```text
Page Navigation
Refresh
Logout
New Login
```

as long as the underlying data is retained and authorized.

This is an explicit platform requirement. fileciteturn12file2L58-L66

---

# 25. Reset Semantics

Reset is **module-local**.

Example:

```text
Reset Trend Explorer
```

must not delete:

```text
Competitor Tracker
Analyzer
Planner
Script
```

unless the product explicitly defines a separate full-slot reset action.

---

# 26. Module Reset

Each module may have:

```text
reset()
```

but reset must respect domain boundaries.

Example:

```text
Analyzer Reset
→ removes/reset Analyzer-specific working result

Does NOT automatically:
→ delete Content Slot
→ delete Planner
→ delete Script
→ delete Assets
```

---

# 27. Full Slot Reset

If a future full reset exists:

```text
Reset Content Slot
```

it must be explicit and high-impact.

It should:

- show impact;
- require confirmation;
- audit the operation;
- preserve necessary financial/audit history.

---

# 28. Content Slot Lock

A slot may be locked for operations that should not allow concurrent edits.

Examples:

```text
Exporting
Publishing
Critical migration
Admin maintenance
```

Conceptual:

```text
lock_status
locked_by
locked_at
lock_reason
```

---

# 29. Concurrent Editing

Current normal member experience may be single-user editing.

However core should prevent silent overwrite.

If two sessions edit the same slot:

```text
Version A
Version B
```

the system should use optimistic concurrency/version checking.

---

# 30. Version / Revision

Editable entities should maintain:

```text
revision_number
```

Example:

```text
Slot Revision 10
```

Save request:

```text
expected_revision = 10
```

If current revision is already 11:

```text
VERSION_CONFLICT
```

The client must reload/merge instead of silently overwriting newer changes.

---

# 31. Draft vs Published

Content Slot is a production context.

Publication state is not identical to production status.

Example:

```text
Content Slot
status = Exported

Publication:
not yet published
```

Analytics may later attach performance data after actual publishing.

---

# 32. Content Slot Archive

A completed slot may eventually be archived.

Recommended:

```text
Active
Archived
```

Archive should not remove historical research/script/analytics records automatically.

Binary files still follow independent Storage retention.

---

# 33. Content Slot Deletion

Hard deletion should be restricted.

Prefer:

```text
Archived
```

for historical production records.

If deletion is supported:

- audit;
- confirmation;
- dependency checks;
- preservation of legally/financially required records.

---

# 34. Workspace Archive

Workspace can be:

```text
Active
Archived
```

Archived workspace should prevent new production work unless restored.

Existing content remains accessible according to policy.

---

# 35. Workspace Access

Authorization uses Core Contract #2.

Example:

```text
User
→ Workspace Membership
→ Workspace Permission
→ Content Slot Access
```

Do not rely solely on:

```text
content_slot.owner_user_id
```

when workspace collaboration is active.

---

# 36. Content Slot Access Resolution

Recommended:

```text
Authenticate
→ resolve user
→ resolve workspace membership
→ resolve role/permission
→ resolve slot ownership/scope
→ allow/deny
```

Tenant boundary, when future White-label is active:

```text
tenant_id
→ workspace_id
→ content_slot_id
```

---

# 37. Workspace vs Tenant

These must remain separate.

```text
Tenant
→ business/organizational boundary

Workspace
→ operational content space
```

For normal platform users:

```text
Tenant may equal platform master
```

For future White-label:

```text
Tenant A
├── Workspace 1
└── Workspace 2
```

---

# 38. Current Single-Workspace Behavior

Although core is multi-workspace-ready:

> The initial member experience may use one primary workspace.

The data model should not require a future rewrite when multiple workspaces are activated.

---

# 39. Context Object

Services may resolve a standardized:

```text
ProjectContext
```

containing:

```text
user_id
workspace_id
content_plan_id
content_slot_id
tenant_id
```

All fields except required current identifiers may be nullable depending on workflow.

---

# 40. Context Resolution API

Conceptual:

```text
getProjectContext(content_slot_id)
```

returns:

```text
ProjectContext
```

validated against:

- authorization;
- workspace;
- tenant;
- content status.

---

# 41. Domain Write Rule

A downstream domain should not create an unrelated duplicate context if an existing Content Slot is supplied.

Example:

```text
Analyzer
receives:
content_slot_id = CS-123
```

It writes results under:

```text
CS-123
```

rather than:

```text
AnalyzerProject-789
```

unless an explicit Analyzer-specific entity is required.

---

# 42. Domain-Specific Entities

A domain may still have its own entity:

```text
AnalyzerRun
ResearchRun
ExportJob
EditorSession
```

But it references:

```text
content_slot_id
```

where the operation belongs to a content slot.

---

# 43. Context Lifecycle Example

```text
Planner
   ↓
Create Content Slot
   ↓
content_slot_id = CS-001
   ↓
Research Applied
   ↓
Analyzer Run
   ↓
Script
   ↓
Assets
   ↓
Editor
   ↓
Export
```

The ID remains stable.

---

# 44. Context and Event System

Core Contract #8 can use:

```text
content_slot_id
```

as event metadata.

Example:

```text
AnalyzerCompleted
content_slot_id = CS-001
```

Then:

```text
ScriptGenerationRequested
content_slot_id = CS-001
```

This supports full traceability.

---

# 45. Context and Audit

Audit should record `content_slot_id` where relevant.

Example:

```text
Actor:
User A

Action:
RESET_ANALYZER

Target:
Analyzer Run

content_slot_id:
CS-001
```

---

# 46. Context and Storage

StorageObject should reference:

```text
content_slot_id
```

when production-related.

This lets Storage answer:

> "File ini milik content slot mana?"

while storage lifecycle remains independent.

---

# 47. Context and Entitlement

Feature usage may consume entitlement and include:

```text
content_slot_id
```

as consumption metadata.

Example:

```text
Image Generation
→ Entitlement Consumption
→ content_slot_id = CS-001
```

This helps cost and usage attribution.

---

# 48. Context and Payment

A purchase does not need a Content Slot.

Example:

```text
AI Image 25
→ Order
→ Entitlement
```

Only actual usage may later reference:

```text
content_slot_id
```

This keeps commercial purchase separate from production context.

---

# 49. Context and Analytics

Analytics may reference:

```text
content_slot_id
```

to close the loop.

Example:

```text
Content Slot CS-001
→ Published URL
→ Performance Record
→ Insight
```

---

# 50. Content Slot Metadata

The slot can hold stable planning metadata such as:

```text
title
platform
format
publication date
pillar
category
status
owner
workspace
```

Large result sets should remain in their domain tables, not be duplicated into Content Slot.

---

# 51. Content Slot as Orchestrator Reference

Content Slot should not become a massive "god object".

It stores:

```text
identity
ownership
workflow state
stable references
```

It should not store every:

```text
research result
AI provider response
editor timeline JSON
binary file
analytics metric
```

Those belong to their domains.

---

# 52. Data Duplication Rule

Prefer:

```text
reference
```

over:

```text
duplicate full payload
```

When a small snapshot is necessary for historical integrity, store an explicit snapshot:

```text
source_type
source_id
snapshot_version
```

---

# 53. Context Snapshot

For important production transitions, a domain may store a snapshot.

Example:

```text
Blueprint Generated
→ blueprint snapshot
```

This ensures later changes to upstream research do not silently rewrite the historical blueprint.

---

# 54. Content Slot Revision

The slot itself may have a revision:

```text
revision_number
```

but domain-specific entities can have their own versioning.

Example:

```text
Content Slot Revision 12
Analyzer Run Version 3
Script Version 5
```

Do not force every domain into one universal revision number.

---

# 55. Workspace Settings

Workspace may have non-business operational settings:

```text
timezone
default_platform
default_language
default_brand
```

These can be stored through Workspace/Configuration relationships.

Workspace settings do not replace global Configuration.

---

# 56. Workspace Branding

Future White-label may override:

```text
brand
logo
colors
domain
email identity
```

through Tenant/Workspace configuration.

Do not build full White-label branding logic into Content Slot.

---

# 57. Workspace Ownership Transfer

Admin may eventually transfer workspace ownership.

Transfer must:

- validate permission;
- preserve content;
- preserve slot IDs;
- audit;
- not change historical authorship.

---

# 58. Project Context API

Conceptual APIs:

```text
GET /workspaces
POST /workspaces
GET /workspaces/:id

GET /workspaces/:id/content-plans
POST /workspaces/:id/content-plans

GET /content-slots/:id
POST /content-slots
PATCH /content-slots/:id
POST /content-slots/:id/archive
```

Exact API paths will be refined during API Architecture.

---

# 59. Content Slot Create

Validation:

```text
user authenticated
workspace valid
workspace access allowed
content plan valid (if required)
required planning fields valid
```

Then:

```text
ContentSlotCreated
```

event.

---

# 60. Content Slot Update

Update must verify:

```text
permission
ownership/scope
revision
status
lock
```

If version conflict:

```text
VERSION_CONFLICT
```

---

# 61. Content Slot Locking

Lock actions should be auditable:

```text
LOCK
UNLOCK
```

Example:

```text
Export starts
→ Content Slot locked for export-sensitive fields

Export complete
→ unlock
```

Exact lock granularity is an implementation detail.

---

# 62. Content Slot Delete Safety

Before delete:

```text
Check dependencies
```

Potential dependencies:

```text
Research
Analyzer
Script
Asset
Editor
Export
Analytics
```

Recommended default:

> Archive instead of delete.

---

# 63. Workspace Archive Safety

Before archive:

```text
Check active operations
```

Existing content remains stored according to retention policy.

---

# 64. Search

The context layer should support search by:

```text
content_slot_id
title
workspace_id
platform
status
scheduled_at
owner_user_id
```

Search does not alter ownership/security checks.

---

# 65. Pagination

List endpoints should support:

```text
cursor/page
limit
sort
filter
```

Large workspaces should not return unlimited Content Slots in one request.

---

# 66. Sorting

Common sorting:

```text
updated_at DESC
scheduled_at ASC
created_at DESC
```

UI may select user-friendly views.

---

# 67. Context Caching

Current active project context may be cached in the client.

However:

> Server remains authoritative.

Client cache must be invalidated when:

```text
slot updated
slot archived
permission changed
workspace changed
session changes
```

---

# 68. Cross-session Recovery

User returns after logout/login:

```text
Load Workspace
→ Load Content Slots
→ Open Content Slot
→ Load persisted domain state
```

Nothing should depend solely on browser local memory.

This is aligned with the PRD's explicit persistence requirement. fileciteturn12file2L58-L66

---

# 69. Core Invariants

```text
1. content_slot_id is stable.

2. Content Slot is the primary production context anchor.

3. Workspace is separate from Tenant.

4. Membership is separate from Workspace membership.

5. System Role is separate from Workspace role.

6. Module state is separate from Content Slot state.

7. Support Ticket state never replaces Content Slot state.

8. Payment state never replaces Content Slot state.

9. Storage binary retention does not determine project retention.

10. Module reset is local unless an explicit full-slot reset exists.

11. Downstream domains reference Content Slot instead of creating
    duplicate production contexts.

12. Binary files are not embedded into Content Slot records.

13. Historical domain states may use explicit snapshots.

14. Server persistence is authoritative.

15. Concurrent edits must not silently overwrite newer revisions.

16. Tenant isolation is enforced before future White-label activation.

17. Archiving a slot does not automatically delete its historical domain data.
```

---

# 70. Definition of Done

Core Contract #9 is complete when:

1. Workspace exists.
2. Workspace membership is modeled.
3. Content Plan context is modeled.
4. Content Slot exists as a stable identifier.
5. Content Slot lifecycle is defined.
6. Content Slot ownership is defined.
7. Workspace scope is integrated with authorization.
8. Research can reference Content Slot.
9. Planner can create/update Content Slot.
10. Analyzer can reference Content Slot.
11. Script/Blueprint can reference Content Slot.
12. Asset Preparation can reference Content Slot.
13. Editor can reference Content Slot.
14. Export can reference Content Slot.
15. Analytics can reference Content Slot.
16. Storage can reference Content Slot.
17. Entitlement consumption can reference Content Slot.
18. Server-side persistence survives refresh/logout/login.
19. Autosave is supported.
20. Reset remains module-local.
21. Revision/concurrency control exists.
22. Archive is preferred over destructive delete.
23. Context can be traced through Event/Audit.
24. Multi-workspace is core-ready.
25. Tenant/White-label context is core-ready.

---

# 71. Dependencies

Depends on:

```text
Core Contract #1
Identity / User / Session

Core Contract #2
Role / Permission

Core Contract #3
Configuration

Core Contract #7
Storage

Core Contract #8
Audit / Events / Notifications
```

Integrates with:

```text
Core Contract #4
Product / Entitlement

Core Contract #5
Order / Payment

Core Contract #6
Provider / Integration
```

Used by:

```text
Research Intelligence
Planner
Analyzer
Script
Asset Preparation
Editor
Analytics
Support
Future White-label
```

---

# 72. Next Contract

The next recommended contract is:

> **Core Contract #10 — Research Intelligence Data Model & Provider Results**

This should define the persistent structures for:

```text
Research Source
Research Run
Evidence
Competitor
Trend
Keyword
Audience Insight
Mood Board
Angle
Opportunity
Claim / Citation
Research Freshness
```

It will sit above the Shared Core and become the first major **domain contract** for the Research Intelligence engine.
