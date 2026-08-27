# Core Contract #6 — Provider Pool & Integration

## Status

**Draft for Core Design — based on the finalized PRD, Final Business Decision Register, Core Contracts #1–#5**

This contract defines the shared provider infrastructure used by:

```text
Research Intelligence
Content Engine
Analytics
Billing / Payment
```

Core principle:

> **Domain services call a Provider Abstraction, not a specific vendor directly.**

Architecture:

```text
Domain Service
      ↓
Provider Service
      ↓
Provider Pool
      ↓
Provider Adapter
      ↓
External Provider
```

The platform must be able to add, remove, disable, prioritize, or replace providers without rewriting the consuming engine.

---

# 1. Scope

This contract covers:

- Provider;
- Provider Pool;
- Provider Adapter;
- Provider Capability;
- routing;
- priority;
- fallback;
- health;
- retry;
- rate limit;
- provider quota;
- credential reference;
- model mapping;
- endpoint configuration;
- provider status;
- usage tracking;
- cost metadata;
- provider events;
- Research/Data Provider Pool;
- AI Provider Pool;
- Payment Provider Adapter reference.

It does not define:

- individual AI model behavior;
- Research business logic;
- Product/Entitlement;
- Order;
- Payment commercial rules;
- White-label business flow.

---

# 2. Source Principles

The PRD defines five main provider pools:

```text
1. Text & Analysis
2. Image Generator
3. Video Generator
4. Voice Generator
5. Research & Data
```

The PRD also specifies:

- Image and Video pools can have separate Included / Add-on paths;
- Preview requests can be routed to cheaper providers/models;
- YouTube Data API v3 may use multiple API keys/projects for quota rotation;
- provider failover is supported;
- Admin can add custom providers through a Custom Provider Registry;
- provider configuration includes name, pool, path, Base URL, API Key, Model ID, priority, and active status. fileciteturn11file0L17-L42

The final architecture additionally requires:

```text
Research Provider Administration
→ its own provider pool

Payment:
Xendit
Duitku
NOWPayments
Manual Transfer
future providers
```

Payment remains a dedicated domain adapter but follows the same provider abstraction principles.

---

# 3. Core Principle — Provider ≠ Capability

A provider is an external implementation.

A capability is what the platform needs.

Example:

```text
Capability:
image_generation

Providers:
Provider A
Provider B
Provider C
```

The application asks:

```text
"generate image"
```

It does not ask:

```text
"call Provider A"
```

This separation is central to provider portability.

---

# 4. Provider Entity

Conceptual:

```text
Provider
```

Minimum fields:

| Field | Purpose |
|---|---|
| `provider_id` | stable internal identity |
| `name` | provider name |
| `provider_type` | AI / Research / Payment / other |
| `status` | active / inactive / degraded / disabled |
| `base_url` | provider endpoint |
| `environment` | test / production |
| `priority` | routing priority |
| `health_status` | health state |
| `created_at` | timestamp |
| `updated_at` | timestamp |

Credentials are **not stored directly as plaintext fields**.

Use:

```text
credential_ref
```

to the Secret Management boundary.

---

# 5. Provider Type

Minimum:

```text
AI
ResearchData
Payment
```

Future:

```text
Storage
Email
Messaging
Other
```

---

# 6. Provider Pool

A pool groups interchangeable providers for one technical purpose.

Core pools:

```text
Text & Analysis
Image Generator
Video Generator
Voice Generator
Research & Data
```

Payment provider adapters are conceptually similar but remain under Payment Service because their business lifecycle is different.

---

# 7. Text & Analysis Pool

Examples:

```text
GPT-family provider
Claude-family provider
Gemini-family provider
Custom text provider
```

Potential capabilities:

```text
text_generation
reasoning
classification
analysis
summarization
comparison
```

Exact model/provider list is Admin configuration, not core hard-code.

---

# 8. Image Generator Pool

Capabilities may include:

```text
image_generation
image_editing
image_variation
image_upscale
```

PRD currently separates:

```text
Included
Add-on
Preview
```

routing paths for Image generation.

These paths are configuration/routing policy, not separate provider implementations.

---

# 9. Video Generator Pool

Capabilities may include:

```text
video_generation
video_variation
video_render_support
```

