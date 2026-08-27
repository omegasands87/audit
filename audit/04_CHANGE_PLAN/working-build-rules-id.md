# Build Rules / Implementation Rules — Working Correction Set

## Status
CONTROLLED WORKING DOCUMENT — PHASE 5

1. Use the authority hierarchy and Phase 4 Source-of-Truth.
2. Never invent missing business behavior.
3. Preserve one authoritative owner per persistent entity.
4. Keep authorization separate from commercial entitlement.
5. Keep Order, Payment and Fulfillment state separate.
6. Use explicit lifecycle/state machines.
7. Make retryable side effects idempotent.
8. Reconcile ambiguous provider outcomes before applying irreversible business effects.
9. Use command/event boundaries for cross-domain mutations.
10. Keep registry/index layers non-semantic.
11. Preserve tenant/security boundaries.
12. UI must reflect authoritative backend state.
13. Every vertical slice must provide end-to-end traceability before implementation.
14. Do not modify `original/`.
15. Do not alter locked business decisions or website concept.
