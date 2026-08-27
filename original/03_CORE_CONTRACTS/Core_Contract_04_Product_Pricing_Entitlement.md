# Core Contract #4 — Product, Pricing & Entitlement

## Status

**Draft for Core Design — based on the finalized PRD, Final Business Decision Register, Core Contract #1, Core Contract #2, and Core Contract #3**

This contract defines the core domain that answers:

> **What can the platform sell, at what price, in which market/currency, and what benefit does the customer receive after purchase?**

The resulting model is:

```text
Membership / Product
        ↓
Price
        ↓
Order / Payment
        ↓
Entitlement Grant
        ↓
Entitlement Consumption
        ↓
Feature Usage
```

The finalized billing direction deliberately does **not** use a general-purpose member wallet:

```text
Top Up
PAYG Wallet
Deposit Balance
```

Those are not part of the normal member billing model.

---

# 1. Scope

This contract covers:

- Product;
- Product Type;
- Product Version;
- Product Status;
- Membership Product;
- Feature Package;
- Add-on Product;
- Bundle;
- Price;
- Currency;
- Market availability;
- Billing Type;
- Entitlement;
- Entitlement Grant;
- Entitlement Consumption;
- Entitlement Lock/Unlock;
- Purchase Eligibility;
- Membership lifecycle relation;
- Package persistence;
- Package usability;
- quota/usage semantics;
- historical pricing integrity.

This contract does **not** define:

- Order;
- Payment Gateway;
- Payment Transaction;
- Refund Processing;
- Role/Permission;
- Provider implementation;
- full White-label settlement.

Those are separate contracts.

---

# 2. Source Principles

The PRD separates core feature access from heavy-generation quotas and treats image/video generation as distinct resource dimensions. fileciteturn13file0L10-L14

The PRD also defines membership tiers and states that included generation quantities can vary by tier. fileciteturn13file1L20-L29

Final business decisions establish the newer billing model:

```text
Membership
+
Feature Packages
+
Add-ons
↓
Entitlements
↓
Feature Usage
```

and explicitly remove the normal member-facing:

```text
Top Up
PAYG Wallet
Deposit Balance
```

The finalized business rules also establish:

- IDR and USD are core-ready;
- Admin can set prices independently per currency;
- Admin can create products/add-ons when the capability already exists in core;
- purchased packages do not expire;
- purchased packages become locked when subscription is inactive;
- purchased packages remain owned after downgrade;
- new package purchase is blocked when subscription is inactive;
- membership entitlements remain usable until subscription end when cancellation occurs.

---

# 3. Core Principle — Product vs Entitlement

This distinction is fundamental.

```text
Product
=
what the customer buys

Entitlement
=
what the customer receives as a result
```

Example:

```text
Product:
AI Image 25

Price:
IDR 100.000

Entitlement:
25 image generations
```

The product is the commercial definition.

The entitlement is the customer's resulting right/capacity.

---

# 4. Product Entity

## 4.1 Minimum Product Fields

```text
Product
```

| Field | Purpose |
|---|---|
| `product_id` | stable product identity |
| `product_code` | machine-readable code |
| `name` | product name |
| `description` | user-facing description |
| `product_type` | membership / package / add-on / bundle |
| `status` | draft / active / inactive / archived |
| `visibility` | public / restricted / internal |
| `version_id` | current product definition version |
| `created_at` | creation timestamp |
| `updated_at` | update timestamp |

---

# 5. Product Type

Minimum conceptual types:

```text
Membership
Feature Package
Add-on
Bundle
```

Examples:

```text
Membership:
Starter
Growth

Feature Package:
AI Image 25
AI Video 10

Add-on:
Deep Source Intelligence 10
Visual Continuity 5

Bundle:
Creator Pack
```

The product catalog remains extensible.

Admin is not limited to these names.

---

# 6. Membership Product

Membership is a product with subscription semantics.

Examples:

```text
Starter
Growth
Agency Mode
```

Membership may provide:

```text
Included Entitlements
Feature Access
Workspace Limits
Default Analyzer Configuration
Other Membership Benefits
```

