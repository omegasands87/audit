# Canonical Registries — Working Authority Index

Status: **PHASE 5 CONTROLLED WORKING DOCUMENT**

Registry entries are discovery/governance indexes. They never replace domain semantic authority.

| Registry | Authority | Purpose |
|---|---|---|
| Capability | Domain Contract | Discover capability identifiers |
| Permission | Authorization / Contract #2 | Discover permissions |
| Configuration | Configuration / Contract #3 | Discover keys/schema/scope/version |
| State Machine | Domain Contract | Index lifecycle/state machines |
| Event Catalog | Producer Domain Contract | Index event name/version/payload |
| API | Domain Contract | Index endpoint/command/error/idempotency |
| Entity Ownership | Domain/Architecture | Index canonical persistent owner |

## Required invariant
A registry may reference an authority but cannot override, duplicate, or redefine its semantic rules.
