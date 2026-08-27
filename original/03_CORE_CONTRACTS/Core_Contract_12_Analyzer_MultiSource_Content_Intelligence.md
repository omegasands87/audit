# Core Contract #12 — Analyzer & Multi-Source Content Intelligence

## Status

**Draft for Core Design — based on the finalized PRD, Final Business Decision Register, and Core Contracts #1–#11**

This contract defines the **AI Multi-Source Analyzer** as the intelligence layer between:

```text
Research / Content Source
        ↓
Source Ingestion
        ↓
Quality Gate
        ↓
Structured Extraction
        ↓
Evidence / Claim
        ↓
Insight / Opportunity
        ↓
Angle
        ↓
Content Readiness
        ↓
Script / Content Production Blueprint
```

The Analyzer is not merely a summarizer.

Its responsibility is to produce:

```text
evidence
claim
insight
opportunity
angle
readiness
```

with provenance, confidence, and anti-hallucination controls.

The PRD explicitly positions Analyzer as a **Content Intelligence Layer** rather than a generic summarizer. fileciteturn17file0L135-L139

---

# 1. Scope

This contract covers:

- Analyzer Run;
- Source Ingestion;
- source classification;
- source validation;
- source quality gate;
- duplicate detection/reuse;
- structured extraction;
- core facts;
- key points;
- fact/opinion/interpretation/prediction/anecdote classification;
- tone/sentiment;
- statistics;
- evidence references;
- angle generation;
- angle family;
- hook generation;
- hook accuracy;
- audience fit;
- Virality Potential Score;
- anti-hallucination;
- Deep Source Intelligence;
- Media Intelligence;
- Cross-Source Analysis;
- Multi-AI;
- confidence;
- disagreement;
- Content Readiness;
- Analyzer output versioning;
- Analyzer → Script/Blueprint Input Contract;
- persistence/reset;
- provider traceability.

It does not define:

- Research provider routing;
- normalized ResearchSource storage rules;
- Planner calendar generation;
- Script generation;
- Asset generation;
- Editor;
- Analytics ingestion.

Those domains connect through contracts already defined.

---

# 2. Source Principles

The PRD defines Analyzer as the second Content Engine module and specifies that its role is to transform raw sources into:

```text
evidence
claim
insight
opportunity
angle
```

ready for production.

The PRD also requires Analyzer results to persist across refresh/logout/login and provides an independent Reset Analyzer action. fileciteturn15file3L124-L127

The PRD defines the Analyzer pipeline as:

```text
Default:
Ingest
→ Identify & Classify Source
→ Quality Gate Dasar
→ Structured Extraction
→ Fact/Opinion Split
→ Evidence Reference
→ Angle Generation
→ Virality Potential Score

Optional:
Deep Source Intelligence
→ Media Intelligence
→ Cross-Source Analysis
→ Multi-AI

↓
Script Review
```

The explicit pipeline and monetization split are defined in the PRD. fileciteturn17file0L151-L171

---

# 3. Core Principle — Analyzer Uses Research Core Entities

Analyzer must reuse the normalized Research model from Core Contract #10.

It must not create a competing source model.

Relationship:

```text
ResearchSource
      ↓
AnalyzerRun
      ↓
AnalyzerResult
      ├── Evidence
      ├── Claims
      ├── Insights
      └── Angles
```

Where a source is not already represented in Research, Analyzer may create/resolve a `ResearchSource` through the shared Source service.

---

# 4. Analyzer Run

Conceptual entity:

```text
AnalyzerRun
```

Minimum fields:

| Field | Purpose |
|---|---|
| `analyzer_run_id` | stable run identity |
| `user_id` | user |
| `workspace_id` | workspace |
| `content_slot_id` | optional slot context |
| `input_type` | URL/media/concept |
| `source_ids` | normalized source references |
| `requested_capabilities` | default/add-ons |
| `provider_context` | provider/model trace |
| `status` | run state |
| `started_at` | start |
| `completed_at` | completion |
| `created_at` | creation |
| `updated_at` | update |

---

# 5. Analyzer Run Status

Minimum:

```text
QUEUED
RUNNING
PARTIAL
COMPLETED
FAILED
CANCELLED
SUPERSEDED
```

A later analysis of the same source can supersede a previous current result without destroying required history.

---

# 6. Analyzer Input Modes

PRD defines three primary input options:

### URL Extractor

Supports:

```text
Article
Blog
Video
Reference URL
```

and multiple URLs when Cross-Source Analysis is active.

### Media Uploader

Supported classes include:

```text
Video
Audio
Document
Image
```

### Raw Concept Note

A free-form:

```text
Raw Concept
```

input for ideas, rough notes, or questions.

---

# 7. Source Ingestion Boundary

Analyzer receives:

```text
Canonical Source Reference
```

and relies on:

```text
Source Service
+
Provider / Extraction Layer
```

to access content.

Analyzer does not embed:

- provider URL logic;
- API credentials;
- scraping implementation;
- storage credentials.

---

# 8. Source Identity

Every Analyzer input must resolve to:

```text
source_id
```

where possible.

Source identity includes the normalized metadata defined in Core Contract #10.

Examples:

```text
YouTube Video
Article
PDF
Podcast
Image
Raw Concept
```

---

# 9. Source Classification

Default classification is required.

Supported source classes may include:

```text
News
Article
Blog
Research / Paper
Report
Document
Video
Podcast / Audio
Interview
Social Post
Thread
Forum
Product Page
Advertisement
User-Generated
Image
Raw Concept
Other
```

The exact extraction profile depends on source type.

The PRD explicitly requires source classification before heavy analysis. fileciteturn17file0L173-L180

---

# 10. Pre-Analysis Quality Gate

Before expensive AI analysis:

```text
Validate Source
   ↓
Quality Gate
   ↓
Proceed / Reject
```

Checks may include:

- source accessible;
- supported format;
- extraction success;
- transcription/OCR quality;
- language detected;
- duration reasonable;
- content non-empty;
- content not too short.

If minimum quality is not met:

> **"Sumber tidak dapat dianalisis dengan andal."**

The system should not force an unreliable output merely to produce a result.

---

# 11. Quality Gate Status

Recommended:

```text
PASS
PASS_WITH_WARNING
FAIL
```

Example:

```text
Video
Transcript quality = low
→ PASS_WITH_WARNING
```

or:

```text
PDF
Unreadable
→ FAIL
```

---

# 12. Duplicate / Reuse Detection

If a source was previously analyzed:

```text
Existing Analysis Found
```

Member can choose:

```text
Gunakan Analisa Sebelumnya
Analisa Ulang
```

If source content has changed:

```text
Konten sumber berubah sejak analisa terakhir
```

The system can identify the prior result and avoid unnecessary re-analysis.

This follows the PRD's duplicate/reuse requirement. fileciteturn17file0L173-L180

---

# 13. Source Content Version

Analyzer should distinguish:

```text
same source identity
```

from:

```text
changed source content
```

Use a content fingerprint/hash where practical.

Example:

```text
source_id = S123
content_version = 4
```

A new version does not create an unrelated source identity unless the source itself changed identity.

---

# 14. Structured Extraction

Default Analyzer output must be structured.

Minimum:

```text
General Summary
Core Facts
Key Points
Fact / Opinion / Interpretation / Prediction / Anecdote
Tone & Sentiment
Supporting Statistics
Evidence References
```

The PRD explicitly requires this structured approach instead of generic summarization. fileciteturn17file0L181-L190

---

# 15. General Summary

Summary is a synthesis of the source.

It must not:

- introduce unsupported facts;
- replace evidence;
- change the source's meaning.

---

# 16. Core Facts

Structured facts can include:

```text
Who
What
When
Where
Why
How
Numbers
Dates
Percentages
Sequence
```

Every important factual statement should retain an evidence reference.

---

# 17. Key Points / Talking Points

Key points are concrete concepts that can later support:

```text
slide
shot
script point
hook
narrative beat
```

They remain distinct from final Script content.

---

# 18. Semantic Classification

Each important extracted point should be classified as:

```text
FACT
OPINION
INTERPRETATION
PREDICTION
ANECDOTE
TESTIMONIAL
```

This prevents source opinion from being presented as factual certainty.

---

# 19. Tone & Sentiment

Capture the source's original tone:

```text
Formal
Casual
Neutral
Emotional
Urgent
Critical
Optimistic
etc.
```

Tone is descriptive metadata.

It does not automatically become the member's brand voice.

---

# 20. Statistics

Extract:

```text
Metric
Value
Unit
Time Period
Context
Relative/Absolute
Evidence Reference
Verification Status
```

Example:

