# Final Domain Specifications — Working Correction Set

Status: **PHASE 5 CONTROLLED WORKING DOCUMENT**

| Domain | Canonical ownership |
|---|---|
| Identity | User / Session |
| Authorization | Role / Permission |
| Configuration | Config / Feature Flag |
| Product | Product / Price |
| Subscription | Subscription lifecycle |
| Entitlement | Grant / consume / release / reversal |
| Order | Purchase/order lifecycle |
| Payment | Payment / refund |
| Provider | Generic provider infrastructure |
| Research | Workspace / Source / Evidence / Insight |
| Analyzer | Analysis Run / Interpretation |
| Planner | Planning decisions |
| Content Context | Content Plan / Content Slot |
| Blueprint | Blueprint / Variant |
| Storage | Storage Object |
| Event Infrastructure | Event delivery |
| Audit | Audit Record |
| Notification | Notification |
| Workspace | Operational content context |
| Tenant | Organizational / White-label boundary |
| Support | Support Ticket |
| Referral | Referral Commission |

## Domain invariant
A lower-level domain may consume another domain's published facts but may not silently assume ownership of that domain's persistent state.
