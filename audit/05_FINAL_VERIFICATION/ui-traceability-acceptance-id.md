# UI / Design Traceability & Acceptance — Phase 6

## Status
**FINAL VERIFIED**

## Mandatory Surface Contract
Every implementation screen/surface must trace: route/surface → purpose → owner domain → API/application operation → permission → commercial entitlement if applicable → loading → empty → error → denied → pending/processing → success after backend confirmation → responsive behavior → accessibility acceptance.

## Canonical State Presentation
- Authorization failure is distinct from commercial entitlement failure.
- Loading is not empty.
- Empty is not error.
- Pending/processing is not success.
- Success is shown only after authoritative backend confirmation.
- Conflict displays a recoverable version/conflict state.

## Production Surface Trace
`Content Slot → Blueprint → Asset Preparation/Generation → Editor → Export`.
Each surface must reference the state and contract of the owning domain and may not create competing business state.

## Required Acceptance
For every affected screen:
1. route/action is mapped to an approved API/application operation;
2. permission is checked server-side;
3. entitlement check is separate where required;
4. state transitions are rendered from authoritative backend state;
5. retry does not duplicate mutation;
6. conflict is visible and recoverable;
7. keyboard/focus/semantic accessibility behavior is defined;
8. responsive behavior is verified at supported breakpoints;
9. user-safe error messages do not expose secrets/internal details;
10. no UI path directly accesses database, provider, storage or queue outside the approved application boundary.

## Traceability Rule
A screen cannot be marked Done solely because it renders. Its functional slice must prove UI ↔ API ↔ domain ↔ persistence, plus worker/provider/storage where applicable.
