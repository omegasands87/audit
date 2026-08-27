# Final Architecture — Working Correction Set

Status: **PHASE 5 CONTROLLED WORKING DOCUMENT**

## Canonical boundaries
Identity, Authorization, Configuration, Product, Entitlement, Order, Payment, Provider Infrastructure, Storage, Event Infrastructure, Audit, Notification, Workspace, Content Context, Research, Planner, Analyzer, Blueprint, Support, Referral, Subscription, and Tenant retain distinct ownership.

## Boundary rules
- Domain owns canonical business state.
- Provider adapters do not own consuming-domain business state.
- Cross-domain mutation uses approved command/event boundaries.
- Registries are discovery/governance indexes, not semantic owners.
- Configuration cannot override authorization, tenant isolation, or security.
- Workspace is operational context; Tenant is organizational/White-label isolation boundary.

## Platform rules
UTC is canonical system time. Events are idempotent/replay-safe. Workers use retry/DLQ. Storage deletion has explicit failure/recovery states. Backup/restore requires measurable RPO/RTO.

## Production topology
Content Slot → Blueprint → Asset Preparation/Generation → Editor → Export, with explicit ownership and state at each boundary.

## Non-change
No architectural change is permitted that alters the approved website concept or business model.
