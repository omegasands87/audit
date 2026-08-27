# Core Contract #7 — Storage & File Lifecycle

## Status

**Draft for Core Design — based on the finalized PRD, Final Business Decision Register, and Core Contracts #1–#6**

This contract defines the shared Storage Service responsible for:

```text
File
→ Metadata
→ Ownership
→ Availability
→ Retention
→ Download
→ Purge
```

The storage layer must distinguish:

```text
Project / Business Data
```

from:

```text
Binary / Media File
```

A file may be purged without deleting the project or content lifecycle that referenced it.

---

# 1. Scope

This contract covers:

- storage object;
- file metadata;
- storage provider abstraction;
- client-side vs server-side boundary;
- upload;
- commit/finalization;
- export;
- download;
- retention;
- auto-purge;
- purge jobs;
- support attachments;
- export files;
- editor media;
- storage manager;
- ownership;
- content_slot attribution;
- access control;
- integrity;
- audit;
- future storage monetization readiness.

It does not define:

- Editor business logic;
- Product/Pricing;
- Order/Payment;
- Content lifecycle;
- full White-label storage behavior.

---

# 2. Source Principles

The finalized storage policy establishes two distinct retention domains:

```text
Content Export / Editor Media
→ 48 hours

Support Ticket Attachment
→ 90 days after ticket Closed
```

The finalized billing direction also prepares future storage monetization as a Product/Entitlement capability, but storage monetization is not required for the current implementation phase.

The PRD establishes Storage & Auto-Purge as shared platform infrastructure used by the three main engines.

---

# 3. Core Principle — File ≠ Project

This is the most important storage invariant.

```text
Content Slot
├── Planner Data
├── Research
├── Analyzer
├── Script / Blueprint
├── Editor Metadata
├── Export Record
└── Binary File
```

Auto-Purge may remove:

```text
Binary File
```

while preserving:

```text
Content Slot
Project Metadata
Script
Blueprint
Export Metadata
History
```

Therefore:

> **Purging a file must never automatically delete the project lifecycle that references it.**

---

# 4. Storage Object

Conceptual entity:

```text
StorageObject
```

Minimum fields:

| Field | Purpose |
|---|---|
| `storage_object_id` | unique identity |
| `owner_user_id` | owner |
| `tenant_id` | future tenant reference |
| `content_slot_id` | production lifecycle reference |
| `object_type` | export / editor_media / support_attachment / etc. |
| `file_name` | user-facing name |
| `mime_type` | file type |
| `size_bytes` | file size |
| `storage_key` | internal object reference |
| `checksum` | integrity |
| `status` | lifecycle state |
| `available_at` | availability timestamp |
| `retention_expires_at` | purge eligibility |
| `created_at` | creation |
| `updated_at` | update |
| `purged_at` | deletion timestamp |

---

# 5. Object Type

Minimum:

```text
export
editor_media
support_attachment
```

Future:

```text
research_attachment
system_asset
tenant_asset
```

Each object type has its own retention policy.

---

# 6. Storage Lifecycle

Recommended lifecycle:

```text
CREATED
   ↓
UPLOADING
   ↓
AVAILABLE
   ↓
EXPIRING
   ↓
PURGE_PENDING
   ↓
PURGED
```

Possible error state:

```text
PURGE_FAILED
```

A storage object can only be downloaded while it is available.

---

# 7. Created

Object metadata exists but binary may not yet be fully available.

Example:

```text
Export Job
→ creates StorageObject
→ status = CREATED
```

---

# 8. Uploading

Binary transfer is in progress.

```text
CREATED
→ UPLOADING
```

If upload fails:

```text
UPLOAD_FAILED
```

The object must not become publicly/privately downloadable until integrity is verified.

---

# 9. Available

Binary is successfully stored and validated.

```text
status = AVAILABLE
```

Available objects may be:

- downloaded;
- previewed;
- referenced by an active workflow;
- displayed in Storage Manager.

---

# 10. Expiring

An object can be classified as `EXPIRING` for UI/operational purposes as it approaches retention expiry.

This status does not itself delete the file.

Example:

```text
Available
→ less than configured warning threshold
→ Expiring
```

The exact warning threshold is configurable.

---

# 11. Purge Pending

Once:

```text
now >= retention_expires_at
```

the object becomes eligible for purge.

It enters:

```text
PURGE_PENDING
```

