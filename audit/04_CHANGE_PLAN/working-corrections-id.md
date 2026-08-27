# Phase 5 — Corrected / Working Document Set

## Status
**CONTROLLED WORKING SET — PHASE 5**

This directory is outside `original/` and contains the controlled correction layer produced from Phase 4 Source-of-Truth reconciliation.

## Authority
1. Final Business Decision Register
2. Phase 4 Source-of-Truth
3. Approved Core Contracts / Contract Addenda
4. Approved Architecture
5. Approved Implementation Specifications
6. UI / Operations working specifications

## Applied Corrections
- Role/Permission = authorization.
- Membership/Product/Entitlement = commercial access/benefit.
- Agency Mode = commercial membership/product mode; Agency roles provide authorization.
- Research = canonical source/evidence authority; Analyzer = derived interpretation.
- Subscription has explicit authority and lifecycle.
- Order, Payment, Fulfillment are separate state boundaries.
- Entitlement reservation/commit/release/reversal is idempotent.
- Refund financial state remains Payment-owned; entitlement reversal remains Entitlement-owned.
- Provider ambiguity uses reconciliation and cannot duplicate business effects.
- Registry/index documents are discovery/governance layers, not semantic authorities.
- Content production handoffs are explicit.
- UTC is canonical platform time.
- Delivery and read state are separate.
- Privacy deletion is distinct from mandatory financial/audit retention.
- Security-sensitive configuration cannot bypass authorization, tenant isolation, or security controls.

## Controlled Documents
- `authoritative-contracts-id.md`
- `canonical-registries-id.md`
- `implementation-specifications-id.md`
- `cross-reference-matrix-id.md`
- Existing Phase 5 correction specifications in this directory.

## Non-Changes
`original/` is untouched. No product concept, monetization model, pricing model, or locked business decision is changed.