The same conceptual routing paths can be used:

```text
Included
Add-on
Preview
```

---

# 10. Voice Generator Pool

Capabilities may include:

```text
voice_generation
speech_synthesis
```

Voice consumption follows the finalized billing/product entitlement model rather than a wallet.

---

# 11. Research & Data Provider Pool

Research Provider Administration has its own pool.

Core conceptual structure:

```text
Research & Data Pool
├── YouTube Data API
├── YouTube Analytics API
├── Social Data Provider
├── SEO / Keyword Provider
├── Trend Provider
├── Stock / Media Provider
└── Custom Research Provider
```

This follows the Research/Data Provider Pool defined in the PRD. fileciteturn11file0L31-L42

---

# 12. Research Provider Capabilities

Examples:

```text
youtube_channel_data
youtube_video_data
youtube_trend_data
youtube_analytics
social_public_data
keyword_research
seo_metrics
trend_data
stock_media_search
```

Provider capability mapping determines which provider can satisfy which request.

---

# 13. Provider Capability Entity

Conceptual:

```text
ProviderCapability
```

Fields:

```text
provider_id
capability_code
model_id
version
status
constraints
```

Example:

```text
Provider A
capability = image_generation
model = Model-X
```

Another:

```text
Provider B
capability = image_generation
model = Model-Y
```

---

# 14. Model Registry

For AI providers, model selection should be registry-driven.

Conceptual:

```text
Model
├── model_id
├── provider_id
├── capability
├── quality_class
├── cost_class
├── max_input
├── max_output
├── status
└── metadata
```

Admin can add models without changing the engine when the adapter already supports them.

---

# 15. Provider Adapter

Each provider requires an adapter translating the platform's canonical request into provider-specific API calls.

Conceptually:

```text
CanonicalRequest
       ↓
Provider Adapter
       ↓
Provider API
```

And:

```text
Provider Response
       ↓
Provider Adapter
       ↓
CanonicalResponse
```

Domain services consume the canonical format.

---

# 16. Canonical Request

Every provider-capable operation should have a normalized request.

Example:

```text
GenerateImageRequest
├── capability
├── prompt
├── input_assets
├── dimensions
├── quality
├── model_preference
├── policy_context
├── user_id
├── request_id
└── metadata
```

Provider-specific fields can live in an adapter extension field when required.

---

# 17. Canonical Response

Normalized response:

```text
GenerateImageResponse
├── status
├── output_reference
├── provider_id
├── model_id
├── usage
├── cost_metadata
├── request_id
├── external_reference
└── error
```

---

# 18. Provider Routing

Routing decides:

> **Which provider should process this request?**

Inputs may include:

```text
capability
market
currency
product
entitlement_source
quality
speed
cost
provider_health
priority
user/role policy
request_type
preview/final
```

---

# 19. Routing Priority

Default routing may use:

```text
1. Capability match
2. Active provider
3. Health
4. Priority
5. Policy constraints
6. Cost / quality routing
```

Exact routing strategy is configurable.

---

# 20. Included vs Add-on vs Preview

For Image/Video:

```text
Preview Request
→ Preview routing policy

Included Entitlement Request
→ Included routing policy

Purchased Add-on Request
→ Add-on routing policy
```

The same provider can serve multiple paths if allowed.

The route is a policy, not a new pool.

---

# 21. Cost-Aware Routing

Admin may configure a routing policy:

```text
Lowest Cost
Balanced
Highest Quality
Lowest Latency
Custom Priority
```

Example:

```text
Preview
→ lowest-cost eligible provider

Final
→ quality-first eligible provider
```

---

# 22. Quality / Cost Metadata

Provider model metadata may include:

```text
quality_class
cost_class
latency_class
```

These are routing inputs, not absolute claims.

Actual cost should come from provider usage/billing data where available.

---

# 23. Auto-Failover

If the selected provider fails with a retryable condition:

```text
Provider A
   ↓
Failure
   ↓
Provider B
   ↓
Success
```

Failover is allowed only if:

- the request is safe to retry;
- the fallback provider supports the capability;
- entitlement is not consumed twice;
- idempotency is preserved.

---

# 24. Non-Retryable Errors

Examples:

```text
Invalid Request
Invalid Credentials
Unsupported Capability
Policy Violation
Invalid Parameters
```

These should not blindly fail over.

The adapter classifies errors:

```text
Retryable
Non-Retryable
Unknown
```

---

# 25. Retry Policy

Retry may use:

```text
Immediate Retry
Exponential Backoff
Provider Switch
```

Maximum retries are configuration-driven.

Retry must preserve:

```text
request_id
idempotency_key
```

---

# 26. Provider Health

Provider health states:

```text
Healthy
Degraded
Unavailable
Disabled
Unknown
```

Health may consider:

```text
success rate
error rate
latency
last successful request
last failure
quota state
```

---

# 27. Health Check

Provider adapters may support:

```text
healthCheck()
```

If the provider has no health endpoint, health can be inferred from real request outcomes.

---

# 28. Provider Circuit Breaker

Core should support a circuit breaker concept:

```text
Healthy
   ↓
Failure Threshold
   ↓
Degraded / Open
   ↓
No new traffic
   ↓
Recovery Test
   ↓
Healthy
```

Exact thresholds are configuration.

---

# 29. Rate Limit

Provider requests must respect:

```text
provider rate limit
platform rate limit
user rate limit
role policy
```

The most restrictive applicable limit wins.

---

# 30. Provider Quota

Some providers have provider-side quota.

Example:

```text
YouTube API
daily project quota
```

The PRD explicitly calls for:

- multiple API keys/projects;
- automatic rotation;
- failover when one key reaches quota/rate-limit/error. fileciteturn11file0L39-L42

Core therefore treats:

```text
Provider Credential / Project
```

as a routable unit.

---

# 31. Credential Pool

A single provider may have multiple credentials.

Conceptual:

```text
YouTube Provider
├── Credential A
├── Credential B
└── Credential C
```

Routing can rotate among credentials based on:

```text
quota
status
priority
health
```

Credentials themselves remain in secure secret storage.

---

# 32. Provider Quota Tracking

Conceptual data:

```text
credential_id
quota_limit
quota_used
quota_remaining
reset_at
status
```

Not every provider exposes quota information.

Unknown quota should remain:

```text
UNKNOWN
```

rather than fabricated.

---

# 33. Credential Rotation

Rotation may happen because:

- quota exhausted;
- rate limit;
- credential failure;
- scheduled rotation;
- Admin action.

Provider pool should be able to select the next eligible credential.

---

# 34. Custom Provider Registry

Godmode may provide:

```text
+ Add Provider
```

Minimum configuration:

```text
Name
Provider Type
Pool
Capability
Base URL
Credential Reference
Model ID
Priority
Status
```

This follows the flexible provider registry principle already established in the PRD. fileciteturn11file0L39-L42

---

# 35. Capability Registration

A custom provider can only be activated for a capability if:

```text
Adapter Type
+
Capability Contract
```

exists.

Admin cannot turn arbitrary URL input into a supported provider without an adapter/protocol implementation.

This preserves the same core principle used for Product and Role:

> Configurable catalog does not create new backend capability.

---

# 36. Provider Environment

Provider can have:

```text
Test
Production
```

Never allow a test credential to be silently used in production.

---

# 37. Credential Security

Credential data is referenced by:

```text
credential_ref
```

Provider configuration must never expose:

```text
API Secret
Private Key
Webhook Secret
```

as plaintext in normal Admin UI.

Supported operations:

```text
Create / Replace
Rotate
Disable
Test
```

---

# 38. Provider Test Connection

Admin can request:

```text
Test Connection
```

Flow:

```text
Admin
 ↓
Provider Test
 ↓
Adapter
 ↓
Provider
 ↓
Canonical Result
```

The test must not consume production customer entitlement unless explicitly designed as a controlled provider test.

---

# 39. Provider Usage Record

For operational and cost tracking:

```text
ProviderUsage
```

may record:

```text
request_id
provider_id
credential_id
model_id
capability
user_id
timestamp
duration
status
usage_units
estimated_cost
```

This supports Finance/Cost Tracking.

---

# 40. Provider Cost Metadata

Cost may be configured/recorded as:

```text
cost_model
unit_type
unit_price
currency
effective_from
```

Examples:

```text
per_token
per_image
per_second
per_request
per_api_unit
```

