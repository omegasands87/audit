# Core Contract #10 — Research Intelligence Data Model & Provider Results

## Status

**Revised — Replacement Version**

This document replaces the previous **Core Contract #10 — Research Intelligence Data Model & Provider Results**.

Revision purpose:

> **Synchronize Core Contract #10 completely with the final PRD Research Data Model in §5-M, while preserving the broader Research Intelligence behavior defined throughout §5.**

The PRD is the source of truth for product requirements and business rules; this contract translates those requirements into a domain/data contract without turning the PRD into an implementation architecture document. fileciteturn20file0L1-L8

---

# 1. Scope

This contract defines the persistent domain model and normalized result boundary for **Research Intelligence / Riset & Insight Center**.

It covers:

- Research Workspace;
- Competitor;
- Competitor Snapshot;
- Content Observation;
- Topic;
- Hook Pattern;
- CTA Pattern;
- Trend Signal;
- Trend Snapshot / History;
- Keyword / Keyword Cluster;
- Audience Signal;
- Audience Insight;
- Research Evidence;
- Research Insight;
- Opportunity;
- Research Digest;
- Research Run;
- Provider Result;
- Research Source Identity;
- provenance;
- freshness;
- confidence;
- observed vs estimated data;
- historical snapshots;
- cross-research correlation;
- recommendation standard;
- Research → Planner handoff;
- Research → Analyzer handoff;
- saved insight snapshots;
- reset/persistence boundary.

It does **not** define:

- Provider Pool routing;
- provider adapter implementation;
- Planner scheduling algorithm;
- Analyzer internal intelligence;
- Script generation;
- Asset generation;
- Editor rendering;
- Analytics ingestion internals.

Those are defined by their respective contracts.

---

# 2. Source of Truth

The final PRD explicitly defines Research Intelligence as a decision engine with four layers:

```text
DATA
  ↓
INSIGHT
  ↓
OPPORTUNITY
  ↓
ACTION
```

The member should not be required to interpret raw charts manually to determine what to create. fileciteturn29file0L15-L26

The final PRD also defines the research data architecture as:

```text
Research Workspace
→ Competitor
→ Content Observation
→ Topic
→ Hook Pattern / CTA Pattern
→ Trend Signal
→ Audience Signal
→ Opportunity
→ Research Insight
→ Research Digest
```

with snapshots and cross-research correlation as supporting layers. fileciteturn29file0L170-L183

This sequence is now the canonical conceptual backbone of Core Contract #10.

---

# 3. Core Principle — Research Intelligence Is a Domain, Not a Provider Schema

Provider responses are not the Research data model.

The boundary is:

```text
Research Request
        ↓
Research Provider Pool
        ↓
Provider Adapter
        ↓
Raw / External Result
        ↓
Normalization
        ↓
Research Domain Model
        ↓
Insight
        ↓
Opportunity
        ↓
Action
```

A provider may change:

- API schema;
- field names;
- response shape;
- data source;
- model;
- credential;
- availability.

The normalized Research domain must remain stable.

---

# 4. Canonical Research Data Model

The canonical PRD model is:

```text
ResearchWorkspace
        │
        ├── Competitor
        │      └── CompetitorSnapshot
        │              └── ContentObservation
        │
        ├── Topic
        │      ├── Keyword / KeywordCluster
        │      ├── TrendSignal
        │      └── ContentObservation
        │
        ├── AudienceSignal
        │
        ├── Opportunity
        │      └── Evidence
        │
        ├── ResearchInsight
        │      └── Evidence
        │
        └── ResearchDigest
```

Cross-cutting:

```text
ResearchRun
ProviderResult
SourceProvenance
Freshness
Confidence
HistoricalSnapshot
```

---

# 5. Research Workspace

## 5.1 Purpose

`ResearchWorkspace` is the persistent research context for:

```text
account
+
niche
+
brand
+
audience
+
research history
```

The final PRD explicitly describes Research Workspace as being tied to account/niche/brand. fileciteturn28file2L94-L103

It is not the same entity as the broader product `Workspace` from Core Contract #9.

Relationship:

```text
Product Workspace
        ↓
Research Workspace
```

A Research Workspace may carry research-specific context while remaining scoped to the broader authorized Workspace.

---

# 6. ResearchWorkspace Fields

Minimum conceptual fields:

| Field | Purpose |
|---|---|
| `research_workspace_id` | stable research identity |
| `workspace_id` | broader platform workspace |
| `owner_user_id` | owner |
| `niche` | research niche/context |
| `brand_profile_ref` | brand context reference |
| `audience_profile_ref` | audience context reference |
| `status` | active / archived |
| `created_at` | creation |
| `updated_at` | last update |