so the background purge worker can process it safely.

---

# 12. Purged

After the binary is permanently removed:

```text
status = PURGED
purged_at = timestamp
```

The StorageObject metadata may remain for audit/history.

After purge:

```text
Download
→ unavailable
```

---

# 13. Client-side vs Server-side Boundary

The platform uses a client-first principle for media processing.

Media uploaded in Asset Preparation may remain:

```text
Browser / Client
```

and must not be sent to server storage unless it is intentionally committed into a server-backed workflow.

---

# 14. Client-side Processing

Examples:

```text
crop
resize
trim preview
text placement preview
filter preview
temporary background removal preview
live editor preview
```

These do not automatically create persistent server storage objects.

---

# 15. Server-side Commit

A file enters server storage when a workflow explicitly requires persistence.

Examples:

```text
Member brings media into Editor
Final export produced
Support attachment submitted
System requires durable asset reference
```

This action creates or finalizes a `StorageObject`.

---

# 16. Upload Commit

Recommended flow:

```text
Client Upload
    ↓
Temporary / Upload Session
    ↓
Binary Transfer
    ↓
Integrity Validation
    ↓
Commit
    ↓
StorageObject = AVAILABLE
```

Do not treat a partially uploaded binary as a valid stored asset.

---

# 17. Upload Integrity

Validate at minimum:

```text
MIME type
file size
checksum
upload completeness
ownership
```

Where possible, validate actual file signature rather than trusting only the browser-provided file extension/MIME declaration.

---

# 18. Ownership

Every stored object must have an ownership boundary.

Minimum:

```text
owner_user_id
```

Future:

```text
tenant_id
```

For support attachments:

```text
support_ticket_id
```

For production assets:

```text
content_slot_id
```

Ownership must be evaluated before download or manipulation.

---

# 19. Content Slot Attribution

Production-related files should reference:

```text
content_slot_id
```

Example:

```text
Content Slot CS-001

├── Editor Media A
├── Editor Media B
├── Export MP4
└── Export PNG
```

This allows the system to trace files back to the content lifecycle.

---

# 20. Support Ticket Attribution

Support attachments should reference:

```text
support_ticket_id
```

and:

```text
uploader_user_id
```

This lets Support identify the exact ticket context without relying on filenames.

---

# 21. Retention Policy

Final policy:

## Content Export / Editor Media

> **48 hours**

## Support Attachment

> **90 days after ticket Closed**

These are independent retention domains.

---

# 22. Content Export Retention

Countdown starts when the exported file becomes successfully available for download:

```text
Export Job
   ↓
Export Complete
   ↓
File Available
   ↓
Retention Clock Starts
   ↓
48 hours
   ↓
Eligible for Purge
```

Do not start the clock when:

- Editor opens;
- content slot is created;
- rendering starts;
- upload begins.

---

# 23. Support Attachment Retention

Support attachment retention begins only after ticket closure:

```text
Ticket Active
→ attachment retained

Ticket Closed
→ retention clock starts

90 days
→ eligible for purge
```

Therefore an active/open ticket does not lose its evidence simply because it is old.

---

# 24. Retention Clock Does Not Reset on Download

Downloading a file does not reset retention.

Similarly:

```text
Open Storage Manager
→ no reset

Preview
→ no reset

Download
→ no reset
```

The retention deadline remains deterministic.

---

# 25. Retention Clock Does Not Reset on Editor Activity

Opening or modifying a project does not automatically extend the deadline of an old exported file.

Example:

```text
Export A
→ 48-hour timer

User opens project after 40 hours
→ Export A deadline unchanged
```

A new export creates a new storage object / retention cycle.

---

# 26. Dependency Protection

Auto-purge must not delete a binary that is still a live dependency of an active server process.

Examples:

```text
Rendering
Export
Upload Finalization
Async Processing
```

If the file is required by a job that has not completed:

```text
Purge Eligible
→ Hold
```

until the dependency is safely removed.

---

# 27. Storage Object vs Active Job

Recommended references:

```text
StorageObject
└── active_job_references
```

or equivalent dependency tracking.

Purge worker should verify:

```text
no active dependency
```

before deletion.

---

# 28. Download Authorization

Download requires:

```text
Authenticated User
+
Permission
+
Ownership / Scope
+
StorageObject Available
```

The server must validate authorization before issuing a download URL or stream.