Membership does not itself represent Role/Permission.

---

# 7. Feature Package

A feature package provides a specific entitlement or group of entitlements.

Examples:

```text
AI Image 25
AI Video 10
Research Analysis 10
```

Feature package rules:

- purchased separately from membership;
- owned by the member after purchase;
- does not expire under current final business rules;
- cannot be used while subscription is inactive;
- remains owned after membership downgrade;
- becomes usable again when subscription becomes active.

---

# 8. Add-on Product

An add-on is a product providing additional capability/usage.

Examples:

```text
Deep Source Intelligence
Media Intelligence
Multi-AI
Cross-Source Analysis
Visual Continuity
Advanced Prompt Studio
```

An add-on may be:

```text
Included in Membership
or
Purchased Separately
```

The exact commercial packaging is controlled by Product/Entitlement configuration.

---

# 9. Bundle

Core should support product bundles.

Example:

```text
Creator Bundle
├── Image 25
├── Video 5
└── Deep Intelligence 10
```

A bundle may grant multiple entitlements after purchase.

Bundle membership remains a commercial product definition; the resulting entitlements are tracked separately.

---

# 10. Product Versioning

Products must be versionable.

Example:

```text
AI Image 25

Version 1
Price = Rp100.000

Version 2
Price = Rp120.000
```

New purchases use the active version.

Historical orders retain the exact product/pricing snapshot used at purchase time.

---

# 11. Product Lifecycle

Minimum product lifecycle:

```text
Draft
  ↓
Active
  ↓
Inactive
  ↓
Archived
```

### Draft

Not purchasable.

### Active

Purchasable according to market/payment rules.

### Inactive

Not available for new purchase, but historical ownership remains.

### Archived

No new assignment/purchase.

Historical records remain immutable.

---

# 12. Product Deletion

Hard deletion of products is generally prohibited once the product has commercial history.

Prefer:

```text
Active
→ Inactive
→ Archived
```

Historical order/product snapshots remain available.

---

# 13. Product Capability Requirement

Admin may create a new product if:

> **the underlying capability already exists in core.**

Example:

```text
Core capability:
image_generation

Admin can create:
AI Image 25
AI Image 50
AI Image 100
```

But Admin cannot create a new backend capability merely by entering:

```text
new_capability = "quantum_video"
```

without an actual domain capability existing.

This preserves the same principle used for Admin-created roles and permissions.

---

# 14. Product Capability Reference

Product should reference a capability concept.

Example:

```text
product:
AI Image 25

capability:
image_generation
```

Other examples:

```text
video_generation
deep_source_analysis
multi_ai_analysis
media_intelligence
cross_source_analysis
visual_continuity
advanced_prompt
storage_retention
```

Capabilities are defined by Core/domain services.

---

# 15. Product Price

A product may have multiple prices.

Conceptual entity:

```text
ProductPrice
```

Minimum fields:

| Field | Purpose |
|---|---|
| `price_id` | unique price |
| `product_version_id` | associated product version |
| `currency` | IDR/USD/etc. |
| `amount` | monetary amount |
| `billing_type` | one-time / recurring |
| `market_scope` | available market |
| `status` | active/inactive |
| `effective_from` | activation |
| `effective_until` | optional |
| `created_at` | timestamp |

---

# 16. Multi-Currency

Minimum core-ready currencies:

```text
IDR
USD
```

Admin may determine:

```text
IDR price
USD price
```

independently.

No automatic exchange-rate conversion is required for product pricing.

Example:

```text
AI Image 25

IDR:
Rp100.000

USD:
$9.99
```

Both are valid independent commercial definitions.

---

# 17. Market Availability

Product availability is market-aware.

Example:

```text
Product X
Indonesia → Active
Global    → Inactive
```

Another:

```text
Product Y
Indonesia → IDR
United States → USD
```

A product price alone does not make a product purchasable.

Purchasability requires:

```text
Product Active
+
Price Active
+
Market Allowed
+
Membership/Eligibility Allowed
+
Payment Method Available
```

