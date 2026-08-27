# Core Contract #5 — Order & Payment

## Status

**Draft for Core Design — based on the finalized PRD, Final Business Decision Register, Core Contracts #1–#4**

This contract defines the transaction layer between:

```text
Product / Pricing
       ↓
Order
       ↓
Payment
       ↓
Payment Confirmation
       ↓
Entitlement Grant
```

The core objective is to ensure that:

> **A customer receives entitlement only after a valid purchase has been successfully confirmed.**

The normal member billing model does **not** use:

```text
Top Up
PAYG Wallet
Deposit Balance
```

A separate wholesale deposit mechanism exists only for future White-label/Agency settlement and is not part of normal member billing.

---

# 1. Scope

This contract covers:

- Order;
- Order Item;
- Price Snapshot;
- Payment;
- Payment Attempt;
- Payment Method;
- Payment Provider abstraction;
- Payment status;
- Manual Transfer;
- payment verification;
- webhook/event handling;
- idempotency;
- currency;
- discount/promo;
- invoice/receipt reference;
- refund eligibility;
- refund request;
- refund status;
- entitlement grant trigger;
- transaction/audit references;
- payment failure behavior.

This contract does not define:

- Product catalog;
- Product pricing definitions;
- Entitlement definitions;
- Role/Permission;
- Provider implementation details;
- full Finance reporting;
- future White-label wholesale settlement.

Those are owned by their respective contracts.

---

# 2. Source Principles

The PRD identifies Payment Gateway configuration and Refund & Dispute Management as part of Finance Center operations. fileciteturn14file0L4-L12

The finalized platform direction changes the member billing model from the older Top Up/PAYG approach to:

```text
Product
→ Order
→ Payment
→ Entitlement
→ Usage
```

Payment provider integration must be extensible for:

```text
Xendit
Duitku
NOWPayments
Manual Transfer
Other provider
```

Provider availability is configurable by product/market.

A failed payment does not require automatic provider failover; the member may select another available payment method.

---

# 3. Core Principle — Order ≠ Payment

These are separate entities.

```text
Order
=
what the customer intends to buy

Payment
=
how that order is financially settled
```

An order may have:

```text
1 Order
→ multiple Payment Attempts
```

Example:

```text
Order #1001
   ↓
Attempt 1 — Xendit — Failed
   ↓
Attempt 2 — Bank Transfer — Pending
   ↓
Attempt 2 — Approved
   ↓
Order Paid
```

---

# 4. Order Entity

## 4.1 Minimum Fields

```text
Order
```

| Field | Purpose |
|---|---|
| `order_id` | immutable order identity |
| `order_number` | user/admin-facing identifier |
| `user_id` | purchaser |
| `market` | market context |
| `currency` | transaction currency |
| `subtotal_amount` | before discount |
| `discount_amount` | total discount |
| `total_amount` | final payable amount |
| `status` | order lifecycle |
| `source_channel` | web / admin / future tenant |
| `created_at` | creation timestamp |
| `updated_at` | update timestamp |
| `expires_at` | optional order expiry |

---

# 5. Order Item

An order may contain one or more items.

Conceptual entity:

```text
OrderItem
```

Minimum:

| Field | Purpose |
|---|---|
| `order_item_id` | unique item |
| `order_id` | parent order |
| `product_id` | purchased product |
| `product_version_id` | exact product definition |
| `quantity` | quantity |
| `unit_price` | snapshot price |
| `currency` | item currency |
| `discount_amount` | discount allocated |
| `final_amount` | final item amount |
| `metadata` | commercial snapshot |

---

# 6. Price Snapshot

At order creation, the system must snapshot the commercial information used for that order.

At minimum:

```text
product_id
product_version_id
product_name
price
currency
quantity
discount
final_amount
```

If Admin changes the product price later:

```text
Existing Order
→ unchanged
```

Historical orders must remain financially immutable.

---

# 7. Order Status

Recommended lifecycle:

```text
DRAFT
   ↓
PENDING_PAYMENT
   ↓
PAID
   ↓
FULFILLED
```

Alternative terminal/exception states:

