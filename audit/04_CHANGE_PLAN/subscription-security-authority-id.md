# Phase 5 — Step 3: Subscription & Security Authority Corrections

## Status
**COMPLETE — WORKING SPECIFICATION**

## Guardrails
- No change to website concept.
- Existing Final Business Decision Register remains authoritative.
- `original/` remains immutable.

## Subscription Authority

### Ownership
`Subscription` is a dedicated authoritative domain entity.

### Responsibilities
Subscription authority owns:
- subscription identity;
- account/member association;
- lifecycle state;
- start/end dates;
- cancellation intent and effective end;
- renewal/reactivation state where applicable;
- commercial status needed by entitlement evaluation.

Product/Membership authority owns product/package definition and commercial offer. Entitlement authority owns granted/consumed/reversed usage rights.

### Canonical Lifecycle

```text
ACTIVE
  ├─ cancel → CANCELLED_PENDING_END
  │              └─ end date → EXPIRED
  └─ renewal/reactivation → ACTIVE
```

No document may infer entitlement solely from subscription status without applying the approved commercial and authorization rules.

### Existing Business Decisions Preserved
- Existing cancellation behavior remains unchanged.
- Existing package locking behavior remains unchanged.
- Existing inactive-subscription purchase restriction remains unchanged.

## Security & Content Protection Authority

A dedicated technical specification is required because the audited source set contains business policy but lacks a single technical implementation authority.

### Scope
The technical specification must define:
- authentication/session security boundary;
- authorization enforcement;
- tenant isolation;
- security-sensitive configuration;
- content protection controls;
- protected-content defaults;
- threat assumptions;
- logging/audit requirements;
- incident/recovery expectations;
- acceptance criteria.

### Existing Policy That Must Not Change
Content protection remains:
- default ON;
- a deterrence/protection mechanism;
- not an absolute OS-level guarantee.

### Security Boundary

```text
Authentication
    ↓
Authorization / Permission
    ↓
Entitlement / Commercial access check
    ↓
Tenant isolation
    ↓
Domain operation
```

Security controls may reject an operation but must not silently redefine business ownership.

## Configuration Security

Configuration owns:
- key;
- schema;
- scope;
- version;
- effective value.

The consuming domain owns:
- business meaning;
- validation;
- enforcement;
- state transition.

Security-sensitive configuration requires explicit approval/actor separation and cannot override authorization, tenant isolation, or security boundaries.

## Acceptance Criteria

- Subscription has one authoritative owner.
- Subscription lifecycle is explicit.
- Product, Subscription, and Entitlement are not conflated.
- Security policy is separated from technical implementation detail.
- Content protection promise is not overstated.
- Configuration cannot bypass security boundaries.
- All downstream references use the canonical terminology.

## Non-Changes

No new business model, monetization rule, feature, or user journey is introduced by this correction.