---

# 18. Billing Type

Minimum billing types:

```text
One-Time
Recurring Monthly
Recurring Annual
```

Potential future types:

```text
Custom Period
Usage Package
```

The billing engine remains a separate contract.

---

# 19. Membership Billing Cycle

Membership may be:

```text
Monthly
Annual
```

Included benefits refresh according to the membership cycle and entitlement policy.

Example:

```text
Growth Monthly
→ monthly included entitlements

Growth Annual
→ annual subscription
→ included monthly allocations may still refresh monthly
  according to membership policy
```

The exact allocation schedule belongs to Entitlement Policy.

---

# 20. Entitlement Entity

Conceptual entity:

```text
Entitlement
```

An entitlement represents a user's right/capacity to use a capability.

Minimum fields:

| Field | Purpose |
|---|---|
| `entitlement_id` | unique entitlement |
| `user_id` | owner |
| `capability_code` | capability |
| `source_type` | membership / package / add-on / bundle / admin |
| `source_id` | source reference |
| `granted_amount` | original amount |
| `consumed_amount` | amount consumed |
| `remaining_amount` | calculated/stored remaining |
| `status` | active / locked / exhausted / revoked |
| `granted_at` | timestamp |
| `available_from` | optional |
| `expires_at` | optional |
| `metadata` | domain-specific info |

---

# 21. Entitlement Source

Every entitlement must have an identifiable source.

Examples:

```text
Membership
Package Purchase
Add-on Purchase
Bundle Purchase
Admin Grant
```

Example:

```text
Entitlement
image_generation = 40

Source:
membership_growth
Billing Cycle:
2026-08
```

Or:

```text
Entitlement
image_generation = 25

Source:
order_12345
Product:
AI Image 25
```

---

# 22. Included Membership Entitlement

Membership can grant included entitlements.

Example:

```text
Growth
→ 40 Image / month
→ 6 Video / month
```

Included entitlements generally follow the membership billing period.

When the billing period ends, the membership entitlement cycle is renewed according to product policy.

---

# 23. Purchased Package Entitlement

Purchased package rules are finalized:

> **Purchased packages do not expire under current business policy.**

However:

> **They can only be used while the user's subscription is active.**

Therefore:

```text
Subscription Active
→ entitlement status = usable

Subscription Inactive
→ entitlement status = locked
```

---

# 24. Locked Purchased Entitlement

Locked does not mean deleted.

Example:

```text
User
Purchased:
AI Image 25

Subscription:
Inactive

Entitlement:
25 remaining
Status:
Locked
```

When subscription becomes active again:

```text
25 remaining
Status:
Active
```

No entitlement is recreated; the same ownership record is unlocked.

---

# 25. Membership Cancellation

If membership is cancelled but still active until its end date:

```text
Subscription:
Cancelled
Active Until:
2026-09-25
```

Membership entitlements remain usable until:

```text
2026-09-25
```

This is consistent with the finalized business rules.

---

# 26. Subscription Expiry

When subscription fully expires:

```text
Membership Entitlements
→ locked / unavailable

Purchased Packages
→ retained
→ locked
```

Nothing is automatically deleted merely because the subscription expired.

---

# 27. New Package Purchase Eligibility

Final business rule:

> **A user cannot purchase a new feature package while subscription is inactive.**

Flow:

```text
Subscription Active
→ Package purchase allowed

Subscription Inactive
→ Package purchase blocked
```

Existing purchased packages remain owned but locked.

---

# 28. Membership Reactivation

When a user reactivates subscription:

```text
Subscription Active
    ↓
Membership Entitlements
→ Active

Purchased Package Entitlements
→ Unlocked / Active
```

Remaining quantities are preserved.

---

# 29. Membership Downgrade

When a user downgrades:

```text
Membership A
→ Membership B
```

Existing purchased packages remain owned.

Membership-specific included entitlements follow the new membership rules from the effective billing cycle.

Purchased entitlement ownership is not removed because of downgrade.

---

# 30. Entitlement Lock Reasons