```text
EXPIRED
CANCELLED
PAYMENT_FAILED
REFUNDED
PARTIALLY_REFUNDED
```

The exact state used depends on order/payment behavior.

---

# 8. Order State Definitions

### DRAFT

Order is being assembled but not submitted for payment.

### PENDING_PAYMENT

Order exists and requires payment.

### PAID

Eligible payment amount has been successfully confirmed.

### FULFILLED

Entitlement grant has been successfully completed.

### EXPIRED

Order was not paid before its allowed validity period.

### CANCELLED

Order was intentionally cancelled.

### REFUNDED

Payment was refunded according to the refund policy.

---

# 9. Payment Entity

Conceptual:

```text
Payment
```

Minimum fields:

| Field | Purpose |
|---|---|
| `payment_id` | immutable payment identity |
| `order_id` | associated order |
| `provider_id` | payment provider |
| `payment_method` | actual method |
| `currency` | payment currency |
| `amount` | payment amount |
| `status` | payment lifecycle |
| `provider_reference` | external reference |
| `created_at` | creation |
| `confirmed_at` | confirmation |
| `failed_at` | failure |
| `metadata` | provider-specific information |

---

# 10. Payment Status

Minimum:

```text
INITIATED
PENDING
PAID
FAILED
EXPIRED
CANCELLED
REFUNDED
PARTIALLY_REFUNDED
```

Recommended interpretation:

```text
INITIATED
→ payment session/order created

PENDING
→ waiting for provider/member/admin confirmation

PAID
→ confirmed settlement

FAILED
→ provider or validation failure

EXPIRED
→ payment window expired

REFUNDED
→ full refund processed
```

---

# 11. Payment Attempt

An order may have multiple payment attempts.

```text
Order
├── Payment Attempt 1
├── Payment Attempt 2
└── Payment Attempt 3
```

This prevents the system from treating each failed method attempt as a new commercial order.

Example:

```text
Order = $9.99

Attempt A:
Xendit
Failed

Attempt B:
Card
Failed

Attempt C:
Manual Transfer
Approved
```

Still:

```text
One Order
One fulfilled purchase
```

---

# 12. Payment Method

Payment Method identifies how the member intends to pay.

Examples:

```text
Bank Transfer
Card
E-wallet
QR
Crypto
Manual Transfer
```

The actual methods available depend on the payment provider and market configuration.

---

# 13. Payment Provider Abstraction

Core should use a provider abstraction:

```text
Payment Service
       ↓
Provider Adapter
       ├── Xendit
       ├── Duitku
       ├── NOWPayments
       ├── Manual Transfer
       └── Future Provider
```

Domain logic must not directly depend on one provider's API.

---

# 14. Provider Adapter Contract

Each provider adapter should conceptually support:

```text
createPayment()
getPaymentStatus()
cancelPayment()
verifyCallback()
refundPayment()
```

Not every provider must support every operation.

Capability support should be declared by provider.

Example:

```text
Provider:
Manual Transfer

createPayment = yes
getPaymentStatus = admin-controlled
refundPayment = manual policy
```

---

# 15. Provider Configuration

Provider configuration belongs to the Admin/Configuration/Provider domains.

Core expects a configured provider reference:

```text
provider_id
```

Sensitive credentials are managed through secure secret storage, not normal order records.

---

# 16. Product/Market Payment Availability

A payment provider can be enabled/disabled per:

```text
Market
Product
Currency
Payment Method
```

Example:

```text
Indonesia
Xendit
IDR
Active
```

```text
Global
NOWPayments
USD
Active
```

Purchasability requires an available payment method.

---

# 17. Failed Payment Behavior

Final business rule:

> **No mandatory automatic provider failover.**

If payment fails:

```text
Payment Attempt
→ FAILED
```

The order can remain:

```text
PENDING_PAYMENT
```

if still valid.

Member can select another available payment method.

Example:

```text
Xendit Failed
   ↓
Choose Another Method
   ↓
Duitku / Card / Manual Transfer
```

---

# 18. Payment Success

Only a confirmed payment may transition:

```text
Order
PENDING_PAYMENT
        ↓
Payment
PAID
        ↓
Order
PAID
```

