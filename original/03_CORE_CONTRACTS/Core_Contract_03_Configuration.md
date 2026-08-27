# Core Contract #3 — Configuration

## Status

**Draft for Core Design — based on finalized PRD, Final Business Decision Register, Core Contract #1, and Core Contract #2**

Dokumen ini mendefinisikan **Configuration Layer** sebagai fondasi pusat untuk seluruh pengaturan platform yang memang dirancang configurable.

Konsep utama:

```text
Admin Godmode
      ↓
Configuration Service
      ↓
Configuration Store
      ↓
Domain Services
```

Configuration Layer bukan tempat menyimpan seluruh data bisnis. Ia menyimpan **policy, setting, flag, threshold, defaults, dan runtime configuration** yang perlu dikendalikan secara terpusat.

---

# 1. Scope

Contract ini mencakup:

- configuration namespace;
- configuration key/value;
- typed configuration;
- global / market / membership / role / product / user scope;
- defaults;
- override;
- configuration precedence;
- versioning;
- activation;
- audit;
- validation;
- feature flags;
- provider configuration references;
- currency/market policy;
- support policy;
- storage policy;
- security policy;
- localization policy references.

Tidak mencakup:

- Role/Permission definition;
- Product/Entitlement definition;
- Order/Payment;
- AI Provider implementation;
- White-label business flow;
- feature business logic.

Configuration hanya menyediakan nilai/policy yang dibaca service-service tersebut.

---

# 2. Source Principle

PRD menempatkan banyak infrastructure secara terpusat di Admin, termasuk:

- AI/Data Provider Pool;
- Storage & Auto-Purge;
- Billing/Quota;
- Role & Permission;
- Auth.

PRD juga menetapkan Admin dapat membuat role dan mengatur default Analyzer secara bebas per role. fileciteturn12file0L7-L12 fileciteturn12file3L87-L94

Final Business Decisions juga menetapkan banyak parameter configurable, termasuk:

- attribution window;
- payment provider per market/product;
- harga IDR/USD;
- support SLA;
- storage retention;
- security policy;
- language by market;
- currency by market;
- white-label pricing behavior.

Configuration Layer menjadi fondasi teknis untuk semua itu.

---

# 3. Core Principle

Configuration harus menjawab:

> **"Nilai/policy apa yang sedang berlaku?"**

Bukan:

> **"Data bisnis apa yang sedang dimiliki user?"**

Contoh configuration:

```text
support.sla.normal.first_response_hours = 8
```

Contoh business data:

```text
ticket_id = SUP-00123
```

Business data tetap berada di domain service masing-masing.

---

# 4. Configuration Entity

Konsep utama:

```text
Configuration
```

Minimum fields:

| Field | Purpose |
|---|---|
| `config_id` | unique config record |
| `namespace` | domain |
| `key` | configuration key |
| `value` | typed configuration value |
| `value_type` | string / number / boolean / JSON / duration / enum |
| `scope_type` | global / market / role / membership / product / user / tenant |
| `scope_id` | identifier of scope |
| `status` | active / inactive |
| `version` | configuration version |
| `effective_from` | activation time |
| `effective_until` | optional expiry |
| `created_by` | actor |
| `updated_by` | actor |
| `created_at` | timestamp |
| `updated_at` | timestamp |

---

# 5. Namespace

Configuration dibagi berdasarkan namespace.

Contoh:

```text
system
security
session
localization
market
currency
billing
product
entitlement
payment
provider
research
analytics
support
storage
referral
notification
feature
tenant
```

Namespace membantu:

- organization;
- permission;
- auditing;
- validation;
- ownership.

---

# 6. Configuration Key

Setiap key harus mempunyai nama stabil.

Contoh:

```text
security.single_login.enabled
support.auto_close.resolved_days
support.attachment.max_file_size_mb
referral.attribution.default_days
storage.export.retention_hours
```

Key tidak boleh bergantung pada label UI.

UI dapat berubah tanpa mengubah key.

---

# 7. Typed Configuration

Configuration tidak boleh selalu diperlakukan sebagai string.

Supported types minimal:

```text
string
integer
decimal
boolean
duration
datetime
enum
JSON object
JSON array
```

Contoh:

```text
support.attachment.max_file_size_mb
type = integer
value = 2
```

```text
security.content_protection.enabled
type = boolean
value = true
```

```text
support.auto_close.resolved_days
type = integer
value = 7
```

---

# 8. Schema Definition

Setiap configurable key yang penting sebaiknya memiliki schema.

Contoh:

```text
Key:
support.auto_close.resolved_days

Type:
integer

Min:
1

Max:
365

Default:
7
```

Tujuannya mencegah Admin menyimpan nilai yang tidak valid.

---

# 9. Configuration Scope

Minimum scope:

```text
Global
Market
Membership
Role
Product
User
Tenant
```

Contoh:

```text
billing.currency.default
scope = market
scope_id = indonesia
value = IDR
```

Contoh:

```text
analyzer.default_ai_count
scope = role
scope_id = growth
value = 2
```

---

# 10. Global Configuration

Global berlaku ketika tidak ada override yang lebih spesifik.

Contoh:

```text
security.single_login.enabled
scope = global
value = true
```

---

# 11. Market Configuration

Market configuration digunakan untuk global-ready behavior.

Contoh:

```text
market = indonesia
currency = IDR
language = id
```

dan:

```text
market = global
currency = USD
language = en
```

Market bukan sama dengan language atau currency.

---

# 12. Membership Configuration

Membership dapat memiliki configuration defaults.

Contoh:

```text
role / membership:
Growth

analyzer.default_ai_count = 2
```

Namun benefit quota/package yang bersifat entitlement tetap dikelola oleh Entitlement Service, bukan Configuration Service.

---

# 13. Role Configuration

Role dapat memiliki defaults tertentu.

Contoh dari PRD:

```text
Role:
Growth

Default AI:
2

Default Result View:
Single

Deep Source Intelligence:
Included
```

PRD memang menetapkan setting ini dapat diatur bebas Admin per role. fileciteturn11file0L9-L15

Configuration Layer menyimpan policy/default tersebut.

Permission tetap ditentukan oleh Role & Permission Service.

---

# 14. Product Configuration

Product dapat memiliki configurable metadata.

Contoh:

```text
product.visibility
product.available_markets
product.purchase_enabled
product.duration_policy
```

Namun:

> Product identity, price records, entitlement definitions, dan order history tetap dimiliki oleh Product/Entitlement/Billing domains.

Configuration hanya menyimpan setting/policy yang memang bersifat configurable.

---

# 15. User Override

User-specific configuration dapat digunakan secara terbatas.

Contoh:

```text
user.language = en
```

atau:

```text
user.default_dashboard = analytics
```

User override tidak boleh mengalahkan:

- security rule;
- billing integrity;
- permission boundary;
- mandatory system policy.

---

# 16. Tenant Configuration

White-label future/core-ready:

```text
tenant.branding
tenant.domain
tenant.market
tenant.currency
tenant.product_visibility
tenant.pricing_policy
```

Configuration Layer hanya menyiapkan scoped storage.

Full white-label behavior akan didefinisikan di Tenant/White-label Contract.

---

# 17. Configuration Precedence

Jika key memiliki beberapa scope, sistem membutuhkan precedence yang deterministik.

Recommended order:

```text
System Safety Rule
      ↓
Global
      ↓
Market
      ↓
Membership / Product
      ↓
Role
      ↓
Tenant
      ↓
User Override
```

Namun tidak semua configuration mendukung semua scope.

Setiap configuration key harus mendefinisikan:

```text
allowed_scopes
```

---

# 18. Safety Rules Override Everything

Configuration tidak boleh mengalahkan immutable safety rules.

Contoh:

```text
Admin:
security.audit.enabled = false
```

Jika audit merupakan mandatory integrity control:

```text
Effective Audit
= ON
```

Admin UI harus menolak konfigurasi yang melanggar safety boundary.

---

# 19. Default Value

Setiap configurable key sebaiknya memiliki:

```text
default_value
```

Sehingga jika tidak ada override:

```text
Effective Value
= Default
```

Contoh:

```text
support.auto_close.resolved_days
Default = 7
```

---

# 20. Effective Configuration

Service harus dapat meminta:

```text
getEffectiveConfig(key, context)
```

Context dapat berisi:

```text
user_id
market
membership
role
product
tenant
```

Contoh:

```text
getEffectiveConfig(
  "currency.default",
  market=indonesia
)
```

menghasilkan:

```text
IDR
```

---

# 21. Configuration Evaluation

Flow:

```text
Request
   ↓
Configuration Context
   ↓
Find Applicable Config
   ↓
Apply Precedence
   ↓
Apply Safety Rules
   ↓
Validate
   ↓
Effective Value
```

Domain service hanya menggunakan:

> **effective configuration**

bukan mencari-cari override sendiri.

---

# 22. Configuration Versioning

Configuration penting harus versioned.

Contoh:

```text
Version 1
support.sla.normal = 1 day

Version 2
support.sla.normal = 2 days
```

History tetap tersedia.

---

# 23. Effective Date

Configuration dapat memiliki:

```text
effective_from
effective_until
```

Contoh:

```text
Promotion Policy
Effective:
1 Sep
```

Service dapat menjadwalkan perubahan tanpa deploy baru.

---

# 24. Immediate vs Scheduled Activation

Configuration dapat memiliki:

```text
Apply Now
Schedule
```

Contoh:

```text
Product price change
→ effective next billing cycle
```

atau:

```text
Feature flag
→ active immediately
```

Domain rules menentukan apakah perubahan boleh berlaku langsung.

---

# 25. Immutable Historical Configuration

Jika konfigurasi mempengaruhi financial transaction:

> Historical transaction tidak boleh berubah karena configuration berubah.

Contoh:

```text
Product Price
Version 1
Rp100.000

Order #123
→ snapshot Rp100.000

Admin changes price
→ Rp120.000

Order #123
→ tetap Rp100.000
```

Billing/Product domains menyimpan snapshot transaksi.

---

# 26. Configuration Audit

Setiap perubahan configuration penting harus diaudit.

Minimal:

```text
config_id
key
scope
before
after
changed_by
changed_at
reason
```

Audit reason sebaiknya wajib untuk configuration berisiko tinggi.

---

# 27. High-Risk Configuration

Kategori:

```text
Low
Medium
High
Critical
```

Contoh:

```text
support.sla.* → Medium

product.price.* → High

payment.provider.* → Critical

security.* → Critical

role/permission policy → Critical
```

High-risk change dapat membutuhkan:

```text
Confirmation
+
Step-up Authentication
+
Approval
```

sesuai policy.

---

# 28. Configuration Validation

Sebelum save:

```text
Schema Validation
+
Business Validation
+
Cross-field Validation
+
Safety Validation
```

Contoh:

```text
support.auto_close.resolved_days = -2
→ Reject
```

Contoh:

```text
currency = USD
market = Indonesia
```

mungkin valid, tetapi harus mengikuti market policy.

---

# 29. Configuration Dependencies

Beberapa configuration bergantung pada configuration lain.

Contoh:

```text
security.content_protection.enabled
```

bergantung pada:

```text
protected_content.enabled
```

Atau:

```text
product.purchase_enabled
```

tidak boleh true jika:

```text
product.status = archived
```

Dependency validation harus dilakukan sebelum activation.

---

# 30. Configuration Change Event

Important changes emit event:

```text
ConfigurationChanged
```

Payload concept:

```text
config_id
key
scope
old_value
new_value
changed_by
effective_at
```

Consumers:

- affected domain service;
- audit;
- cache invalidation;
- notification;
- system monitoring.

---

# 31. Cache

Configuration dapat dicache untuk performance.

Namun cache harus invalidate ketika:

```text
ConfigurationChanged
```

High-risk configuration sebaiknya memiliki invalidation guarantee yang kuat.

---

# 32. Configuration API

Internal service contract:

```text
get(key, context)
getEffective(key, context)
set(key, value, scope)
validate(key, value)
activate(config_version)
schedule(config_version)
rollback(config_version)
history(key, scope)
```

---

# 33. Admin API

Conceptual endpoints:

```text
GET    /admin/config
GET    /admin/config/:key
POST   /admin/config
PATCH  /admin/config/:id
POST   /admin/config/:id/activate
POST   /admin/config/:id/schedule
POST   /admin/config/:id/rollback
GET    /admin/config/:key/history
```