Recommended reasons:

```text
SUBSCRIPTION_INACTIVE
PRODUCT_DISABLED
ACCOUNT_SUSPENDED
ACCOUNT_BLOCKED
SECURITY_LOCK
ADMIN_LOCK
```

The domain service should distinguish:

```text
Locked
```

from:

```text
Revoked
```

A locked entitlement may become usable again.

A revoked entitlement normally cannot.

---

# 31. Entitlement Status

Minimum:

```text
Active
Locked
Exhausted
Revoked
```

### Active

Usable.

### Locked

Owned but temporarily unavailable.

### Exhausted

Remaining quantity is zero.

### Revoked

Explicitly invalidated by an authorized system/admin operation.

---

# 32. Entitlement Consumption

Every usage must generate a consumption record.

Conceptual entity:

```text
EntitlementConsumption
```

Minimum fields:

| Field | Purpose |
|---|---|
| `consumption_id` | unique usage record |
| `entitlement_id` | source entitlement |
| `user_id` | user |
| `capability_code` | used capability |
| `quantity` | amount consumed |
| `reference_type` | content/job/request/etc. |
| `reference_id` | source reference |
| `created_at` | timestamp |
| `status` | committed/reversed |

---

# 33. Consumption Rule

Consumption must be atomic.

Example:

```text
Remaining:
1 Image

Generate final image
→ succeeds
→ consume 1
→ remaining = 0
```

If the generation fails before the usage is committed:

> entitlement must not be permanently consumed.

---

# 34. Preview vs Final

The finalized PRD distinguishes preview and final generation.

Preview:

```text
Preview
→ no final entitlement consumption
```

Final use:

```text
Use Final Version
→ entitlement consumption
```

The consumption service must receive an explicit usage state.

---

# 35. Consumption Idempotency

If the same request is retried:

```text
request_id = ABC123
```

the system must not consume the same entitlement twice.

Example:

```text
First request:
ABC123 → consume 1

Retry:
ABC123 → no second consumption
```

This is critical for AI generation/retry workflows.

---

# 36. Consumption Priority

When multiple entitlements can satisfy a capability:

Example:

```text
Membership Included:
40 images

Purchased Package:
25 images
```

Recommended default:

```text
1. Membership Included Entitlement
2. Purchased Package Entitlement
```

This uses included membership allocation first.

The consumption priority should be configurable per capability/product policy.

---

# 37. Consumption Across Multiple Entitlements

If a single operation requires multiple units:

```text
Request = 3 images
```

the system may consume:

```text
Membership:
2

Purchased:
1
```

if necessary and permitted.

The consumption operation must be atomic across all involved entitlement records.

---

# 38. Entitlement Grant

A grant is the action that creates an entitlement from a valid source.

Conceptual:

```text
EntitlementGrant
```

Examples:

```text
MembershipActivated
PackagePurchased
BundlePurchased
AdminGranted
SubscriptionRenewed
```

Grant fields should include:

```text
grant_id
user_id
source_type
source_id
capability_code
amount
granted_at
actor
```

---

# 39. Entitlement Grant Idempotency

A source event must not grant the same entitlement twice.

Example:

```text
order_123
Payment Confirmed
```

If the confirmation webhook arrives twice:

```text
First:
grant entitlement

Second:
no duplicate grant
```

Use a unique source reference or idempotency key.

---

# 40. Product-to-Entitlement Mapping

Product configuration defines what entitlement is granted.

Example:

```text
Product:
AI Image 25

Product Entitlement Definition:
capability = image_generation
amount = 25
```

Membership:

```text
Growth
→ image_generation = 40
→ video_generation = 6
```

Add-on:

```text
Deep Intelligence 10
→ deep_source_analysis = 10
```

---

# 41. Entitlement Unit

Each capability needs a defined unit.

Examples:

```text
image_generation
unit = generation

video_generation
unit = generation

deep_source_analysis
unit = analysis

multi_ai_analysis
unit = analysis

visual_continuity
unit = project
```

The unit must be defined by the capability contract.

---