---

# 29. Signed / Temporary Download URLs

When object storage supports it, use short-lived signed URLs.

The URL should:

- expire;
- be scoped to the object;
- not grant broader storage access.

The Storage Service remains the authorization boundary.

---

# 30. Direct Object Storage

Core may use an object storage backend rather than storing large binaries directly in the application database.

Conceptual:

```text
Application Database
→ metadata

Object Storage
→ binary
```

This keeps the core scalable.

The exact storage provider is an architecture decision.

---

# 31. Storage Provider Abstraction

Similar to Provider Pool:

```text
Storage Service
      ↓
Storage Adapter
      ↓
Object Storage
```

Possible future providers:

```text
AWS S3-compatible
Cloudflare R2
Other Object Storage
```

The domain should not hard-code one provider.

---

# 32. Storage Key

Use non-guessable object keys.

Example conceptual:

```text
tenant/user/object-id/random-key
```

Never expose predictable filesystem paths as authorization.

---

# 33. Filename Safety

User-provided filenames are display metadata only.

Storage keys must not directly trust:

```text
../../../file
```

or other path-like input.

Normalize/sanitize display names separately from internal storage keys.

---

# 34. File Type Policy

Each object type has allowed formats.

Examples:

```text
Support Attachment:
PDF / PNG / JPG

Export:
according to export policy

Editor Media:
supported media formats
```

Validation occurs server-side at the storage boundary.

---

# 35. File Size Policy

Object type may define max file size.

Example finalized Support policy:

```text
Support Attachment
→ 2 MB / file
```

Other media limits may be configurable per product/engine.

Storage Service enforces the final server-side limit.

---

# 36. Storage Manager

The Storage Manager provides visibility into server-side retained files.

For export objects:

```text
Thumbnail
File Name
Type
Size
Related Content
Created / Available At
Retention Deadline
Countdown
Status
Download
```

Support attachments should remain primarily visible through Support rather than being mixed into member export storage.

---

# 37. Storage Manager vs Support Attachments

Do not combine all binary files into one undifferentiated user-facing list.

Recommended:

```text
Export & Storage Manager
→ Export / Editor storage

Support Center
→ Support attachments
```

The backend may share the same Storage Service.

---

# 38. Storage Metadata Persistence

After binary purge, metadata may remain:

```text
storage_object_id
content_slot_id
object_type
created_at
purged_at
status
```

This allows:

- audit;
- history;
- support;
- analytics;
- reconciliation.

Metadata persistence does not imply binary recovery.

---

# 39. Purge Job

Auto-purge runs as a background job.

Flow:

```text
Retention Deadline Reached
        ↓
Purge Scheduler
        ↓
PURGE_PENDING
        ↓
Dependency Check
        ↓
Delete Binary
        ↓
Mark PURGED
```

---

# 40. Purge Idempotency

If the purge job runs twice:

```text
First:
binary deleted
status = PURGED

Second:
detect already purged
→ no-op
```

Purging must be idempotent.

---

# 41. Purge Failure

If deletion fails:

```text
PURGE_FAILED
```

The system should:

- record error;
- increment retry count;
- retry according to policy;
- preserve metadata;
- never delete the project.

---

# 42. Purge Retry

Recommended:

```text
Retry 1
Retry 2
Retry 3
...
```

with backoff.

Maximum retry policy is configurable.

---

# 43. Purge Audit

Record:

```text
object_id
object_type
retention_deadline
purge_started_at
purged_at
status
error
retry_count
```

System-generated events should include:

```text
StorageObjectEligibleForPurge
StoragePurgeStarted
StoragePurged
StoragePurgeFailed
```

---

# 44. User Notification

For export retention, UI must show countdown.

Recommended warning levels:

```text
Normal
→ remaining time

Warning
→ nearing expiry

Critical
→ very near expiry
```

The final countdown wording is UI policy.

The binary retention deadline remains server-authoritative.

---

# 45. Expired File Behavior

After purge:

```text
Storage Status:
Purged / Expired
```

User should not receive a broken download URL.

UI should clearly state:

> File is no longer available because it exceeded its storage retention period.

Project history remains accessible where applicable.

---

# 46. File Recovery

After successful permanent purge:

> **No recovery is promised.**

Recovery mechanisms, if ever introduced, belong to a separate backup/recovery policy.

---

