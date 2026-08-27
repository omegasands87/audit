# Final API Specifications — Working Correction Set

Status: **PHASE 5 CONTROLLED WORKING DOCUMENT**

## API authority
Every endpoint/command has one domain owner. API registry is an index; the domain contract remains semantic authority.

## Required API properties
- authentication and authorization requirements explicit;
- tenant boundary explicit where applicable;
- idempotency required for retryable mutations;
- stable error classification/codes;
- state transition returned from authoritative source;
- correlation/request identifier for operational tracing;
- provider ambiguity returns pending/reconciliation semantics rather than fabricated success/failure.

## Cross-domain mutations
Use command/event boundary. Do not expose direct writes to another domain's authoritative state.

## Contract trace
API → Domain Contract → State Machine → Event → UI/Operations behavior.