# 42. Non-Consumable Entitlement

Some features are access rights rather than quantities.

Example:

```text
feature.long_form_video
enabled = true
```

In such cases:

```text
granted_amount
```

may be omitted/null and the entitlement behaves as an access grant.

This allows the core to support:

```text
Unlimited feature access
```

without pretending that every feature is a numeric quota.

---

# 43. Unlimited Entitlement

Core should support:

```text
UNLIMITED
```

for capabilities that are not usage-limited.

Examples may include:

```text
research_basic
planner
analyzer_core
script_writer
```

Final feature limits remain governed by Product/Entitlement configuration.

---

# 44. Capability + Quantity

A capability may support:

```text
0
1
N
UNLIMITED
```

This is more flexible than hard-coded "quota" logic.

Example:

```text
Analyzer Core
UNLIMITED

Image Generation
40

Video Generation
6
```

---

# 45. Product Eligibility

Before purchase, the system evaluates:

```text
Product Active
+
Market Allowed
+
Price Available
+
Membership Eligibility
+
Subscription Active Requirement
+
Account Status
```

For normal feature package purchase:

```text
Subscription Active = REQUIRED
```

If not:

```text
Purchase = DENIED
```

---

# 46. Membership Purchase Eligibility

Membership itself may be purchased/upgraded according to Billing rules even when the current membership is inactive, because reactivation may be required to restore access.

Membership purchase/renewal eligibility is therefore distinct from feature package purchase eligibility.

---

# 47. Add-on Eligibility

Add-ons can follow product-specific rules.

Examples:

```text
Deep Source Intelligence
Requires subscription = true

Future standalone enterprise add-on
Requires subscription = false
```

This must be product configuration.

The core should not hard-code one universal rule for every future add-on.

For current finalized business rules, ordinary packages require an active subscription.

---

# 48. Entitlement Expiration

The current finalized business rule is:

### Membership entitlement

Usually cycle-bound according to membership billing policy.

### Purchased package

No expiration under current policy.

Therefore:

```text
membership entitlement
→ cycle policy

purchased package
→ no automatic expiry
→ locked while subscription inactive
```

The entitlement model should still support `expires_at` for future product types.

---

# 49. Product Expiration vs Entitlement Expiration

These are separate.

A product may become:

```text
Archived
```

while an already-purchased entitlement remains valid according to its ownership terms.

Do not retroactively delete historical customer rights merely because a product is no longer sold.

---

# 50. Price History

Historical order pricing must remain immutable.

Example:

```text
Product:
AI Image 25

Old Price:
Rp100.000

Order #100
→ Rp100.000

Admin changes:
Rp120.000

Order #100
→ remains Rp100.000
```

---

# 51. Product Price Activation

Price changes should be versioned.

```text
Price V1
→ active until date

Price V2
→ active from date
```

New purchases select the currently valid price.

Historical orders use their snapshot.

---

# 52. Currency Integrity

A price record always stores:

```text
amount
currency
```

Never infer currency solely from market.

Market may determine the default available currency, but the final transaction must store the actual currency used.

---

# 53. Market Eligibility

Product can specify:

```text
available_markets
```

Examples:

```text
Indonesia
Global
US
EU
```

A price may also specify supported markets.

Effective purchase eligibility requires both product and price availability.

---

# 54. Membership Defaults vs Product Definition

Admin may configure role/membership defaults through Configuration and Godmode, but Product/Entitlement remains the source of entitlement truth.

Recommended hierarchy:

```text
Product Entitlement Definition
       ↓
Membership / Package Assignment
       ↓
Entitlement Grant
       ↓
User Usage
```

Configuration can influence policy, not replace entitlement ownership records.

---

# 55. Admin Grant

Godmode may support manual entitlement grants.

Example:

```text
Admin Grant
User:
A

Capability:
image_generation

Amount:
10

Reason:
Support compensation
```

Admin grants must:

- require permission;
- record actor;
- record reason;
- be auditable;
- not alter original commercial transaction history.

---

# 56. Manual Adjustment