Actual provider invoices remain the authoritative external financial source when applicable.

---

# 41. Provider Integration Event

Core events:

```text
ProviderAdded
ProviderUpdated
ProviderEnabled
ProviderDisabled
ProviderHealthChanged
ProviderQuotaChanged
ProviderRequestStarted
ProviderRequestSucceeded
ProviderRequestFailed
ProviderCredentialRotated
```

Consumers:

- Audit;
- Monitoring;
- Finance;
- Analytics;
- Notification.

---

# 42. Provider Request Trace

Every external call should be traceable through:

```text
request_id
provider_id
credential_id
external_reference
```

This allows Support/Admin to answer:

> "Which provider/model actually processed this request?"

---

# 43. AI Generation Trace

Example:

```text
Content Slot
   ↓
Generate Asset
   ↓
request_id
   ↓
Image Provider
   ↓
Model
   ↓
Output
```

The output metadata may preserve:

```text
provider_id
model_id
request_id
```

without exposing sensitive credentials.

---

# 44. Research Request Trace

Example:

```text
Research Request
   ↓
Research Provider Pool
   ↓
YouTube API Credential B
   ↓
External Data
```

The system can record:

```text
provider
credential/project reference
timestamp
source
```

This is useful for freshness, reproducibility, and cost tracking.

---

# 45. Payment Provider Boundary

Payment providers use the same abstraction principles but remain under:

```text
Order & Payment Service
```

Examples:

```text
Xendit
Duitku
NOWPayments
Manual Transfer
```

The Provider Pool contract provides:

```text
adapter
credential reference
health
configuration
```

while Order & Payment controls:

```text
payment state
order state
refund
entitlement fulfillment
```

---

# 46. Provider Health vs Payment Status

Do not confuse:

```text
Provider Health
```

with:

```text
Payment Status
```

Example:

```text
Xendit
Health = Healthy

Payment
Status = Failed
```

A healthy provider can still return a failed payment due to customer/payment conditions.

---

# 47. Rate-Limit Hierarchy

Recommended effective order:

```text
Provider Limit
      ↓
Platform Limit
      ↓
Role/User Limit
      ↓
Request Limit
```

The strictest applicable limit wins.

---

# 48. Provider Pool Selection API

Conceptual internal API:

```text
resolveProvider(
    capability,
    context,
    policy
)
```

Returns:

```text
provider_id
credential_id
model_id
route
```

Then:

```text
execute(provider_route, canonical_request)
```

---

# 49. Provider Adapter API

Conceptual:

```text
validate(request)
execute(request)
normalizeResponse(response)
normalizeError(error)
healthCheck()
getCapabilities()
```

Optional:

```text
estimateCost()
cancel()
getUsage()
```

Not all providers must implement optional methods.

---

# 50. Provider Capability Matrix

Admin/System should be able to inspect:

| Provider | Capability | Model | Status | Priority |
|---|---|---|---|---|
| Provider A | image_generation | Model X | Active | 1 |
| Provider B | image_generation | Model Y | Active | 2 |
| Provider C | video_generation | Model Z | Active | 1 |

This is operational configuration, not customer-facing content.

---

# 51. Provider Fallback Matrix

Example:

```text
Image
Primary:
Provider A

Fallback:
Provider B

Secondary:
Provider C
```

If A is unavailable:

```text
A
↓
B
↓
C
```

The request fails only after all eligible routes are exhausted.

---

# 52. Provider Routing Policy

Potential policies:

```text
priority
lowest_cost
highest_quality
lowest_latency
balanced
custom
```

Each pool may use a different default policy.

---

# 53. Provider Restrictions

A provider may have constraints:

```text
supported_regions
supported_languages
supported_dimensions
supported_formats
max_input_size
max_duration
max_sources
```

Routing must respect those constraints.

---

# 54. Provider Request Rejection

If no provider can satisfy the request:

```text
NO_ELIGIBLE_PROVIDER
```

Do not select an incompatible provider just because it is online.

---

# 55. Provider Timeout

Every external provider call should have a timeout policy.

Timeout should become:

```text
Provider Timeout
→ Retryable / Non-Retryable classification
```

Never allow an external call to block the application indefinitely.

---

# 56. Background Jobs