After this, the system triggers entitlement fulfillment.

---

# 19. Entitlement Grant Trigger

The critical sequence is:

```text
Payment Confirmed
       ↓
Verify Order
       ↓
Verify Amount
       ↓
Verify Currency
       ↓
Verify Product / Price Snapshot
       ↓
Grant Entitlement
       ↓
Order = FULFILLED
```

Payment confirmation alone should not blindly grant entitlement if the order is invalid.

---

# 20. Entitlement Grant Idempotency

If payment confirmation arrives multiple times:

```text
Webhook #1
→ entitlement granted

Webhook #2
→ no duplicate grant
```

Use:

```text
order_id
payment_id
entitlement_source_id
idempotency_key
```

to enforce exactly-once fulfillment semantics at the business level.

---

# 21. Payment Idempotency

Payment creation must support an idempotency key.

Example:

```text
idempotency_key = ORDER-1001-ATTEMPT-01
```

If the client retries because of a timeout:

```text
same key
→ same payment attempt
```

not:

```text
duplicate payment
```

---

# 22. Webhook Handling

Payment providers may notify the platform asynchronously.

Flow:

```text
Provider
   ↓
Webhook Endpoint
   ↓
Verify Signature / Authenticity
   ↓
Resolve Payment
   ↓
Validate Event
   ↓
Update Payment
   ↓
Update Order
   ↓
Grant Entitlement
```

Webhook events must be idempotent.

---

# 23. Webhook Event Entity

Conceptual:

```text
WebhookEvent
```

Minimum:

```text
event_id
provider_id
external_event_id
event_type
received_at
processed_at
status
payload_reference
retry_count
error
```

`external_event_id` should be unique per provider.

---

# 24. Webhook Replay Protection

If the same external event arrives twice:

```text
external_event_id
already processed
```

the second event must not create:

- second payment;
- second order state transition;
- second entitlement grant.

---

# 25. Manual Transfer

Manual transfer is a special payment provider/method.

Final flow:

```text
Order
   ↓
Manual Transfer Selected
   ↓
Payment = PENDING
   ↓
Member transfers funds
   ↓
Member submits proof through Support Ticket
   ↓
Admin verifies ticket
   ↓
Admin approves ticket
   ↓
Payment = PAID
   ↓
Entitlement Granted
```

Approval of the manual-transfer support ticket is the official payment confirmation trigger.

No second manual payment approval is required.

---

# 26. Manual Transfer Proof

The Support Ticket attachment policy applies:

```text
Maximum:
2 MB / file

Formats:
PDF
PNG
JPG
```

The manual payment flow should associate:

```text
ticket_id
order_id
payment_id
```

so Admin does not have to manually reconcile unrelated records.

---

# 27. Manual Transfer Rejection

If proof is invalid:

```text
Ticket
→ Admin requests correction / rejects proof

Payment
→ remains PENDING
```

The member can submit new proof within the same support case.

The order must not be fulfilled until payment is approved.

---

# 28. Payment Amount Validation

Before marking a payment as PAID:

```text
Received Amount == Order Total
```

must normally be true.

If there is a mismatch:

```text
PAYMENT_AMOUNT_MISMATCH
```

and fulfillment is blocked.

Handling of underpayment/overpayment is a Finance policy and is not invented by this contract.

---

# 29. Currency Validation

Payment currency must match the order's expected currency unless the configured provider/payment flow explicitly supports an approved conversion process.

Example:

```text
Order:
USD 9.99

Payment:
USD 9.99
→ valid
```

If:

```text
Order:
USD 9.99

Payment:
IDR
```

the payment must not automatically be treated as successful without a configured conversion/reconciliation mechanism.

---

# 30. Discount / Promo

Promo code is applied at order pricing level:

```text
Product Price
   ↓
Promo / Discount
   ↓
Final Payable Amount
   ↓
Payment
```

Example:

```text
Price      Rp100.000
Discount   Rp20.000
Payable    Rp80.000
```

Payment must match the final payable amount.

---

# 31. Referral Commission Relation

When an order belongs to an eligible downline subscription:

```text
Order
→ Payment
→ Subscription Revenue
→ Referral Commission
```

The referral commission amount is based on the actual amount paid after discount according to the finalized Referral rules.

Referral itself remains a separate domain.

Order/Payment should provide the source transaction reference.

---

# 32. Refund Window

Final business rule:

> **Refund request is allowed for up to 3 days after purchase.**

After the allowed period:

```text
Refund
→ denied
```

unless a separate authorized policy explicitly overrides the rule.

---

# 33. Refund Request

Conceptual:

```text
RefundRequest
```

Fields:

```text
refund_id
order_id
payment_id
requested_by
requested_at
reason
amount
currency
status
approved_by
approved_at
processed_at
```

---

# 34. Refund Status

Minimum:

```text
REQUESTED
APPROVED
REJECTED
PROCESSING
REFUNDED
FAILED
```

---

# 35. Refund Eligibility

Before processing:

```text
Current Time - Purchase Time <= 3 days
```

must be checked.

Also:

```text
Order eligible
Payment eligible
Refund amount valid
```

---

# 36. Refund and Entitlement

Once refund is approved:

```text
Refund Approved
      ↓
Entitlement Reversal / Adjustment
```

However:

> The detailed entitlement-reversal policy belongs to the Entitlement/Refund implementation contract.

This contract supplies the financial trigger.

---

# 37. Refund and Referral Commission

Final Referral decision:

If the downline refunds while the referral commission is still `Pending`:

```text
Refund Approved
      ↓
Pending Commission Cancelled
      ↓
Upline Informed
```

Because normal refund is limited to 3 days, an already Available commission normally does not enter the standard refund path.

---

# 38. No Normal Refund After 3 Days

Once the refund window has expired:

```text
Purchase
+3 days
→ Refund unavailable
```

This means a normal commission clawback due to a late customer refund is not expected.

Future commission deduction remains available only as a financial correction mechanism if required.

---

# 39. Invoice / Receipt Reference

Successful orders/payments should be able to produce or reference:

```text
invoice_id
receipt_id
```

Minimum commercial information:

```text
Order Number
Product
Quantity
Price
Discount
Total
Currency
Payment Method
Payment Date
Status
```

Tax information is not defined by current business decisions and should remain a separate configurable finance policy.

---

# 40. Order Expiration

Orders that are not paid may expire.

Example:

```text
PENDING_PAYMENT
   ↓
payment window ends
   ↓
EXPIRED
```

Exact payment window is provider/product policy and should not be hard-coded in this contract unless configured.

Expired orders cannot be fulfilled without creating a new valid payment process.

---

# 41. Cancellation

An unpaid order may be cancelled:

```text
PENDING_PAYMENT
→ CANCELLED
```

Once paid, the order is not simply "cancelled"; financial changes use refund/reversal flows.

---

# 42. Order Reconciliation

Finance/operations must be able to reconcile:

```text
Order
↕
Payment
↕
Provider Reference
↕
Entitlement Grant
```

Example:

```text
Order #1001
Payment #P1001
Provider Ref: XND-123
Entitlement Grant: G1001
```

This chain is required for support and finance investigation.

---

# 43. Transaction Ledger Reference

Finance Center should be able to derive transaction records from payment/order events.

PRD specifies a Transaction Ledger and Payment Gateway/Refund management under Finance Center. fileciteturn14file1L30-L37

Order/Payment must therefore emit auditable financial events.

---

# 44. Payment Events

Core event concepts:

```text
OrderCreated
OrderSubmitted
PaymentInitiated
PaymentPending
PaymentPaid
PaymentFailed
PaymentExpired
PaymentCancelled
RefundRequested
RefundApproved
RefundRejected
RefundProcessed
OrderFulfilled
```

These can be consumed by:

- Entitlement;
- Finance;
- Referral;
- Notification;
- Support;
- Audit;
- Analytics.

---

# 45. Payment State Transition Rules

Examples:

```text
INITIATED → PENDING
PENDING → PAID
PENDING → FAILED
PENDING → EXPIRED
PENDING → CANCELLED
PAID → REFUNDED
PAID → PARTIALLY_REFUNDED
```

