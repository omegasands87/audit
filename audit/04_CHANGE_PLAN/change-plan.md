# Change Plan — `original/` Consistency Audit

## Status
**AUDIT PLAN — DO NOT MODIFY `original/` YET**

This plan is updated from verified audit findings. Corrective changes happen only after Source-of-Truth reconciliation and explicit project-owner approval.

## Priority
- 🔴 Critical — fundamentally incorrect implementation risk.
- 🟠 High — can cause different modules/agents to implement different behavior.
- 🟡 Medium — ambiguity or missing definition that should be resolved before affected implementation.
- 🟢 Minor — terminology/documentation hygiene.

## Change Register

| ID | Priority | Area | Required Action |
|---|---|---|---|
| CP-001 | 🟠 | Document status | Normalize DRAFT / REVIEW / APPROVED / SUPERSEDED and distinguish status from authority. |
| CP-002 | 🟡 | Manual Transfer | Preserve the verified P0.07 flow: Manual Transfer + minimal Support Payment Verification; ensure its ownership/API contract is explicit and independent of full P1 Support. |
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
| CP-013 | 🟢 | Terminology | Maintain canonical glossary; TERM-001 and TERM-006 require remediation before implementation. |
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
| CP-026 | 🔴 | Subscription | Add authoritative Subscription entity/lifecycle/state machine and define its relationship to Membership Product and Entitlement. |
| CP-027 | 🔴 | Security & Content Protection | Add or designate the authoritative technical specification referenced by PRD. |
| CP-028 | 🟠 | Asset/Editor/Export | Establish explicit authoritative ownership/contracts for the implementation domains required by P0. |
| CP-029 | 🟠 | Purchase eligibility | Create canonical eligibility decision matrix. |
| CP-030 | 🟠 | Subscription allocation | Define annual/monthly allocation schedule, renewal, proration and unused-allocation rules. |
| CP-031 | 🟠 | Refund/reversal | Define cross-domain refund → entitlement reversal behavior, including partial consumption. |
| CP-032 | 🟠 | Provider failure | Define reservation/commit/release and ambiguous-success behavior between provider and entitlement consumption. |
| CP-033 | 🟠 | Order fulfillment | Define post-payment fulfillment failure, retry and reconciliation state machine. |
| CP-034 | 🟡 | Analytics ownership | Define Own Content Intelligence / Analytics handoff and canonical ownership. |
| CP-035 | 🟡 | White-label | Define current tenant foundation versus future full White-label activation. |
| CP-036 | 🟡 | Privacy | Define account/data deletion, anonymization, retention and dependent-record lifecycle. |
| CP-037 | 🟡 | Operations | Define backup/restore RPO/RTO acceptance, observability ownership and notification delivery/read-state separation. |
| CP-038 | 🟡 | Platform time | Define platform clock, timezone and DST authority. |
| CP-039 | 🟡 | Research raw input | Define persistence/identity rule for raw concepts/media/documents entering Analyzer. |

## Verified Reclassifications

The following earlier findings must not be carried forward under stronger classifications than the evidence supports:

```text
Manual Transfer / Support sequencing → clarification, not current Critical conflict
Provider boundary                  → clarification gap
Workspace / Research Workspace     → relationship-definition gap
Engine / Domain                    → terminology/governance clarification
Architecture / build dependency    → dependency taxonomy gap
GAP-026                            → NOT VERIFIED
GAP-038                            → merged into GAP-028
```

## Execution Order

```text
1. Preserve immutable baseline and audit evidence
2. Complete dedicated terminology/lifecycle/cross-contract/design/operations audits
3. Source-of-Truth reconciliation
4. Explicit project-owner decisions
5. Update change plan from approved decisions
6. Correct only approved working/corrected documents
7. Synchronize cross-references
8. Complete concrete slice specifications
9. Final full cross-document verification
```

## Rule
No corrective edit to `original/` until the full audit is complete and every unresolved business decision is explicitly identified. Audit findings and proposed corrections must not be silently treated as approved business decisions.