Long-running provider requests should be job-based where appropriate:

```text
Request
→ Job Queued
→ Provider Processing
→ Callback/Polling
→ Completed
```

The Provider Service must expose normalized job state.

---

# 57. Provider Callback

Where supported:

```text
Provider Callback
→ Verify
→ Resolve Request
→ Update Job
→ Normalize Result
```

Callbacks must be authenticated and idempotent.

---

# 58. Provider Polling

Where callbacks are unavailable:

```text
Job Created
→ Poll Provider
→ Status Update
→ Retry/Poll
→ Complete
```

Polling frequency is configuration-driven.

---

# 59. Provider Data Normalization

Domain services should not depend on vendor-specific result structures.

Example:

Provider A returns:

```text
result.url
```

Provider B returns:

```text
output[0].file
```

Adapter maps both to:

```text
CanonicalOutput.reference
```

---

# 60. Provider Error Normalization

Map vendor errors to canonical classes:

```text
AUTH_ERROR
RATE_LIMITED
QUOTA_EXCEEDED
TIMEOUT
INVALID_REQUEST
UNAVAILABLE
UNSUPPORTED
PROVIDER_ERROR
UNKNOWN
```

Domain logic uses canonical errors.

---

# 61. Provider Pool Audit

Admin changes must be audited:

```text
Provider Added
Provider Disabled
Priority Changed
Credential Rotated
Model Changed
Fallback Changed
Routing Policy Changed
Quota Configuration Changed
```

---

# 62. Provider Security Boundary

Only users with appropriate permission may:

```text
provider.manage
provider.credential.manage
provider.route.configure
provider.health.view
```

Permission comes from Core Contract #2.

---

# 63. Core Invariants

```text
1. Domain services never hard-code provider vendors.

2. Every provider capability is represented through a canonical capability.

3. Provider credentials are referenced through secure secret storage.

4. Provider selection is policy-driven.

5. Inactive/disabled providers cannot receive new requests.

6. Retry/failover must preserve idempotency.

7. Duplicate callbacks cannot duplicate results/entitlement consumption.

8. Provider quota must not be fabricated when unavailable.

9. Provider health and business transaction status are separate.

10. A provider cannot be activated for a capability without a compatible adapter.

11. Historical provider usage records remain auditable.

12. Adding a provider must not require rewriting consuming domain services.

13. Research Provider Administration uses its own pool.

14. Payment provider lifecycle remains owned by Order & Payment.

15. Provider configuration is centralized through Godmode/Configuration.
```

---

# 64. Definition of Done

Core Contract #6 is complete when:

1. Five core provider pools are represented.
2. Research/Data has its own pool.
3. Provider adapters exist as a stable abstraction.
4. Capability mapping exists.
5. Model registry exists for AI providers.
6. Provider priority is configurable.
7. Provider health is tracked.
8. Retry/failover is supported.
9. Rate limits are supported.
10. Provider quota is trackable where available.
11. Multiple credentials per provider are supported.
12. Credential rotation is supported.
13. Custom Provider Registry is supported.
14. Provider requests use canonical request/response structures.
15. Provider errors are normalized.
16. Provider events are emitted.
17. Provider usage/cost can be recorded.
18. Payment providers fit the abstraction without contaminating payment state logic.
19. Provider operations are auditable.
20. Adding a new provider does not require changes to consuming engine contracts.
21. Provider configuration remains separate from product/entitlement data.
22. White-label can consume provider-backed capabilities through normal domain services in the future.

---

# 65. Dependencies

Depends on:

```text
Core Contract #1
Identity

Core Contract #2
Role / Permission

Core Contract #3
Configuration
```

Consumed by:

```text
Research
Analyzer
Script / Production
Asset Generation
Analytics
Payment
Future White-label
```

---

# 66. Next Contract

The next recommended contract is:

> **Core Contract #7 — Storage & File Lifecycle**

It should define:

```text
Storage Object
File Metadata
Upload
Export
Retention
Auto-Purge
Client-side vs Server-side Boundary
Support Attachment Retention
Storage Manager
Download
Purge Jobs
```

The current finalized policies already distinguish:

```text
Content Export
→ 48 hours

Support Attachment
→ 90 days after Closed
```

Those should become formal Storage Core rules.