```text
Value:
42%

Context:
completion rate

Time:
2026-08
```

A number without sufficient context should receive a warning rather than false precision.

---

# 21. Evidence Reference

Default Analyzer provides lightweight evidence references.

Examples:

```text
Paragraph
Page
Timestamp
Section
Comment
Visual Region
```

UI can expose:

```text
"Lihat Sumber"
```

for supported evidence.

The evidence model reuses Core Contract #10.

---

# 22. Claim Registry Compatibility

The normalized Research `SourceClaim` model must be reused when Deep Source Intelligence is enabled.

Analyzer may enrich claims with:

```text
claim_risk
verification_status
temporal_analysis
evidence_count
confidence
```

rather than create a separate claim entity.

---

# 23. Angle Generation

An Analyzer Run can generate multiple angles.

Each angle must include:

```text
Name
Description
Differentiation Reason
Audience Fit
Content Type Compatibility
Hook Options
Suggested Pillar
Evidence
Confidence
Virality Potential
```

The PRD explicitly states that one source can produce multiple angles and an angle may be compatible with image and/or video. fileciteturn16file7L330-L337

---

# 24. Angle Family

Use the extensible `angle_family` from Core Contract #10.

Examples:

```text
Educational
How-To
Problem / Solution
Contrarian
Myth vs Reality
Story
Case Study
Data / Stats
List
Comparison
Warning
Prediction
Opinion / Commentary
Before / After
Q&A
Behind the Scenes
Authority / Expert Insight
```

Only families supported by the source should be generated.

The system must not fabricate variety merely to present many cards.

---

# 25. Angle Differentiation

Each angle should explain:

> **Why is this angle meaningfully different from other angles from the same source?**

Possible dimensions:

```text
Audience
Problem
Narrative
Evidence
Format
Emotion
Contrarian Position
Utility
```

---

# 26. Audience Fit

Angle should be evaluated against:

```text
Audience Insight
```

from Research Intelligence when available.

This can include:

```text
segment
pain point
question
desire
objection
```

If audience information is missing:

```text
confidence = lower / unknown
```

rather than inventing an audience assumption as fact.

---

# 27. Hook Generation

Analyzer may generate multiple hooks/titles where supported by source richness.

Rules:

- do not fabricate evidence;
- do not alter meaning;
- do not exaggerate unsupported causality;
- do not remove material context;
- do not create excessive variants merely to inflate options.

---

# 28. Hook Accuracy Check

Every generated hook should pass a lightweight accuracy check:

```text
Does it exaggerate?
Does it change the meaning?
Does it imply unsupported causality?
Does it remove important context?
```

If it fails:

```text
Revise to safer version
```

before showing it to the member.

This is explicitly required by the PRD. fileciteturn17file0L192-L220

---

# 29. Suggested Content Pillar

Analyzer can suggest:

```text
content_pillar
```

to accelerate Planner decisions.

This is a recommendation, not a forced Planner value.

---

# 30. Content Type Compatibility

An angle may support:

```text
Image / Carousel
Video Pendek
Both
```

The relationship is non-exclusive.

Do not assume:

```text
one angle = one format
```

---

# 31. Virality Potential Score

Analyzer exposes:

```text
Virality Potential Score
0–100
```

The score is a decision-support heuristic.

It is:

> **not a prediction of viral performance and not a guarantee of results.**

The PRD explicitly warns against treating this score as an independent production decision. fileciteturn17file0L202-L220

---

# 32. Default Score Factors

The PRD specifies these starting heuristic weights:

| Factor | Default |
|---|---:|
| Hook Strength | 15% |
| Curiosity / Information Gap | 10% |
| Emotional Trigger | 10% |
| Relatability | 10% |
| Share Motivation | 10% |
| Novelty / Freshness | 10% |
| Utility / Value | 10% |
| Tension / Contrarian Potential | 8% |
| Current Trend Fit | 7% |
| Format-Algorithm Fit | 5% |
| Audience Fit | 5% |

Total:

```text
100%
```

These are starting heuristics, not ground truth.

---

# 33. Score Configuration

Weights are configurable through Core Configuration.

However:

> The score definition must remain versioned.

Example:

```text
Virality Score Model v1
```

Later:

```text
Virality Score Model v2
```

Historical results should retain the scoring model version used.

---

# 34. Score vs Confidence

Do not combine:

```text
score
```

and:

```text
confidence
```

Example:

```text
Virality Potential = 85
Confidence = 0.58
```

means:

> characteristics look promising, but evidence supporting that assessment is limited.

---

# 35. Anti-Hallucination — Non-Negotiable

These rules cannot be disabled by:

```text
Membership
Role
Add-on
Provider
```

AI may:

- summarize;
- classify;
- connect evidence;
- interpret with explicit labeling;
- generate creative hooks that preserve factual meaning.

AI may not:

- invent numbers;
- invent sources;
- invent quotations;
- misattribute quotes;
- convert opinion to fact;
- remove context that changes meaning;
- claim unsupported causality;
- fabricate AI consensus;
- make a headline more sensational than evidence without labeling.

The PRD explicitly marks these as non-negotiable. fileciteturn17file0L222-L228

---

# 36. Interpretation Labeling

If the Analyzer produces interpretation:

```text
Interpretasi AI
```

must be distinguishable from:

```text
Fakta sumber
```

This distinction must survive handoff to downstream modules.

---

# 37. Deep Source Intelligence

Deep Source Intelligence is an optional analysis capability.

It may add:

```text
Source Quality Score
Content Richness Score
Full Claim & Evidence Registry
Claim Risk
Verification
Temporal Analysis
Novelty / Saturation
Contrarian / Tension
Emotional Intelligence
Content Value Score
Transformation Potential Score
Angle Qualification
Content Readiness
```

The PRD explicitly separates these from default Analyzer capability. fileciteturn15file0L17-L26

---

# 38. Deep Source Intelligence Commercial Boundary

The current final billing architecture is:

```text
Product / Package / Add-on
→ Order
→ Payment
→ Entitlement
```

Analyzer must not implement:

```text
wallet
PAYG balance
general deposit deduction
```

directly.

If Deep Source Intelligence is sold separately, the Entitlement Service determines whether the capability is available.

---

# 39. Media Intelligence

Media Intelligence applies to:

```text
Video
Audio
Image
```

Possible outputs:

```text
Visual Intelligence
Audio / Speech Intelligence
Narrative Structure
Peak Moments
Quote Intelligence
```

These remain extensions of the same Source/Evidence model.

---

# 40. Media Evidence

Examples:

```text
video timestamp
audio timestamp
visual region
frame reference
speaker
```

Media evidence must use normalized references rather than vendor-specific result structures.

---

# 41. Cross-Source Analysis

When enabled:

```text
Source A
Source B
Source C
   ↓
Cross-Source Analysis
```

Outputs can include:

```text
Agreement
Contradiction
Shared Theme
Unique Insight
Source Expansion Recommendation
Cross-Source Synthesis
```

The original source identities and evidence remain individually traceable.

---

# 42. Cross-Source Conflict

If sources disagree:

```text
Source A:
Claim X

Source B:
Claim not-X
```

the Analyzer must not silently pick one as truth.

Instead:

```text
Conflict Detected
→ Evidence shown
→ Confidence affected
→ Verification required where appropriate
```

---

# 43. Multi-AI

Multi-AI runs:

```text
AI Model A
AI Model B
AI Model C
```

across relevant pipeline stages when enabled.

The PRD requires cross-validation across:

```text
extraction
→ claim
→ interpretation
→ angle
→ score
```

not only final-angle comparison. fileciteturn15file2L86-L92

---

# 44. Multi-AI Observation

Each model's result should remain identifiable:

```text
model_id
provider_id
result
confidence
```

Then synthesis can calculate:

```text
agreement
disagreement
consensus
```

Do not replace all independent observations with only the final synthesized text.

---

# 45. False Consensus Prevention

If AI models disagree:

```text
Consensus = low
```

should affect confidence.

Do not output:

```text
3 AI agree
```

when only one model actually supports the conclusion.

---

# 46. Content Readiness

Deep Source Intelligence may produce:

```text
Content Readiness
```

Conceptual values:

```text
READY
READY_WITH_CAUTION
NEEDS_VERIFICATION
NOT_READY
```

Readiness is a production decision-support status.

It is not a guarantee that the eventual content is correct.

---

# 47. Content Readiness Inputs

Potential inputs:

```text
Source quality
Evidence coverage
Claim risk
Freshness
Audience relevance
Angle quality
Transformation potential
Contradictions
```

The exact formula can evolve.

---

# 48. Default vs Add-on Boundary

Default Analyzer must remain useful without paid depth.