If an entitlement needs correction:

```text
Adjustment
```

rather than silent editing.

Example:

```text
Before:
remaining = 5

Adjustment:
+5

After:
remaining = 10
```

Audit record:

```text
reason
actor
reference
timestamp
```

---

# 57. Refund Impact Boundary

Refund logic belongs to Order/Payment/Refund contracts.

However Entitlement must support:

```text
grant reversal
```

when a refund is approved.

Example:

```text
Order Paid
→ Entitlement Granted

Refund Approved
→ Entitlement Grant Reversed
```

The exact reversal behavior depends on whether entitlement was already consumed.

That policy belongs to the Refund contract.

---

# 58. Package Purchase While Subscription Inactive

Final invariant:

```text
subscription.active == false
AND
product.type == feature_package
→ purchase denied
```

Existing package:

```text
retained = true
usable = false
```

---

# 59. Membership Reactivation Example

```text
User:
Growth

Membership:
Inactive

Purchased Package:
Image 25
Remaining:
17
```

User reactivates Growth:

```text
Membership:
Active

Image Package:
17 remaining
Usable
```

No new 25-unit grant is created.

---

# 60. Multiple Packages

User can own multiple packages for the same capability.

Example:

```text
Image 25
Image 50
Image 100
```

The Entitlement Service should keep grants identifiable by source.

Consumption policy decides the order.

Recommended:

```text
oldest eligible grant first
```

unless product policy says otherwise.

---

# 61. Entitlement Ledger

For audit and reconciliation, entitlement changes should be ledger-like.

Examples:

```text
Grant +40
Consume -1
Consume -1
Adjustment +5
Reversal -10
```

Do not rely only on mutable `remaining_amount` without an underlying history.

---

# 62. Entitlement Consistency

Recommended invariant:

```text
remaining_amount
=
sum(grants)
-
sum(consumptions)
+
sum(adjustments)
-
sum(reversals)
```

The exact implementation may use cached counters, but the ledger remains the source for reconciliation.

---

# 63. Consumption Failure

If feature execution fails before final commit:

```text
Consumption
→ not committed
```

If execution succeeds but response is lost:

```text
Idempotency key
→ prevents double consumption
```

Domain services must expose sufficient state to reconcile ambiguous jobs.

---

# 64. Entitlement Service API

Conceptual internal API:

```text
getEntitlements(user_id)
getEntitlement(user_id, capability)
grantEntitlement(source)
consumeEntitlement(request)
lockEntitlement(id, reason)
unlockEntitlement(id, reason)
revokeEntitlement(id, reason)
adjustEntitlement(id, amount, reason)
checkAvailability(user_id, capability, quantity)
```

---

# 65. Product Service API

Conceptual:

```text
getProduct(product_id)
getActiveProducts(context)
getProductPrices(product_id, context)
getProductEntitlements(product_id)
checkPurchaseEligibility(product_id, user_id)
resolveActiveProductVersion(product_id, context)
```

---

# 66. Admin Product API

Conceptual:

```text
POST   /admin/products
PATCH  /admin/products/:id
POST   /admin/products/:id/activate
POST   /admin/products/:id/deactivate
POST   /admin/products/:id/archive

POST   /admin/products/:id/versions
POST   /admin/products/:id/prices
POST   /admin/products/:id/entitlements
```

Exact endpoint design belongs to API Architecture.

---

# 67. Entitlement API

Conceptual member-facing reads:

```text
GET /me/entitlements
GET /me/entitlements/:capability
```

Administrative:

```text
GET /admin/users/:id/entitlements
POST /admin/users/:id/entitlements/grant
POST /admin/users/:id/entitlements/adjust
```

All administrative writes require authorization.

---

# 68. Business Rule Summary

### Normal Member

```text
Subscription Active
    ↓
Membership Entitlements Usable
    ↓
Purchased Packages Usable
    ↓
New Packages Purchasable
```

### Subscription Inactive

```text
Membership Entitlements
→ Locked

Purchased Packages
→ Retained
→ Locked

New Package Purchase
→ Blocked
```