# 47. Storage and Project Lifecycle

Example:

```text
Content Slot
Status:
Exported

Export File:
PURGED

Content Slot:
still exists

Script:
still exists

Blueprint:
still exists

Editor Metadata:
still exists
```

This is a required core invariant.

---

# 48. Storage and Entitlement

Generating an export may involve:

```text
Entitlement
→ consumed

Storage
→ file created
```

These are separate state transitions.

A storage failure after successful generation must not silently consume entitlement twice.

The generation/entitlement transaction boundary is handled by the relevant domain service.

---

# 49. Storage and Editor

Editor may reference:

```text
StorageObject
```

while the file is available.

If the binary is purged:

```text
Editor Metadata
→ retained

Missing Binary
→ clearly reported
```

The system must not pretend that a purged file is still available.

---

# 50. Storage and Support

Support attachments follow:

```text
2 MB / file
PDF / PNG / JPG
90 days after Closed
```

When ticket is still:

```text
Open
In Progress
Resolved
```

attachments remain retained.

After Closed + 90 days:

```text
eligible for purge
```

---

# 51. Storage and Security

Storage access must use the authorization system from Core Contract #2.

Examples:

```text
storage.download.own
storage.download.assigned
storage.manage.all
```

A file's URL does not replace authorization.

---

# 52. Storage and Tenant

Future White-label:

```text
StorageObject
→ tenant_id
```

Tenant-aware authorization must prevent:

```text
Tenant A
→ Tenant B file
```

The tenant foundation is prepared now but full White-label behavior is future.

---

# 53. Storage Quota / Capacity

The core should support capacity tracking:

```text
used_bytes
reserved_bytes
available_bytes
```

This is infrastructure capacity, not the same as AI generation entitlement.

Future paid storage can use Product/Entitlement to define:

```text
storage_bytes_limit
storage_retention
```

without redesigning the Storage Service.

---

# 54. Future Storage Monetization

The current member policy remains:

```text
Export retention = 48 hours
```

Future packages may extend storage:

```text
Storage Package
→ longer retention
→ larger capacity
```

Example concept:

```text
30-day Storage
90-day Storage
Expanded Storage
```

These are future products, not current business decisions.

---

# 55. Storage Retention Policy Resolution

Future retention can be resolved through:

```text
Product / Entitlement
      ↓
Storage Policy
      ↓
StorageObject.retention_expires_at
```

Once the object gets its retention deadline:

> that deadline becomes the authoritative object-level value.

A later change to product policy must not silently rewrite historical retention deadlines unless an explicit migration policy exists.

---

# 56. Upload Session

For larger uploads, core may use upload sessions:

```text
Create Upload Session
→ Receive Upload URL
→ Upload
→ Validate
→ Commit
```

Session fields may include:

```text
upload_session_id
user_id
object_type
expected_size
expected_mime
status
expires_at
```

---

# 57. Upload Cancellation

Incomplete upload sessions can be cancelled or expire automatically.

Incomplete temporary binaries should be cleaned by a separate temporary-object cleanup job.

---

# 58. Temporary Storage

Temporary files must have a separate lifecycle from retained objects.

Example:

```text
Temporary
→ Uploading
→ Failed / Abandoned
→ Cleanup
```

Do not confuse temporary cleanup with the 48-hour export retention rule.

---

# 59. Storage Integrity

Use checksums where practical:

```text
checksum
checksum_algorithm
```

This supports:

- upload verification;
- integrity checks;
- duplicate detection if later enabled;
- troubleshooting.

---

# 60. Duplicate Detection

Duplicate binary detection is **not required for P0**.

The core may support checksum storage so that duplicate detection can be added later without redesigning metadata.

---

# 61. Virus / Malware Scanning

For user-uploaded server-side files, the architecture should allow an optional security scanning step:

```text
Upload
→ Scan
→ Accept / Reject
→ Available
```

This is especially relevant for Support attachments and future public/tenant uploads.

Exact scanner/provider is not defined by current business decisions.

---

# 62. Storage Events

Core events:

```text
StorageObjectCreated
StorageUploadStarted
StorageObjectAvailable
StorageObjectFailed
StorageObjectExpiring
StorageObjectPurgePending
StorageObjectPurged
StoragePurgeFailed
StorageDownloadRequested
```

Events may feed:

- Audit;
- Analytics;
- Notification;
- Operations;
- Support.