Exact API naming dapat disempurnakan dalam API Architecture.

---

# 34. Configuration UI

Godmode sebaiknya mengorganisasi configuration berdasarkan domain:

```text
Configuration
├── General
├── Security
├── Session
├── Localization
├── Market
├── Currency
├── Billing
├── Product
├── Entitlement
├── Payment
├── AI Providers
├── Research Providers
├── Analytics
├── Support
├── Storage
├── Referral
├── Notification
├── Feature Flags
└── Tenant / White-label
```

---

# 35. Configuration ≠ Product Catalog

Important boundary:

```text
Configuration
= how system behaves

Product
= what customer buys
```

Example:

```text
support.auto_close.resolved_days = 7
```

is configuration.

```text
AI Image 25
Rp100.000
25 generations
```

is Product.

Configuration may control product policy, but Product remains its own domain entity.

---

# 36. Configuration ≠ Permission

Role & Permission controls:

```text
Who may do what
```

Configuration controls:

```text
How the platform behaves
```

Example:

```text
permission:
product.manage.all

configuration:
product.purchase.enabled = true
```

Admin needs the permission before changing the configuration.

---

# 37. Configuration Categories Required by Final Decisions

The following configuration domains are required by current finalized decisions.

## Security

```text
security.single_login.enabled
security.content_protection.enabled
security.focus_loss_blur.enabled
security.printscreen_restriction.enabled
```

## Support

```text
support.attachment.max_size_mb = 2
support.auto_close.resolved_days = 7

support.sla.urgent.first_response_hours = 4
support.sla.high.first_response_hours = 8
support.sla.normal.first_response_hours = 24
support.sla.low.first_response_hours = 48

support.sla.urgent.resolution_hours = 24
support.sla.high.resolution_hours = 48
support.sla.normal.resolution_hours = 120
support.sla.low.resolution_hours = 240
```

The exact interpretation of "working hours" belongs to Support policy.

## Referral

```text
referral.attribution.default_days = 90
referral.commission.rate = 10%
referral.withdrawal.minimum = 20000 IDR
```

## Storage

```text
storage.export.retention_hours = 48
storage.support_attachment.retention_days = 90
```

## Global

```text
localization.default_language_by_market
currency.default_by_market
```

## Billing

```text
billing.refund.window_days = 3
```

## Membership / Package

```text
package.requires_active_subscription = true
package.purchase.requires_active_subscription = true
```

These reflect finalized business decisions; actual Product/Entitlement enforcement belongs to those domains.

---

# 38. Configuration for Admin Flexibility

The core should allow Admin to configure:

```text
Price
Duration
Feature Availability
Market
Currency
Provider
Priority
Retention
SLA
Threshold
Default
Feature Flag
```

But only where the corresponding domain capability already exists.

This follows the finalized platform rule:

> Admin may create/configure products and roles freely within capabilities already implemented in core.

---

# 39. Secret Configuration Boundary

Secrets are not normal configuration values.

Examples:

```text
API Key
Secret Key
Webhook Secret
OAuth Secret
Payment Credential
```

They should be stored through a secure secret-management boundary.

Configuration stores only a reference:

```text
secret_ref
```

not plaintext secret material.

---

# 40. Provider Configuration

Provider domains may expose configuration:

```text
provider.enabled
provider.priority
provider.model
provider.base_url
provider.routing_policy
```

Credentials remain in Secret Management.

---

# 41. Feature Flag Configuration

Feature flag entity/configuration should support:

```text
enabled
target_scope
rollout
effective_from
effective_until
```

Scopes may include:

```text
Global
Market
Role
Membership
User
Tenant
```

Example:

```text
feature.long_form_video.enabled = false
```

Agency/White-label full feature remains future.

---

# 42. Localization Configuration

Minimum:

```text
id
en
```

Language selection follows market by default.

Admin can enable additional languages later.

Configuration determines:

```text
available_languages
default_language_by_market
fallback_language
```

Translation resources themselves belong to the i18n system.

---

# 43. Currency Configuration

Minimum:

```text
IDR
USD
```

Configuration supports:

```text
active_currencies
default_currency_by_market
display_format
decimal_precision
rounding_rule
```

Product pricing itself remains in Product/Pricing domain.