### Default

```text
Source identity/provenance
Source classification
Basic quality gate
Duplicate detection
Structured extraction
Fact/opinion split
Light evidence
Angle generation
Angle family
Virality Potential Score
Hook accuracy
Anti-hallucination
```

### Optional Add-ons

```text
Deep Source Intelligence
Media Intelligence
Multi-AI
Cross-Source Analysis
```

This matches the PRD's default/add-on structure. fileciteturn15file0L15-L26

---

# 49. Analyzer Output Model

Conceptual:

```text
AnalyzerResult
├── run_id
├── source_ids
├── content_slot_id
├── summary
├── facts[]
├── key_points[]
├── classifications[]
├── evidence_refs[]
├── claims[]
├── themes[]
├── angles[]
├── score_model_version
├── readiness
├── confidence
├── provider_trace
├── created_at
└── version
```

Large source binaries remain in Storage.

---

# 50. Analyzer Result Versioning

Each completed analysis has a version.

Example:

```text
Analyzer Result v1
→ initial analysis

Analyzer Result v2
→ source changed / re-analysis

Analyzer Result v3
→ improved model
```

Current result can point to latest valid version.

Historical versions remain available according to policy.

---

# 51. Analyzer Reset

Reset is module-local.

```text
Reset Analyzer
```

must not delete:

```text
Research Tracker
Trend Explorer
Planner
Script
```

It may reset the Analyzer's current working result for the selected context.

Historical audit/event records remain.

---

# 52. Re-analysis

When member chooses:

```text
Analisa Ulang
```

create a new `AnalyzerRun`.

Do not mutate the historical result in place.

If source content changed:

```text
source_version
```

must be captured.

---

# 53. Persistence

Analyzer results must persist server-side.

They must survive:

```text
page refresh
navigation
logout
later login
```

as long as retention/access conditions permit.

This is required by the platform persistence model. fileciteturn15file3L124-L127

---

# 54. Analyzer → Script / Blueprint Input Contract

The Analyzer must output a structured handoff.

Minimum:

```text
content_slot_id

source
├── source_ids
├── provenance
└── evidence references

selected_angle
├── angle_id
├── angle_family
├── angle_text
├── hook
├── audience
└── format compatibility

opportunity
├── opportunity_id
├── opportunity_score
└── confidence

content_pillar
target_audience
claim/evidence references
readiness status
```

The PRD explicitly requires a structured Analyzer → Script input rather than free-form text and keeps the Content Production Blueprint downstream from Analyzer. fileciteturn15file2L97-L113

---

# 55. Angle Selection

Analyzer may produce multiple angles.

Member selects:

```text
Angle A
```

and passes only the selected angle into the default production path.

Other angles remain available as Analyzer results.

---

# 56. Multiple Format Output

A selected angle may support:

```text
Image
Video
```

The same angle can be used for both without forcing one to replace the other.

Each downstream production instance receives:

```text
content_slot_id
angle_id
format
```

so production variants remain distinguishable.

---

# 57. Analyzer Does Not Generate Final Script

Analyzer may produce:

```text
hook direction
talking points
angle
evidence
```

but final:

```text
script
visual blueprint
asset plan
editor mapping
```

belong to Content Production Blueprint / Script Review.

The PRD explicitly separates these responsibilities. fileciteturn15file2L97-L109

---

# 58. Analyzer Data Provenance

Every major generated result should be traceable to:

```text
source_id
evidence_id
research_run_id / analyzer_run_id
provider/model
```

This creates:

```text
Source
→ Evidence
→ Claim
→ Angle
→ Script
```

traceability.

---

# 59. Analyzer Provider Trace

Record:

```text
provider_id
model_id
request_id
prompt_version
analysis_stage
```

where available.

Secrets remain in Provider infrastructure.

---

# 60. Analyzer Cost / Entitlement Trace

When an optional paid analysis capability is used:

```text
Analyzer Run
→ capability_code
→ entitlement consumption reference
```

The Analyzer does not directly manipulate balance.

Entitlement Service owns consumption.

---

# 61. Failed Analysis

If analysis fails before a consumable operation is committed:

```text
Entitlement
→ not permanently consumed
```

If a paid analysis was successfully executed:

```text
entitlement consumption
→ committed
```

The exact retry/refund semantics belong to Entitlement/Payment contracts.

---

# 62. Partial Results

If some stages succeed and others fail:

```text
AnalyzerRun
= PARTIAL
```

Example:

```text
Extraction = complete
Evidence = complete
Angle = complete
Deep Source Intelligence = failed
```

The system can still expose valid default output while clearly labeling unavailable add-on results.

---

# 63. Unsupported Source

If source type is unsupported:

```text
Quality Gate
→ FAIL
→ clear explanation
```

Do not generate fabricated analysis.

---

# 64. Source Too Weak

If source is accessible but insufficient:

```text
Quality Gate
→ PASS_WITH_WARNING

Analysis:
limited
Confidence:
reduced
```

Do not force a high-confidence recommendation.

---

# 65. Content Freshness

If source is time-sensitive:

```text
freshness
```

must affect:

```text
confidence
readiness
recommendation
```

Example:

```text
News article from 2025
```

may be accessible but not suitable for a "current trend" conclusion in 2026.

---

# 66. Research vs Analyzer Responsibility

### Research Intelligence

Owns:

```text
external research ecosystem
competitors
trends
keywords
audience
research opportunities
```

### Analyzer

Owns:

```text
deep interpretation of a selected source/set
evidence/claim understanding
angle extraction
content readiness
```

Analyzer may consume Research Insight but does not replace Research Intelligence.

---

# 67. Analytics Relationship

Analytics may later provide:

```text
angle performance
hook performance
content format performance
```

These can inform future score calibration.

Analyzer historical results should not be silently rewritten because Analytics changes future weights.

---

# 68. Analytics Feedback Boundary

Future loop:

```text
Analyzer Score
→ Content Production
→ Published Content
→ Analytics
→ Performance Pattern
→ Future Analyzer Score Calibration
```

Calibration updates the scoring model version.

Historical Analyzer scores remain associated with their original model version.

---

# 69. Security / Access

Analyzer results may contain sensitive research/content information.

Access requires:

```text
authenticated session
+
workspace authorization
+
content slot ownership/scope
+
tenant boundary (future)
```

---

# 70. API Contract

Conceptual:

```text
POST /analyzer/runs
GET  /analyzer/runs/:id
GET  /content-slots/:id/analyzer

POST /analyzer/runs/:id/retry
POST /analyzer/runs/:id/reset
POST /analyzer/runs/:id/reanalyze

GET  /analyzer/runs/:id/angles
POST /analyzer/runs/:id/select-angle
```

Exact API naming will be refined in API Architecture.

---

# 71. Analyzer Service

Conceptual internal operations:

```text
AnalyzerService.ingest()
AnalyzerService.validate()
AnalyzerService.extract()
AnalyzerService.classify()
AnalyzerService.generateAngles()
AnalyzerService.scoreAngles()
AnalyzerService.runDeepAnalysis()
AnalyzerService.runMediaAnalysis()
AnalyzerService.runCrossSource()
AnalyzerService.runMultiAI()
AnalyzerService.evaluateReadiness()
AnalyzerService.buildProductionHandoff()
```

---

# 72. Analyzer Events

Domain events:

```text
AnalyzerRunCreated
AnalyzerRunStarted
AnalyzerRunQualityGatePassed
AnalyzerRunQualityGateFailed
AnalyzerExtractionCompleted
AnalyzerEvidenceCreated
AnalyzerAngleGenerated
AnalyzerAngleSelected
AnalyzerRunCompleted
AnalyzerRunPartial
AnalyzerRunFailed
AnalyzerRunReset
AnalyzerRunSuperseded
ContentReadinessChanged
```

These integrate with Core Contract #8.

---

# 73. Audit

Audit important actions:

```text
Analyzer Run Started
Analyzer Reset
Analyzer Re-analysis
Angle Selected
Paid Add-on Analysis Executed
Readiness Overridden (if ever allowed)
Manual Adjustment
```

AI generation itself may be evented without requiring a full user-facing audit entry for every internal token-level operation.

---

# 74. No Manual Override of Evidence Integrity

Admin/member may be able to annotate or flag a claim.

They must not silently rewrite source evidence.

If correction is necessary:

```text
Correction / Annotation
```

rather than altering the original source-derived evidence.

---

# 75. Core Invariants