Research Workspace should not duplicate the complete global Workspace record.

---

# 7. Competitor

A `Competitor` represents a tracked external account/entity.

Examples:

```text
YouTube Channel
Instagram Account
TikTok Account
Other supported public account
```

The PRD requires member support for adding and tracking multiple competitors and deep competitor analysis. fileciteturn29file0L63-L74

---

# 8. Competitor Fields

Minimum:

| Field | Purpose |
|---|---|
| `competitor_id` | stable identity |
| `research_workspace_id` | research scope |
| `platform` | YouTube / Instagram / TikTok / etc. |
| `external_account_id` | platform/provider identity |
| `handle` | public handle |
| `display_name` | display name |
| `profile_url` | canonical profile |
| `status` | active / paused / archived |
| `tracked_since` | tracking start |
| `last_synced_at` | latest sync |

---

# 9. Competitor Snapshot

Competitor state must be historically observable.

The PRD explicitly requires periodic snapshots so the system can answer:

> **"Apa yang berubah?"** fileciteturn28file2L98-L100

Conceptual:

```text
CompetitorSnapshot
```

Minimum:

```text
snapshot_id
competitor_id
captured_at
followers
follower_growth
estimated_posting_frequency
engagement_metrics
content_mix
format_mix
pillar_mix
top_topics
best_publishing_window
momentum
confidence
freshness
provider_reference
```

---

# 10. Competitor Historical Comparison

Historical comparison should support:

```text
Current
vs
Previous Period
vs
Longer Baseline
```

Example:

```text
Carousel share:
22% → 47%

Median save rate:
+31%
```

This is exactly the type of historical insight described in the PRD. fileciteturn28file2L98-L100

Do not store only current competitor state.

---

# 11. Content Observation

`ContentObservation` is the canonical representation of an observed piece of competitor/own/public content.

Examples:

```text
YouTube Video
Instagram Post
TikTok Video
```

The PRD explicitly places `Content Observation` between Competitor and Topic in its recommended model. fileciteturn28file2L94-L103

---

# 12. ContentObservation Fields

Minimum conceptual fields:

| Field | Purpose |
|---|---|
| `content_observation_id` | stable identity |
| `competitor_id` | related competitor when applicable |
| `source_id` | canonical Research Source |
| `platform` | platform |
| `external_content_id` | external content identity |
| `published_at` | publication time |
| `captured_at` | observation time |
| `title` | title |
| `format` | content format |
| `pillar` | classified pillar |
| `topic_id` | topic relationship |
| `hook_pattern_id` | hook pattern |
| `cta_pattern_id` | CTA pattern |
| `metrics` | observed/estimated metrics |
| `confidence` | confidence |
| `freshness` | freshness |
| `provider_reference` | provider trace |

---

# 13. Observation Metrics

Possible normalized metrics:

```text
views
likes
comments
shares
saves
reach
watch_time
completion_rate
engagement_rate
save_rate
share_rate
comment_rate
```

The final PRD explicitly requires separation of raw metrics and relevant rates, and recommends median plus baseline rather than relying only on averages. fileciteturn29file0L67-L74

---

# 14. Observed vs Estimated Metrics

Every externally sourced metric must preserve whether it is:

```text
OBSERVED
ESTIMATED
UNKNOWN
```

Examples:

```text
YouTube public views
→ OBSERVED

Third-party estimated engagement
→ ESTIMATED
```

The Research UI must clearly distinguish estimates from observed data. fileciteturn29file0L128-L136

---

# 15. Topic

`Topic` is a shared semantic entity across Research tabs.

A topic may be referenced by:

```text
Competitor
Content Observation
Trend
Keyword
Audience Signal
Opportunity
```

The PRD explicitly describes Topic as being used across competitor/trend/keyword/audience/own-content research. fileciteturn28file2L94-L103

---

# 16. Topic Fields

Minimum:

```text
topic_id
research_workspace_id
name
normalized_name
description
category
status
created_at
updated_at
```

Optional references:

```text
related_topics[]
parent_topic_id
```

The topic system should support normalization so variants of the same semantic topic can be related.

---

# 17. Hook Pattern

A `HookPattern` represents a reusable observed hook structure.

Examples:

```text
Problem
Curiosity
Number / List
Contrarian
Before / After
Story
Warning
Question
Promise
```

The PRD explicitly requires grouping competitor hooks by pattern and measuring:

```text
frequency
median performance
uplift against baseline
saturation
```

fileciteturn28file4L124-L130

---

# 18. HookPattern Fields

Minimum:

```text
hook_pattern_id
pattern_type
name
description
created_at
updated_at
```

Observation relationship:

```text
HookPattern
← ContentObservation
```

Do not duplicate the entire observed content into the pattern record.

---

# 19. CTA Pattern

A `CtaPattern` represents a reusable observed call-to-action structure.

Examples:

```text
Save
Comment
Share
Follow
Click
Purchase Intent
```

The PRD explicitly requires CTA classification by objective and linkage to relevant performance metrics. fileciteturn29file0L71-L74

---

# 20. CTA Pattern Fields

Minimum:

```text
cta_pattern_id
purpose
name
description
created_at
updated_at
```

Relationship:

```text
CtaPattern
← ContentObservation
```

---

# 21. Trend Signal

A `TrendSignal` represents a normalized research observation about a rising/declining/saturated topic/trend.

It is not merely a trend name.

The PRD defines:

```text
velocity
lifecycle
niche relevance
saturation
format fit
expected shelf life
actionability
```

as core trend intelligence. fileciteturn29file0L76-L86

---

# 22. TrendSignal Fields

Minimum:

| Field | Purpose |
|---|---|
| `trend_signal_id` | identity |
| `topic_id` | related topic |
| `keyword_id` | optional keyword relationship |
| `platform` | source platform |
| `region` | market/region |
| `velocity` | normalized velocity |
| `lifecycle_stage` | lifecycle |
| `niche_relevance` | relevance |
| `saturation` | saturation |
| `format_fit` | supported content formats |
| `expected_shelf_life` | expected relevance window |
| `actionability` | action guidance |
| `captured_at` | observation |
| `confidence` | confidence |
| `freshness` | freshness |

---

# 23. Trend Lifecycle

Supported lifecycle values:

```text
Emerging
Rising
Peak
Declining
Saturated
```

The stage is a research interpretation.

Not every trend must pass through every stage.

---

# 24. Trend History

Trend signals must support historical observation when the applicable priority level is implemented.

Store:

```text
trend_score_history
velocity_history
lifecycle_history
opportunity_score_history
```

The PRD explicitly requests historical snapshot capability as a mature Research capability. fileciteturn29file0L198-L211

---

# 25. Keyword

A `Keyword` represents a normalized search-related term.

Possible fields:

```text
keyword_id
keyword
language
market
intent
topic_id
```

---

# 26. Keyword Cluster

Keywords may belong to:

```text
KeywordCluster
```

for grouped topic/demand analysis.

Example:

```text
Keyword Cluster:
"AI content creation"

Keywords:
AI content generator
AI social media tool
AI video generator
AI content planner
```

---

# 27. Search Intent

Supported intent examples:

```text
informational
problem
comparison
commercial
inspiration
navigational
```

The final PRD explicitly defines intent as a core part of Keyword Research. fileciteturn29file0L88-L95

---

# 28. Audience Signal

`AudienceSignal` is a normalized audience-demand unit.

The PRD defines audience areas:

```text
Profile
Pain Points
Questions
Desire
Objection
Sentiment
Language
Content Demand
Active Time
```

fileciteturn29file0L97-L111

---

# 29. AudienceSignal Fields

Minimum:

```text
audience_signal_id
research_workspace_id
signal_type
topic_id
text
language
segment
source_reference
confidence
freshness
captured_at
```

Signal types:

```text
question
pain_point
desire
objection
sentiment
language_pattern
content_demand
active_time
profile
```

---

# 30. Audience Enrichment

Audience information may be:

```text
MANUAL
PROVIDER_DATA
ANALYTICS_DERIVED
AI_INFERENCE
```

The system must preserve the origin.

The PRD explicitly allows manual audience input at the beginning and later enrichment through Analytics. fileciteturn29file0L97-L111

---

# 31. Own Content Intelligence

Own-content intelligence belongs in Research as an upstream closed-loop input.

The PRD requires automatic research visibility for:

```text
top topics
top formats
top hooks
top CTAs
best posting windows
save/share drivers
weak patterns
content fatigue
historical baseline
A/B learnings
```

fileciteturn29file0L113-L122

This is represented through normalized `ContentObservation` and related pattern/metric records rather than creating an unrelated Research-only copy of Analytics.

---

# 32. Research Evidence

All important Research outputs require evidence references.

Conceptual:

```text
ResearchEvidence
```

Minimum:

```text
evidence_id
source_id
research_run_id
observation_reference
evidence_type
location
snippet_or_metric_reference
captured_at
confidence
```

Evidence types may include:

```text
metric
text
comment
timestamp
metadata
image
audio_segment
visual_region
```

---

# 33. Research Insight

`ResearchInsight` is the normalized interpretation layer.

It answers:

> **What does the collected data mean?**

The PRD explicitly distinguishes raw Data from Insight. fileciteturn29file0L15-L26

Minimum:

```text
research_insight_id
research_workspace_id
insight_type
summary
explanation
topic_refs
evidence_refs
confidence
freshness
source_type
created_at
updated_at
```

---

# 34. Insight Types

Examples:

```text
competitor_pattern
trend_pattern
audience_pattern
own_content_pattern
content_gap
performance_pattern
timing_pattern
format_pattern
topic_pattern
```

The taxonomy remains extensible.

---

# 35. Insight vs Opportunity

These must remain separate.

```text
Insight
=
what the data means

Opportunity
=
what can/should be exploited
```

Example:

```text
Insight:
Competitors have low carousel coverage for Topic X.

Opportunity:
Create a carousel explaining Topic X now.
```

---

# 36. Opportunity

`Opportunity` is the primary decision object generated by Research.

The PRD states that Opportunity combines multiple evidence/signal sources. fileciteturn28file2L94-L103

Minimum:

```text
opportunity_id
research_workspace_id
topic_id
title
what
why_now
for_whom
angle
hook
format
cta
evidence_refs
source_signal_refs
opportunity_score
confidence
priority
status
created_at
updated_at
```

---

# 37. Opportunity Source Signals

An Opportunity may be built from:

```text
Competitor
Trend
Keyword
Audience
Own Content
Seasonality
Event
```

Example:

```text
TrendSignal
+
CompetitorGap
+
AudienceQuestion
+
OwnContentFit
→ Opportunity
```

---

# 38. Opportunity Score

Default starting components from the PRD:

| Component | Default Weight |
|---|---:|
| Demand | 25% |
| Momentum | 15% |
| Competitor Gap | 20% |
| Expected Engagement | 15% |
| Own Fit | 15% |
| Freshness | 10% |

Total:

```text
100%
```

The PRD states these weights are configurable in Admin and are starting heuristics rather than a final permanent formula. fileciteturn29file0L48-L61

---

# 39. Confidence

Every important Insight/Opportunity must have:

```text
High
Medium
Low
```

Confidence is separate from:

```text
Opportunity Score
```

Example:

```text
Opportunity Score = 84
Confidence = Medium
```

---

# 40. Research Freshness

Every time-sensitive research result should preserve:

```text
retrieved_at
captured_at
freshness
```

UI should show:

```text
updated X hours/days ago
```

This is explicitly required across the Research module. fileciteturn29file0L128-L136

---

# 41. Source Type

Every major Research output should identify source type:

```text
official API
third-party provider
member data
AI inference
```

This is part of the final PRD Research evidence/quality transparency requirement. fileciteturn29file0L128-L136

---

# 42. Evidence Count

Where applicable, outputs should preserve:

```text
evidence_count
```

Examples:

```text
47 posts
3 competitors
2,840 comments
```

Counts must reflect actual available evidence.

Never invent counts.

---

# 43. Insufficient Data

When available data is insufficient:

```text
status = INSUFFICIENT_DATA
```

or equivalent metadata.

User-facing output must explicitly say:

```text
Insufficient data
```

rather than manufacture an estimate.

The PRD explicitly prohibits AI from inventing numbers to fill gaps. fileciteturn29file0L128-L136

---

# 44. Provider Result

`ProviderResult` records the relationship between a Research Run and an external provider.

Minimum:

```text
provider_result_id
research_run_id
provider_id
capability
external_request_id
retrieved_at
status
raw_payload_reference
normalized_version
```

Raw provider response should preferably be stored as a controlled raw object/reference instead of making provider JSON the canonical Research schema.

---

# 45. Research Run

A `ResearchRun` represents execution of a research operation.

Minimum:

```text
research_run_id
research_workspace_id
workspace_id
user_id
content_slot_id (nullable)
research_type
requested_capabilities
started_at
completed_at
status
request_hash
```

Status:

```text
QUEUED
RUNNING
COMPLETED
PARTIAL
FAILED
CANCELLED
SUPERSEDED
```

---

# 46. Research Run vs Research Data

`ResearchRun` is an execution/history entity.

The domain records are durable research entities.

Example:

```text
Research Run #123
→ updates Competitor Snapshot
→ creates Trend Signal
→ updates Audience Signal
→ creates Research Insight
→ produces Opportunity
```

Do not confuse an execution record with the normalized research objects it produced.

---

# 47. Source Identity

Provider/external sources remain normalized through a shared source concept.

Conceptual:

```text
ResearchSource
```

Minimum:

```text
source_id
source_type
platform
external_source_id
canonical_url
title
author
published_at
retrieved_at
language
provenance
status
```

This entity may be referenced by:

```text
ContentObservation
ResearchEvidence
Opportunity
Analyzer
```

---

# 48. Source Deduplication

The same external content encountered across:

```text
Competitor Tracker
Analyzer
Cross-Research Correlation
```

should resolve to the same normalized `source_id` where identity can be established.

Do not create disconnected copies for every module.

---

# 49. Provenance

Every externally derived observation should preserve:

```text
provider_id
provider_result_id
external_reference
retrieved_at
retrieval_method
```

Do not store provider secrets in Research records.

Credential references remain owned by Provider Infrastructure.

---

# 50. Research Digest

`ResearchDigest` is a periodic executive decision brief.

The PRD defines:

```text
What Changed
What is Working
What is Emerging
What is Saturated
Audience Asks
Opportunities
Recommended Plan
Actions
```

fileciteturn29file0L138-L153

Minimum:

```text
research_digest_id
research_workspace_id
period_start
period_end
generated_at
sections
insight_refs
opportunity_refs
evidence_summary
status
```

---

# 51. Digest Is a Snapshot

Digest must be treated as a historical decision snapshot.

If today's Research changes:

```text
Digest from last week
→ remains historically interpretable
```

Do not dynamically rewrite historical digest content.

---

# 52. Cross-Research Correlation Engine

Cross-Research Correlation is a **layer**, not a new Research tab.

The PRD explicitly defines examples:

```text
Trend + Competitor
Audience + Keyword
Competitor + Own Content
Keyword + Competitor
Audience + Own Content
```

fileciteturn29file0L178-L183

Therefore:

```text
Correlation Engine
→ reads normalized research entities
→ creates Insight / Opportunity
```

It does not create duplicate source models.

---

# 53. Correlation Result

Conceptual:

```text
CorrelationResult
```

may store:

```text
correlation_id
signal_refs
relationship_type
derived_insight
confidence
created_at
```

But the final business-facing result should normally become:

```text
ResearchInsight
or
Opportunity
```

not remain a meaningless technical record.

---

# 54. Recommendation Format

Every Research recommendation must use the standardized structure:

```text
What
Why Now
For Whom
Angle
Hook
Format
CTA
Evidence
Opportunity Score
Confidence
Action
```

This is defined by the final PRD. fileciteturn29file0L185-L196

---

# 55. Research → Planner Contract

When an Opportunity is sent to Planner, minimum metadata:

```text
topic
pillar
format
priority
suggested_timing
opportunity_score
confidence
```

The PRD explicitly defines this handoff. fileciteturn29file0L189-L196

The Opportunity itself remains preserved.

---

# 56. Research → Analyzer Contract

When sent to Analyzer:

```text
raw_concept
evidence
target_audience
angle
hook_direction
source references
```

The PRD explicitly defines this handoff. fileciteturn29file0L189-L196

---

# 57. Save as Insight

When member saves a Research Insight:

```text
evidence_snapshot
timestamp
confidence
source references
```

must be preserved.

This prevents the saved insight from changing silently when live provider data changes later.

---

# 58. Content Slot Relation

Research can operate in two modes.

### General Research

```text
content_slot_id = null
```

Useful for:

```text
Competitor Tracker
Trend Explorer
Keyword Research
Audience Research
Research Digest
```

### Slot-Specific Research

```text
content_slot_id = CS-123
```

Useful when research is directly attached to a planned content item.

This preserves the global Research Intelligence domain while supporting Core Contract #9.

---

# 59. Research and Own Content

Own-content observations may use:

```text
content_slot_id
```

when a published item can be mapped back to the production lifecycle.

If mapping is unavailable:

```text
content_slot_id = null
```

The Research system must not invent the relationship.

---

# 60. Research and Planner

Research may influence Planner but does not own Calendar state.

Boundary:

```text
Research
→ recommendation

Planner
→ scheduling decision
```

Planner remains the authoritative owner of calendar state.

---

# 61. Research and Analytics

Analytics supplies performance facts.

Research turns those facts into:

```text
patterns
insights
opportunities
```