---

# 44. White-label Configuration

Core-ready only:

```text
tenant.enabled
tenant.custom_domain.enabled
tenant.branding.enabled
tenant.product_sync.enabled
tenant.pricing_policy
```

Full White-label business logic remains outside current implementation phase.

---

# 45. Rollback

Configuration must support rollback for versioned settings.

Flow:

```text
Version 3 active
   ↓
Issue detected
   ↓
Rollback
   ↓
Version 2 active
```

Rollback must be audited.

Financial configuration rollback must not mutate historical orders.

---

# 46. Configuration Lock

Some configurations may be locked by environment.

Examples:

```text
production security secrets
database connection
runtime infrastructure credentials
```

These should not be editable from normal Godmode UI.

---

# 47. Environment Separation

Configuration may differ by:

```text
Development
Staging
Production
```

Never allow development defaults to silently propagate to production.

Examples:

```text
payment.environment = test
```

must be explicit.

---

# 48. Read vs Write Configuration Access

Domain services should usually have:

```text
read effective configuration
```

Godmode gets:

```text
read + write configuration
```

subject to Role & Permission.

A normal member should not access configuration service directly.

---

# 49. Error Handling

Configuration API errors:

```text
CONFIG_NOT_FOUND
CONFIG_INVALID
CONFIG_SCOPE_NOT_SUPPORTED
CONFIG_VERSION_CONFLICT
CONFIG_ACTIVATION_FAILED
CONFIG_PERMISSION_DENIED
CONFIG_SAFETY_RESTRICTION
CONFIG_DEPENDENCY_FAILED
```

---

# 50. Concurrency / Version Conflict

If two Admins edit the same configuration:

```text
Admin A loads Version 3
Admin B loads Version 3

Admin A saves → Version 4

Admin B attempts save from Version 3
→ Version Conflict
```

The system must prevent silent overwrite.

---

# 51. Definition of Done

Configuration Contract #3 is complete when:

1. Configuration is centralized.
2. Configuration is typed.
3. Configuration has schema validation.
4. Global/market/role/membership/product/user/tenant scopes are supported where allowed.
5. Deterministic precedence exists.
6. Safety rules override configuration.
7. Configuration is versioned.
8. Configuration changes are auditable.
9. Effective configuration can be resolved by context.
10. Changes can be immediate or scheduled where allowed.
11. Rollback is supported.
12. Configuration cache can be invalidated.
13. Secrets are not stored as normal configuration.
14. Feature flags are supported.
15. i18n/market/currency configuration is supported.
16. Support/Referral/Storage/Security policies can be configured centrally.
17. Product data remains separated from system configuration.
18. Permission data remains separated from configuration.
19. Historical financial data remains immutable.
20. Admin changes can be made without hard-coding business configuration into domain services.

---

# 52. Core Invariants

```text
1. Configuration does not own business transactions.

2. Configuration does not replace Product, Entitlement,
   Payment, or Permission domains.

3. Default = deterministic if no override exists.

4. Safety rules cannot be disabled through ordinary configuration.

5. Historical financial records are immutable.

6. Every important configuration change is auditable.

7. Effective configuration is resolved centrally.

8. Domain services consume configuration;
   they do not duplicate configuration resolution logic.

9. Unsupported configuration capabilities cannot be created
   merely by adding a configuration key.

10. Configuration schema is itself versionable.
```

---

# 53. Dependencies

Depends on:

```text
Core Contract #1
Identity / User / Session

Core Contract #2
Role / Permission / Authorization
```

Used by:

```text
Product
Entitlement
Billing
Payment
Provider
Storage
Support
Referral
Analytics
Localization
Security
Tenant
```

---

# 54. Next Contract

The next recommended contract is:

> **Core Contract #4 — Product, Pricing & Entitlement**

It should define:

```text
Product
Product Version
Price
Currency
Package
Entitlement
Entitlement Grant
Entitlement Consumption
Membership Benefit
Package Lock/Unlock
Expiration Policy
Purchase Eligibility
```

This contract will become the direct foundation for the finalized billing model:

```text
Membership
+
Feature Package
+
Add-on
   ↓
Order / Payment
   ↓
Entitlement
   ↓
Feature Usage
```