```text
1. Analyzer uses the canonical Research Source/Evidence model.

2. Analyzer does not create a second incompatible source identity model.

3. Source quality gate happens before expensive analysis.

4. AI interpretation is distinguishable from source fact.

5. Evidence remains traceable.

6. Confidence is separate from scoring.

7. Virality Potential Score is not a guaranteed outcome.

8. Anti-hallucination rules cannot be disabled.

9. Source/provider disagreement is not silently hidden.

10. Multiple AI observations remain traceable.

11. Historical Analyzer results retain their analysis/scoring version.

12. Reset is module-local.

13. Re-analysis creates a new run/version.

14. Analyzer does not directly manipulate billing balance.

15. Paid analysis uses Entitlement Service for capability access/consumption.

16. Analyzer does not generate the final production script.

17. Analyzer output to Script/Blueprint is structured.

18. content_slot_id remains the production context anchor.

19. Provider changes do not invalidate normalized historical analysis.

20. Insufficient source quality can block or lower-confidence analysis.

21. Unsupported or inaccessible sources are not fabricated into results.

22. Historical evidence is not silently overwritten by live provider refreshes.
```

---

# 76. Definition of Done

Core Contract #12 is complete when:

1. Analyzer Run exists.
2. URL/media/raw concept inputs are supported.
3. Source identity is normalized through Research.
4. Source classification exists.
5. Pre-analysis quality gate exists.
6. Duplicate/reuse detection exists.
7. Content version/change detection exists.
8. Structured extraction exists.
9. Fact/opinion/interpretation classification exists.
10. Evidence references exist.
11. Claim registry compatibility exists.
12. Multiple angles can be generated.
13. Angle families exist.
14. Hook generation exists.
15. Hook accuracy checks exist.
16. Audience Fit exists.
17. Content Pillar recommendation exists.
18. Virality Potential Score exists.
19. Score model is versioned.
20. Confidence is independent of score.
21. Anti-hallucination is enforced.
22. Deep Source Intelligence can extend the analysis.
23. Media Intelligence can extend the analysis.
24. Cross-Source Analysis can compare sources.
25. Multi-AI can preserve independent model observations.
26. Disagreement affects confidence/interpretation.
27. Content Readiness exists where applicable.
28. Analyzer results persist across sessions.
29. Reset remains local to Analyzer.
30. Re-analysis creates a new run/version.
31. `content_slot_id` is preserved.
32. Analyzer → Script/Blueprint handoff is structured.
33. Provider/model traceability exists.
34. Paid capability access is controlled by Entitlement.
35. Audit/Event integration exists.

---

# 77. Implementation Priority

Based on the PRD:

## P0 — Default Analyzer

```text
Source Identity / Provenance
Source Classification
Basic Quality Gate
Duplicate Detection
Structured Extraction
Fact / Opinion Split
Light Evidence Reference
Angle Generation
Angle Family
Virality Potential Score
Hook Accuracy Check
Anti-Hallucination
Persistence
Reset
```

## P1 — Add-on / Enhanced Intelligence

```text
Deep Source Intelligence
Media Intelligence
Multi-AI
Cross-Source Analysis
```

## Roadmap / P2

```text
Source/Angle Pattern Performance Learning
Analytics-based Automatic Weight Calibration
Knowledge Graph
Predictive Source Quality
```

The PRD explicitly places these advanced capabilities after sufficient historical data is available. fileciteturn15file0L17-L26

---

# 78. Dependencies

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

Core Contract #8
Audit / Events / Notifications

Core Contract #9
Workspace / Content Slot / Project Context

Core Contract #10
Research Intelligence
```

Consumes:

```text
ResearchSource
SourceEvidence
SourceClaim
AudienceInsight
ContentOpportunity
```

Produces:

```text
AnalyzerResult
ContentAngle
ContentReadiness
Structured Production Handoff
```

Used by:

```text
Script / Content Production Blueprint
Asset Preparation
Analytics
Future Learning Engine
```

---

# 79. Next Contract

The next recommended contract is:

> **Core Contract #13 — Content Production Blueprint / Script Review**

It should define:

```text
Selected Opportunity
Selected Angle
Evidence Context
Narrative
Hook
Script
Visual Blueprint
Structured Prompts
Asset Plan
Editor Mapping
Production QA
Script Versioning
Analyzer → Blueprint traceability
Blueprint → Asset Preparation handoff
```

This follows the PRD's explicit boundary: Analyzer completes intelligence/angle/readiness, while Module 3 converts the validated opportunity/angle/evidence into the production blueprint. fileciteturn15file2L97-L109