---

# 63. Storage API

Conceptual internal API:

```text
createObject(metadata)
createUploadSession(request)
commitUpload(session)
getObject(id)
getDownloadUrl(id)
checkAccess(id, context)
markAvailable(id)
schedulePurge(id)
purge(id)
getStorageUsage(context)
```

---

# 64. Storage Manager API

Conceptual:

```text
GET /storage
GET /storage/:id
GET /storage/:id/download
```

The server must enforce:

```text
ownership
scope
status
retention
```

before issuing a download.

---

# 65. Admin Storage API

Conceptual:

```text
GET /admin/storage
GET /admin/storage/usage
GET /admin/storage/objects/:id
POST /admin/storage/objects/:id/purge
GET /admin/storage/purge-jobs
```

Manual purge actions require appropriate Admin permission and audit.

---

# 66. Manual Purge

Admin may need controlled manual purge for:

- security;
- legal/compliance;
- abuse;
- storage emergency.

Manual purge:

```text
Admin Request
→ Permission Check
→ Confirmation
→ Purge
→ Audit
```

Manual purge should not silently delete project metadata.

---

# 67. Storage Access Errors

Recommended:

```text
STORAGE_OBJECT_NOT_FOUND
STORAGE_OBJECT_NOT_AVAILABLE
STORAGE_OBJECT_PURGED
STORAGE_ACCESS_DENIED
STORAGE_UPLOAD_INVALID
STORAGE_UPLOAD_TOO_LARGE
STORAGE_UPLOAD_FAILED
STORAGE_PURGE_FAILED
STORAGE_RETENTION_EXPIRED
```

---

# 68. Core Invariants

```text
1. File binary and project data are separate.

2. Purging a binary never automatically deletes the project.

3. Content export retention is 48 hours.

4. Export retention starts when file becomes available.

5. Support attachment retention is 90 days after ticket Closed.

6. Download does not reset retention.

7. Editor activity does not reset an old export's retention.

8. Active processing dependencies prevent purge.

9. Purge is idempotent.

10. Purge failure does not delete project metadata.

11. Storage access is authorization-controlled.

12. Storage credentials/provider details are not exposed to members.

13. Historical storage metadata can remain after binary purge.

14. Purchased storage/retention packages, if introduced later,
    are separate Product/Entitlement concepts.

15. Tenant-aware storage is core-ready but full White-label is future.

16. Temporary uploads have a lifecycle separate from retained files.
```

---

# 69. Definition of Done

Core Contract #7 is complete when:

1. Storage objects are modeled separately from project data.
2. Client-side and server-side boundaries are defined.
3. Upload sessions are supported.
4. Upload integrity is validated.
5. Ownership is defined.
6. Content Slot attribution is supported.
7. Support Ticket attribution is supported.
8. Export retention is 48 hours.
9. Support attachment retention is 90 days after Closed.
10. Retention starts at the correct event.
11. Countdown is based on server retention deadline.
12. Download does not reset retention.
13. Active jobs can protect dependencies.
14. Auto-purge is background-job based.
15. Purge is idempotent.
16. Purge failures are retryable.
17. Binary purge does not delete project metadata.
18. Storage authorization is server-side.
19. Temporary uploads are cleaned independently.
20. Object storage provider can be abstracted.
21. Storage usage can be measured.
22. Future paid storage can map through Product/Entitlement.
23. Tenant-aware storage is core-ready.
24. Audit events exist for sensitive storage operations.

---

# 70. Dependencies

Depends on:

```text
Core Contract #1
Identity

Core Contract #2
Role / Permission

Core Contract #3
Configuration

Core Contract #4
Product / Entitlement
```

May interact with:

```text
Core Contract #5
Order / Payment

Core Contract #6
Provider / Integration
```

Used by:

```text
Asset Preparation
Editor
Export
Support
Analytics
Admin Godmode
Future White-label
Future Storage Products
```

---

# 71. Next Contract

The next recommended contract is:

> **Core Contract #8 — Audit, Events & Notifications**

It should define the platform-wide event/audit foundation used by:

```text
Identity
Role / Permission
Configuration
Product
Order / Payment
Provider
Storage
Support
Referral
Analytics
Future Tenant
```

This will give the core a common mechanism for:

```text
Event
→ Audit
→ Queue / Worker
→ Notification
→ Domain Reaction
```