### Subscription Cancelled but still active

```text
Membership Entitlements
→ Usable until end date

Purchased Packages
→ Usable

New Package Purchase
→ Allowed while subscription remains active
```

---

# 69. Security Boundaries

Product/Entitlement operations require authorization.

Examples:

```text
product.manage
product.price.manage
product.entitlement.manage
entitlement.grant
entitlement.adjust
entitlement.revoke
```

These permissions are provided by Core Contract #2.

---

# 70. Audit Requirements

Audit at minimum:

```text
Product Created
Product Updated
Product Activated
Product Archived
Price Created
Price Changed
Entitlement Definition Changed
Entitlement Granted
Entitlement Consumed
Entitlement Adjusted
Entitlement Locked
Entitlement Unlocked
Entitlement Revoked
```

Financially sensitive events should include:

```text
order_id
payment_id
currency
amount
product_version
```

where applicable.

---

# 71. Core Invariants

```text
1. Product and Entitlement are separate entities.

2. Product capability must exist in core before Admin can sell it.

3. Historical price cannot change after an order is created.

4. Historical entitlement grants cannot be silently rewritten.

5. Membership entitlement and purchased package entitlement are distinct.

6. Purchased packages persist after downgrade.

7. Purchased packages remain owned after subscription expiry.

8. Purchased packages are locked when subscription is inactive.

9. New feature package purchase is blocked when subscription is inactive.

10. Membership entitlements remain usable through the paid subscription
    period even after cancellation.

11. Entitlement consumption is idempotent.

12. Entitlement grants are idempotent.

13. Financially relevant entitlement changes are auditable.

14. Product archival does not silently delete customer entitlements.

15. Currency is part of the price/transaction identity.

16. Entitlement truth is centralized in Entitlement Service.
```

---

# 72. Definition of Done

Core Contract #4 is complete when:

1. Admin can create products.
2. Product types are extensible.
3. Product versions exist.
4. Multiple currency prices are supported.
5. Price history is immutable for historical orders.
6. Market availability is supported.
7. Membership products are supported.
8. Feature packages are supported.
9. Add-ons are supported.
10. Bundles are supported.
11. Product capabilities map to existing core capabilities.
12. Entitlement grants are modeled separately from products.
13. Membership entitlements are supported.
14. Purchased package entitlements are supported.
15. Purchased packages do not expire under current policy.
16. Purchased packages lock when subscription becomes inactive.
17. Purchased packages unlock after membership reactivation.
18. Downgrade does not delete purchased packages.
19. New package purchase is blocked while subscription is inactive.
20. Entitlement consumption is tracked.
21. Consumption is idempotent.
22. Entitlement grants are idempotent.
23. Entitlement adjustments are auditable.
24. Admin manual grants are supported.
25. Unlimited entitlements are supported.
26. Non-consumable access entitlements are supported.
27. Product and entitlement APIs have stable boundaries.
28. Authorization is delegated to Role & Permission.
29. Billing/Payment remains a separate downstream contract.
30. White-label can reference Product/Entitlement through future tenant scope without changing the core model.

---

# 73. Dependencies

Depends on:

```text
Core Contract #1
Identity / User / Session

Core Contract #2
Role / Permission / Authorization

Core Contract #3
Configuration
```

Will be used by:

```text
Membership
Billing
Order
Payment
AI Generation
Research Add-ons
Script/Production Add-ons
Storage Packages
Referral
Analytics
White-label
Admin Godmode
```

---

# 74. Next Contract

The next recommended contract is:

> **Core Contract #5 — Order & Payment**

It should define:

```text
Order
Order Item
Payment
Payment Method
Payment Provider
Manual Transfer
Payment Status
Webhook
Idempotency
Refund Window
Refund
Entitlement Grant Trigger
Currency
Transaction Ledger
```

This is the next critical dependency because Product/Entitlement now needs a formal mechanism that says:

```text
Product Selected
   ↓
Order
   ↓
Payment
   ↓
Payment Confirmed
   ↓
Entitlement Granted
```
