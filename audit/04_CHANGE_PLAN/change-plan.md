# Change Plan — `original/` Consistency Audit

## Status
**AUDIT PLAN — DO NOT MODIFY `original/` YET**

This plan is updated from audit findings. Corrective changes happen only after the full audit and Source-of-Truth reconciliation.

## Priority
- 🔴 Critical — fundamentally incorrect implementation risk.
- 🟠 High — can cause different modules/agents to implement different behavior.
- 🟡 Medium — ambiguity or missing definition that should be resolved before affected implementation.
- 🟢 Minor — terminology/documentation hygiene.

## Change Register

| ID | Priority | Area | Required Action |
|---|---|---|---|
| CP-001 | 🟠 | Document status | Normalize DRAFT / REVIEW / APPROVED / SUPERSEDED and distinguish status from authority. |
| CP-002 | 🔴 | Manual Transfer | Canonicalize Admin Approve Ticket → Payment = Paid → Entitlement Granted; remove duplicate approval semantics. |
| CP-003 | 🔴 | Role vs Membership | Role = authorization; Membership/Product/Entitlement = commercial capability. Remove entitlement-like Role defaults. |
| CP-004 | 🟠 | Content Slot | Content Context owns entity/lifecycle; Planner owns planning decisions and uses approved commands. |
| CP-005 | 🔴 | Research vs Analyzer | Research owns canonical Source/Evidence; Analyzer owns runs, interpretation and derived outputs. |
| CP-006 | 🟠 | Configuration | Separate configuration values from domain business meaning/enforcement. |
| CP-007 | 🟠 | Provider | Separate shared Provider Infrastructure from domain adapters and canonical business state. |
| CP-008 | 🟡 | Workspace | Explicitly model Workspace → ResearchWorkspace relationship and authorization inheritance. |
| CP-009 | 🟡 | Agency balance | Keep Agency wholesale settlement balance separate from member wallet/PAYG/deposit. |
| CP-010 | 🟡 | Engine vs Domain | Define Engine as product/feature grouping; bounded domain remains ownership boundary. |
| CP-011 | 🟡 | Dependency | Distinguish runtime dependency, build dependency, data reference and event subscription. |
| CP-012 | 🟡 | Retention | Storage executes lifecycle; business domain defines policy context and retention period. |
| CP-013 | 🟢 | Terminology | Create canonical glossary and normalize entity/state terminology. |
| CP-014 | 🟠 | Capability | Add canonical Capability Registry. |
| CP-015 | 🟠 | Permission | Add canonical Permission Registry. |
| CP-016 | 🟠 | Configuration keys | Add canonical Configuration Key Registry. |
| CP-017 | 🟠 | State machines | Add canonical State Machine Index. |
| CP-018 | 🟠 | Events | Add canonical Event Catalog. |
| CP-019 | 🟠 | APIs | Add canonical API Contract Registry. |
| CP-020 | 🟠 | Ownership | Add canonical Entity Ownership Registry. |
| CP-021 | 🟡 | Vertical slices | Complete per-slice specifications and synchronize slice IDs/dependencies/gates. |
| CP-022 | 🟡 | Research history | Map deferred historical-observation capability to concrete slice/phase. |
| CP-023 | 🟠 | Security/config | Separate configuration precedence from tenant isolation and authorization. |
| CP-024 | 🟡 | Planner boundary | Define Planner ↔ Content Context command contract. |
| CP-025 | 🟡 | Entitlement consumption | Define success/failure/retry/timeout/failover/cancel/reversal matrix. |

## Execution Order

```text
1. Governance / authority
2. Business/Product terminology
3. Core Contract ownership and boundaries
4. Architecture
5. Implementation sequencing
6. Design/UI
7. Operations
8. Final cross-document verification
```

## Rule
No corrective edit to `original/` until the full audit is complete and every unresolved business decision is explicitly identified.