This is part of the closed loop defined in the PRD:

```text
Production
→ Analytics
→ Research
→ Planner
```

The Research domain should not create a second permanent Analytics metric model.

---

# 62. Research and Analyzer

Research provides:

```text
source
evidence
audience signal
opportunity
context
```

Analyzer performs deeper source/content intelligence when selected.

Analyzer must not create a competing Research Workspace / Competitor / Topic model.

---

# 63. Research Reset

Reset remains module-local.

Examples:

```text
Reset Trend Explorer
```

must not automatically delete:

```text
Competitor Tracker
Audience Insight
Keyword Research
Analyzer
Planner
```

This follows the platform persistence/reset rule. fileciteturn29file0L11-L24

---

# 64. Persistence

Research results must persist server-side and survive:

```text
Navigation
Refresh
Logout
Login Again
```

The platform-wide persistence rule explicitly requires this behavior. fileciteturn20file0L36-L43

---

# 65. Re-run Research

When member runs new Research on the same context:

```text
New ResearchRun
→ new results/version
```

Current UI results may be replaced according to module policy, but historical runs/snapshots should not be silently destroyed when retained by policy.

---

# 66. Historical Snapshot Boundary

Snapshot policy differs by entity:

```text
Competitor
→ periodic snapshot

Trend
→ trend history

Opportunity
→ opportunity score/history where applicable

Research Digest
→ periodic digest snapshot
```

P2 historical depth may be introduced after P0/P1, but the domain model must be core-ready. fileciteturn29file0L198-L211

---

# 67. Research Quality

Quality and confidence are separate.

Potential quality metadata:

```text
source_quality
data_completeness
sample_size
freshness
provider_reliability
verification_status
```

Do not reduce all dimensions to one unexplained number.

---

# 68. Research Anti-Hallucination

The Research domain inherits the final PRD rule:

```text
If data is insufficient:
→ say insufficient data

Never:
→ invent numbers
→ fabricate source coverage
→ present estimates as exact observations
→ create unsupported certainty
```

This applies across all Research tabs. fileciteturn29file0L128-L136

---

# 69. Research Competitive Intelligence Constraints

The system must:

```text
Use median + baseline
```

rather than relying only on average.

Do not:

```text
Treat follower count as primary quality measure
Treat third-party estimates as exact
Copy competitor content literally
Use hashtag as the center of research
Provide recommendation without "why"
```

These are explicit Research constraints in the PRD. fileciteturn29file0L213-L223

---

# 70. Mood Board Boundary

Mood Board references belong to Research as visual inspiration.

It is:

```text
reference
```

not:

```text
content decision truth
```

The PRD explicitly describes Mood Board as a visual reference board and connects it to Brand Kit in the Canvas Editor. fileciteturn29file0L124-L126

Binary reference storage should use Core Contract #7.

---

# 71. Provider Boundary

Research requests:

```text
research capability
```

Provider Infrastructure decides:

```text
provider
credential
route
```

Research receives normalized observations.

Research does not depend on:

```text
API key
provider-specific response schema
provider-specific URL
```

---

# 72. Provider Conflict

If two providers produce different values:

```text
Provider A
Provider B
```

preserve both observations where useful.

A normalized value must be derived only through an explicit reconciliation rule.

Do not silently overwrite one source with another.

---

# 73. AI Inference Boundary

AI inference may generate:

```text
Research Insight
Opportunity
Classification
Pattern
Recommendation
```

but those are clearly marked:

```text
AI inference
```

when that is their source type.

Source evidence remains independently traceable.

---

# 74. Multi-Source Intelligence Boundary

Cross-source relationships may produce:

```text
shared theme
conflict
gap
opportunity
```

but individual source records remain independent.

Example:

```text
Source A
Source B
Source C
   ↓
Correlation
   ↓
ResearchInsight
```

---

# 75. Event Integration

Research events include:

```text
ResearchWorkspaceCreated
CompetitorAdded
CompetitorSnapshotCaptured
ContentObservationCreated
TopicUpdated
TrendSignalUpdated
AudienceSignalCreated

ResearchRunStarted
ResearchRunCompleted
ResearchRunFailed

ResearchInsightCreated
OpportunityCreated
OpportunityUpdated

ResearchDigestCreated
OpportunitySentToPlanner
OpportunitySentToAnalyzer
ResearchReset
```

All events use Core Contract #8.

---

# 76. Audit Integration

Audit important actions:

```text
CompetitorAdded
CompetitorArchived
ResearchReset
ManualAudienceProfileUpdated
OpportunitySaved
OpportunityApplied
ResearchProviderConfigurationChanged
```