Invalid transitions must be rejected.

Example:

```text
FAILED → PAID
```

is only valid if a new verified payment attempt succeeds, not by arbitrary status update.

---

# 46. Order State Transition Rules

Recommended:

```text
DRAFT → PENDING_PAYMENT
PENDING_PAYMENT → PAID
PENDING_PAYMENT → EXPIRED
PENDING_PAYMENT → CANCELLED
PAID → FULFILLED
PAID → REFUNDED
```

`PAID` should not imply that entitlement grant has necessarily completed.

This distinction allows:

```text
Payment = PAID
Order = awaiting fulfillment
```

during a temporary downstream failure.

---

# 47. Fulfillment Failure

If payment succeeds but entitlement granting temporarily fails:

```text
Payment = PAID
Order = PAID / FULFILLMENT_PENDING
```

The system must retry fulfillment.

Customer must not be charged again.

This is one reason Order and Entitlement are separate domains.

---

# 48. Fulfillment Retry

A fulfillment job should use:

```text
order_id
payment_id
idempotency_key
```

and retry safely.

Successful retry:

```text
Entitlement Granted
→ Order FULFILLED
```

---

# 49. Duplicate Payment Protection

If the same external payment is received twice:

```text
provider_reference
```

must uniquely identify the payment transaction.

The second notification must not:

- create a second paid order;
- grant duplicate entitlements;
- create duplicate referral commission.

---

# 50. Payment Provider Availability

Provider availability is configuration-driven.

Example:

```text
Market: Indonesia
Currency: IDR

Xendit → Active
Duitku → Active
Manual Transfer → Active
NOWPayments → Inactive
```

Another market may have a different provider configuration.

---

# 51. Payment Security

Payment credentials belong to provider/security infrastructure.

Order/Payment records should store:

```text
provider_id
provider_reference
status
amount
currency
```

not:

```text
API Secret
Private Key
Full Card Number
CVV
```

Sensitive credentials are never stored in normal Order records.

---

# 52. Customer Payment Method Data

The system may store a safe provider token/reference where applicable:

```text
payment_method_token_reference
```

Never store raw sensitive payment credentials unless a PCI-compliant provider architecture explicitly requires it and the security architecture permits it.

---

# 53. Payment Attempt Retry

When an attempt fails:

```text
Attempt 1 → Failed
```

member may start:

```text
Attempt 2
```

The second attempt belongs to the same order unless the commercial order itself has been cancelled/expired.

---

# 54. No Automatic Wallet Deduction

A normal member payment must always correspond to:

```text
Order
+
Payment Attempt
```

The system must never silently deduct from:

```text
PAYG Wallet
Deposit Balance
General Credit Wallet
```

because these are not part of the finalized normal member billing model.

---

# 55. White-label Boundary

Future White-label has a different financial concept:

```text
Agency Wholesale Deposit
```

This is **not** the same as normal member billing.

For future White-label:

```text
Agency Deposit
→ Webmaster Approval
→ Balance Available
→ Webmaster Settlement
```

Agency deposit cannot be refunded according to finalized business decisions.

That wholesale settlement will be specified in the future White-label/Agency contract.

---

# 56. Manual Payment and Support Integration

Manual transfer connects:

```text
Order
→ Payment
→ Support Ticket
```

The support ticket should hold references:

```text
order_id
payment_id
```

Admin approval triggers:

```text
Payment = PAID
→ Entitlement Grant
```

This creates one traceable flow instead of separate manual systems.

---

# 57. Notifications

Payment events may trigger notifications:

```text
Order Created
Payment Pending
Payment Failed
Payment Paid
Payment Expired
Refund Requested
Refund Approved
Refund Rejected
Entitlement Granted
```

Notification delivery is handled by the Notification domain.

---

# 58. Audit

Financially sensitive actions must be audited:

```text
Order Created
Payment Created
Payment Status Changed
Manual Payment Approved
Refund Requested
Refund Approved
Refund Rejected
Refund Processed
Order Cancelled
Order Expired
```

Minimum audit fields:

```text
actor
target
before
after
timestamp
reason
source
```

---

