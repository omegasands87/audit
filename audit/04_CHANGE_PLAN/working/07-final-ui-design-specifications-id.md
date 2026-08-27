# Final UI / Design Specifications — Working Correction Set

Status: **PHASE 5 CONTROLLED WORKING DOCUMENT**

## Canonical UI semantics
- Role/Permission screens represent authorization.
- Membership/Product/Entitlement screens represent commercial access/benefits.
- Agency Mode is not a role.
- Research is source/evidence; Analyzer is derived result.
- Workspace and Tenant are not interchangeable.

## State UI
Explicit states required for loading, empty, success, validation error, denied, entitlement unavailable, pending/ambiguous provider result, retryable failure, terminal failure, destructive confirmation, and background processing.

UI must not show success before authoritative backend confirmation.

## Traceability
UI action → API/Command → Domain Owner → State Transition → Event (if applicable) → UI State.

## Accessibility/responsive
Keyboard/focus, semantic labels, readable validation/errors, loading/disabled states, confirmation for destructive actions, and responsive/mobile behavior remain required.