Provider configuration changes are audited through Provider/Godmode infrastructure.

---

# 77. Research API Boundary

Conceptual APIs:

```text
GET    /research/workspaces
POST   /research/workspaces

GET    /research/competitors
POST   /research/competitors
PATCH  /research/competitors/:id

GET    /research/trends
GET    /research/keywords
GET    /research/audience
GET    /research/opportunities
GET    /research/insights
GET    /research/digests

POST   /research/runs
GET    /research/runs/:id
POST   /research/runs/:id/retry
POST   /research/runs/:id/reset

POST   /research/opportunities/:id/send-to-planner
POST   /research/opportunities/:id/send-to-analyzer
```

Exact API paths remain subject to Core Architecture/API Architecture.

---

# 78. Research Service Boundary

Conceptual internal operations:

```text
ResearchService.run()
ResearchService.normalize()
ResearchService.persistObservation()
ResearchService.updateCompetitorSnapshot()
ResearchService.updateTrend()
ResearchService.updateAudienceSignal()
ResearchService.buildInsight()
ResearchService.buildOpportunity()
ResearchService.buildDigest()
ResearchService.correlateSignals()
ResearchService.createPlannerHandoff()
ResearchService.createAnalyzerHandoff()
```

Provider routing remains outside this domain.

---

# 79. Canonical Research Domain Graph

The final canonical graph is:

```text
                    Research Workspace
                           │
          ┌────────────────┼────────────────┐
          ↓                ↓                ↓
      Competitor         Topic       Audience Signal
          │                │
          ↓                ├──────── Keyword
   Competitor Snapshot    └──────── Trend Signal
          │
          ↓
   Content Observation
      │       │
      ↓       ↓
 Hook Pattern CTA Pattern
          │
          └───────────────┐
                          ↓
                      Research
                      Evidence
                          │
                          ↓
                   Research Insight
                          │
                          ↓
                     Opportunity
                          │
             ┌────────────┴────────────┐
             ↓                         ↓
          Planner                  Analyzer
                          │
                          ↓
                  Research Digest
```

This graph is the normalized realization of the PRD's recommended Research data architecture. fileciteturn28file2L94-L103

---

# 80. Core Invariants

```text
1. Research Workspace is the research context and is scoped to Product Workspace.

2. Competitor is a tracked external account/entity.

3. Competitor history is snapshot-based, not current-state-only.

4. Content Observation is the canonical observed-content record.

5. Topic is reusable across competitor/trend/keyword/audience/own-content research.

6. Hook Pattern and CTA Pattern are reusable pattern entities linked to observations.

7. Trend is represented as a Trend Signal, not only a label.

8. Audience demand is represented through Audience Signals.

9. Insight explains what the data means.

10. Opportunity explains what action may be taken.

11. Evidence is traceable for important Research outputs.

12. Confidence is separate from score.

13. Freshness is explicitly tracked.

14. Observed and estimated data are distinguishable.

15. Insufficient data is represented honestly.

16. Provider-specific schemas never become canonical Research schemas.

17. Research does not create a second Analytics metric truth.

18. Research does not create a second Analyzer source/evidence truth.

19. Historical snapshots are not silently rewritten by live refreshes.

20. Research Reset is module-local.

21. General Research may exist without content_slot_id.

22. Slot-specific Research may reference content_slot_id.

23. Research → Planner sends structured opportunity metadata.

24. Research → Analyzer sends structured source/evidence/angle context.

25. Cross-Research Correlation is a processing layer, not a duplicate tab/data silo.

26. Third-party estimates are never presented as exact observed facts.

27. Research recommendations always preserve a "why".

28. Hashtag remains supporting metadata, not the central Research object.
```

---

# 81. Definition of Done

Core Contract #10 is complete when:

### Research Context

1. Research Workspace exists.
2. Research Workspace is tied to broader Workspace.
3. Niche/brand/audience context can be referenced.

### Competitive Intelligence

4. Competitor exists.
5. Competitor snapshots exist.
6. Historical comparison is supported.
7. Content Observation exists.
8. Hook Pattern exists.
9. CTA Pattern exists.

### Topic / Trend / Search

10. Topic exists.
11. Trend Signal exists.
12. Trend lifecycle is represented.
13. Trend history is core-ready.
14. Keyword exists.
15. Keyword Cluster exists.
16. Search Intent exists.

### Audience