# 59. API Contract — Customer

Conceptual APIs:

```text
POST /orders
GET /orders/:id
GET /me/orders

POST /orders/:id/payment-attempts
GET /orders/:id/payments
```

Payment provider-specific URLs remain behind the Payment Service.

---

# 60. API Contract — Payment

Conceptual:

```text
POST /payments/:id/confirm
GET  /payments/:id
POST /payments/:id/cancel
POST /payments/:id/refund
```

Manual transfer approval should use an Admin/support workflow rather than exposing a generic public "mark paid" endpoint.

---

# 61. API Contract — Webhooks

Conceptual:

```text
POST /webhooks/payments/:provider
```

Webhook processing must:

1. authenticate/verify provider event;
2. deduplicate;
3. resolve payment;
4. validate amount/currency/order;
5. transition state;
6. trigger fulfillment;
7. emit audit/event.

---

# 62. Validation Rules

Before payment success:

```text
Order exists
Order is payable
Payment amount valid
Currency valid
Provider valid
Payment attempt not already completed
```

Before entitlement fulfillment:

```text
Payment = PAID
Order commercially valid
Entitlement not already granted
```

Before refund:

```text
Payment = PAID
Within refund window
Refund not already fully processed
Amount <= refundable amount
```

---

# 63. Core Invariants

```text
1. Order and Payment are separate entities.

2. Historical order price is immutable.

3. Payment status is provider/reconciliation-backed.

4. A failed payment does not fulfill an order.

5. A confirmed payment can fulfill entitlement only once.

6. Payment webhooks are idempotent.

7. Payment attempts are idempotent.

8. Duplicate payment notifications cannot duplicate entitlement.

9. Normal member billing does not use Top Up/PAYG Wallet/Deposit.

10. Currency is stored explicitly on Order and Payment.

11. Manual Transfer requires Admin verification.

12. Manual Transfer approval triggers Payment=PAID and entitlement fulfillment.

13. Refund is normally available only within 3 days after purchase.

14. Financially sensitive state changes are auditable.

15. Provider credentials are isolated from transaction records.

16. Historical transactions remain immutable.

17. White-label wholesale deposit is a separate future settlement domain.
```

---

# 64. Definition of Done

Core Contract #5 is complete when:

1. Orders can be created from valid Products.
2. Order items snapshot the exact commercial terms.
3. Multiple payment attempts can exist under one order.
4. Payment provider abstraction is available.
5. Xendit/Duitku/NOWPayments/manual transfer can fit the adapter model.
6. Provider availability is market/product configurable.
7. IDR/USD transactions are supported.
8. Payment status lifecycle is defined.
9. Webhook handling is idempotent.
10. Payment creation is idempotent.
11. Manual transfer uses Support Ticket proof.
12. Admin approval marks manual payment Paid.
13. Paid payment triggers entitlement fulfillment.
14. Entitlement fulfillment is idempotent.
15. Failed payment does not grant entitlement.
16. Refund window is enforced at 3 days.
17. Refund state is tracked.
18. Payment/order events can feed Finance.
19. Payment/order audit trail exists.
20. Historical transaction pricing is immutable.
21. Normal member billing has no wallet/PAYG deduction.
22. White-label deposit remains a separate future domain.
23. Financial discrepancies can be reconciled from Order → Payment → Entitlement.

---

# 65. Dependencies

Depends on:

```text
Core Contract #1
Identity / User / Session

Core Contract #2
Role / Permission

Core Contract #3
Configuration

Core Contract #4
Product / Pricing / Entitlement
```

Will be used by:

```text
Finance
Referral
Support
Notification
Analytics
Admin Godmode
White-label (future)
```

---

# 66. Next Contract

The next recommended contract is:

> **Core Contract #6 — Provider Pool & Integration**

It should consolidate:

```text
AI Provider Pool
Research/Data Provider Pool
Payment Provider Adapter References
Provider Credential References
Routing
Priority
Fallback
Health
Quota
Rate Limit
Retry
```

This is the next natural step because the Order/Payment contract already depends on a provider abstraction, while the broader platform also needs the same abstraction for AI and Research providers.
