# Canonical Registry Population Index — Phase 6

## Status
**FINAL VERIFIED INDEX**

The registry remains non-semantic. Each row points to the owner rather than creating a second authority.

## Capability Registry
| Capability | Owner |
|---|---|
| identity | Identity / Contract #1 |
| authorization | Authorization / Contract #2 |
| configuration | Configuration / Contract #3 |
| commerce | Product + Entitlement + Order + Payment |
| storage | Storage / Contract #7 |
| events/audit/notifications | Event/Audit/Notification / Contract #8 |
| workspace/content | Content Context / Contract #9 |
| research | Research / Contract #10 |
| planning | Planner / Contract #11 |
| analysis | Analyzer addendum |
| subscription | Subscription addendum |
| production | Blueprint + Asset Preparation + Editor + Export addenda |
| support | Support addendum |
| referral | Referral addendum |
| analytics | Analytics addendum |
| tenant/white-label | Tenant / Platform authority |

## Permission Registry Rule
Every permission identifier is owned by Authorization/Contract #2. Commercial entitlement is never encoded as a permission.

## Configuration Registry Minimum Keys
Required registry categories include authentication/session controls, feature flags, market/localization defaults, provider configuration, storage retention, event retry/DLQ policy and security-sensitive settings. Each key must have schema, scope, version, default/effective value, owner and audit requirement before cross-domain use.

## State Machine Index
Indexes Subscription, Entitlement, Payment, Order/Fulfillment, Production, Event Processing, Storage Purge, Workspace/Content Slot, Notification and Privacy Deletion state authorities. Full transition rules are in `final-lifecycle-transition-matrix-id.md` and the owning contracts.

## Event Catalog
Minimum foundational events:
`UserCreated`, `LoginSucceeded`, `RoleAssigned`, `ConfigurationChanged`, `OrderCreated`, `PaymentPaid`, `EntitlementGranted`, `StoragePurged`.

Each event must include version, producer, consumer(s), payload reference, correlation/causation identity, idempotency and retry policy.

## API Registry
All API operations use the schema defined in `final-contract-api-registry-id.md`.

## Entity Ownership Registry
Identity: User/Session. Authorization: Role/Permission. Configuration: Configuration/Feature Flag. Product: Product/Price. Entitlement: Entitlement. Order: Order. Payment: Payment/Refund. Provider: Provider infrastructure. Storage: Storage Object. Event: Event delivery. Audit: Audit Record. Notification: Notification. Content Context: Workspace/Content Plan/Content Slot. Research: Research Source/Evidence/Insight. Planner: planning decisions. Analyzer: Analysis Run/Interpretation. Blueprint: Blueprint/Variant. Subscription: Subscription. Support: Ticket. Referral: Commission. Tenant: Tenant foundation.

## Verification
No registry row is permitted to define an independent lifecycle, entitlement, financial rule, authorization rule or domain ownership rule. Such semantics remain in the owner.
