# Final PRD — Working Correction Set (Bahasa Indonesia)

Status: **PHASE 5 CONTROLLED WORKING DOCUMENT**

## Authority
Business rules mengikuti Final Business Decision Register; product scope mengikuti PRD original setelah direkonsiliasi dengan Phase 4 Source of Truth.

## Mandatory semantic corrections
- Role/Permission = authorization.
- Membership/Product/Entitlement = commercial access/benefit.
- Agency Mode = commercial membership/product mode; Agency Role = authorization.
- Research = canonical source/evidence; Analyzer = derived interpretation.
- Product = sellable definition; Subscription = active commercial relationship; Entitlement = granted usable right.
- Order, Payment, Fulfillment = separate lifecycles.
- Workspace ≠ Tenant.

## Required product behavior
Feature access requires applicable commercial entitlement AND authorization/permission.
Existing approved billing, package, refund, referral, manual transfer, support, localization, currency, White-label, and content-protection decisions remain unchanged.

## Production flow
Content Slot → Blueprint → Asset Preparation/Generation → Editor → Export.
Each handoff requires explicit state, owner, failure/retry behavior and acceptance criteria.

## Scope guardrail
This document corrects ambiguity and authority references only. It does not introduce or remove product capability.