17. Audience Signal exists.
18. Pain points/questions/desire/objection/sentiment/language/content-demand signals are supported.
19. Manual and Analytics-derived audience data can coexist.
20. Source type/provenance is preserved.

### Intelligence

21. Research Evidence exists.
22. Research Insight exists.
23. Opportunity exists.
24. Opportunity score is configurable.
25. Confidence exists.
26. Freshness exists.
27. Observed vs estimated is explicit.
28. Insufficient data is represented.
29. Research Digest exists.
30. Historical digest snapshots are supported.

### Provider / Execution

31. Research Run exists.
32. Provider Result exists.
33. Raw provider data is separated from normalized Research data.
34. Provider provenance is preserved.
35. Provider conflict can be represented.

### Correlation / Integration

36. Cross-Research Correlation exists as a domain capability.
37. Research → Planner handoff exists.
38. Research → Analyzer handoff exists.
39. Saved Insight snapshot exists.
40. `content_slot_id` is optional and supported.
41. Research can exist independently of Content Slot.

### Persistence / Governance

42. Research persists across sessions.
43. Reset is module-local.
44. Research events integrate with Event Infrastructure.
45. Important Research actions are auditable.
46. Research access respects Workspace/Role/Tenant scope.

---

# 82. Implementation Priority

Based on the final PRD:

## P0

```text
Research Overview
Opportunity Engine
Competitor Deep Analysis
Own Content Intelligence
Content Gap
Core Research Workspace
Competitor
Content Observation
Topic
Opportunity
Research Insight
Basic Evidence / Confidence / Freshness
```

The PRD explicitly marks Research Overview + Opportunity Engine, Competitor deep analysis, Own Content Intelligence, and Content Gap as P0. fileciteturn29file0L198-L205

## P1

```text
Trend Lifecycle + Velocity
Audience Questions / Pain Points
Hook Intelligence
CTA Intelligence
Keyword Intent + Clusters
Audience Signal enrichment
```

These are explicitly listed as P1. fileciteturn29file0L206-L209

## P2

```text
Historical Snapshots
Cross-Research Correlation
Advanced historical comparison
```

These are explicitly listed as P2. fileciteturn29file0L210-L211

---

# 83. Dependencies

Depends on:

```text
Core Contract #1
Identity / User / Session

Core Contract #2
Role / Permission

Core Contract #3
Configuration

Core Contract #6
Provider Pool / Integration

Core Contract #7
Storage

Core Contract #8
Audit / Events / Notifications

Core Contract #9
Workspace / Content Slot / Project Context
```

Integrates with:

```text
Core Contract #4
Product / Entitlement

Core Contract #11
Planner

Core Contract #12
Analyzer

Analytics Engine
```

Produces:

```text
Research Insight
Opportunity
Research Digest
Planner Handoff
Analyzer Handoff
```

---

# 84. Architecture Boundary Notes

This section is intentionally included because this is a **Core Contract**, not a second PRD.

The following are domain concepts:

```text
ResearchWorkspace
Competitor
ContentObservation
Topic
HookPattern
CtaPattern
TrendSignal
Keyword
AudienceSignal
ResearchEvidence
ResearchInsight
Opportunity
ResearchDigest
```

The following are execution/integration concepts:

```text
ResearchRun
ProviderResult
Correlation Engine
```

The following remain infrastructure concerns:

```text
Provider Pool
Credential Management
Storage
Event Bus
Audit
Notification
Authorization
```

Core Architecture must decide whether these become:

```text
separate service
module
bounded context
worker
shared library
```

This contract does **not** force a 1:1 service mapping.

---

# 85. Replacement Note

This document supersedes the previous Core Contract #10.

The main correction is that the normalized domain is now explicitly aligned to the PRD's required Research data chain:

```text
Research Workspace
→ Competitor
→ Content Observation
→ Topic
→ Hook / CTA Pattern
→ Trend Signal
→ Audience Signal
→ Opportunity
→ Research Insight
→ Research Digest
```

rather than relying primarily on the earlier generic:

```text
ResearchSource
→ ResearchRun
→ Evidence
→ Claim
→ Theme
→ Opportunity
```

The earlier concepts remain where still useful, but they are now subordinate to the PRD canonical Research model rather than competing with it.

---

# 86. Next Contract

The next work item is:

> **Core Contract #13 revision** — align Blueprint more explicitly with the PRD's `Angle × Content Type` production model and the two official Module 3 add-ons: **Visual Continuity Engine** and **Advanced Prompt Studio & Auto-Fix**.

After that revision, the Core Contracts will be in a substantially cleaner state for the **Core Architecture** phase.
