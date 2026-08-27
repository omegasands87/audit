# Final API Specifications — Working Correction Set

## Status
CONTROLLED WORKING DOCUMENT — PHASE 5

## API rules
- Every operation has a domain owner and contract reference.
- Authorization is evaluated through Role/Permission.
- Commercial access is evaluated through Product/Membership/Entitlement rules.
- Retryable mutations require idempotency keys/semantics.
- Ambiguous provider outcomes return a reconciliation state rather than false failure/success.
- Cross-domain mutation uses approved command/event boundaries.
- Error codes and retryability are catalogued in the canonical registry.

## Trace
API endpoint → domain contract → state transition → event → UI/operation behavior.
