# Core Contract #13 — Content Production Blueprint / Script Review

## Status

**Revised — Replacement Version**

This document replaces the previous **Core Contract #13 — Content Production Blueprint / Script Review**.

Revision purpose:

> Align Module 3 completely with the final PRD by making **Blueprint Variant (Angle × Content Type)** explicit and formally including the two official Module 3 add-ons:
>
> 1. **Visual Continuity Engine**
> 2. **Advanced Prompt Studio & Auto-Fix**

The core boundary remains:

```text
Analyzer
   ↓
Selected Opportunity + Angle + Evidence
   ↓
Content Production Blueprint
   ↓
Blueprint Variant
   ↓
Production QA
   ↓
Asset Preparation
   ↓
Editor
```

The final PRD explicitly positions Module 3 as a **production layer**, not a second analysis layer. fileciteturn18file0L22-L34

---

# 1. Scope

This contract covers:

- Analyzer → Blueprint Input Contract;
- Content Production Blueprint;
- Blueprint Variant;
- Angle × Content Type relationship;
- selected angle lock;
- content context;
- narrative structure;
- script;
- hook refinement;
- source/evidence traceability;
- fact / interpretation / creative copy separation;
- image/carousel blueprint;
- video blueprint;
- voiceover;
- subtitle segments;
- shot list;
- visual direction;
- structured prompts;
- asset requirements;
- asset strategy;
- editor mapping;
- caption/hashtag suggestions;
- Production QA;
- Editability Matrix;
- regeneration;
- versioning;
- member edits;
- Blueprint approval;
- Asset Preparation handoff;
- Editor handoff;
- Visual Continuity Engine;
- Advanced Prompt Studio & Auto-Fix;
- persistence/autosave.

It does not define:

- Research provider implementation;
- Research data normalization;
- Analyzer internals;
- actual image/video generation execution;
- final Editor rendering;
- Export Engine;
- Analytics ingestion.

---

# 2. Source Principles

The final PRD defines Module 3 as a production layer and establishes the following boundary.

### Analyzer owns

```text
Source Understanding
Fact / Claim / Quote
Evidence Mapping
Reliability / Risk / Freshness
Audience Relevance
Novelty
Emotion / Tension / Story
Opportunity
Angle Family
Angle + Hook Validation
Scoring
Cross-validation
Content Readiness
```

### Module 3 owns

```text
Narrative
Script
Validated Hook Refinement
Visual Blueprint
Structured Prompt
Asset Strategy
Editor Mapping
Production QA
Production-ready Blueprint
```

Module 3 must not silently create a second source of truth for Analyzer evidence, claims, opportunity, or selected angle. fileciteturn18file3L126-L140

The PRD also explicitly states that the resulting production unit is defined for each:

```text
Angle × Content Type
```

combination, with one production context and its related script, opportunity, angle, evidence binding, asset requirements, prompts, editor mapping, and Production QA. fileciteturn27file0L4-L6

---

# 3. Core Principle — Blueprint vs Blueprint Variant

This contract introduces two related entities.

```text
Content Production Blueprint
        ↓
Blueprint Variant
```

## Blueprint

Represents the overall production intent inherited from Analyzer.

It contains:

```text
content_slot_id
opportunity
selected angle
audience
objective
evidence context
```

## Blueprint Variant

Represents one concrete production realization:

```text
Angle × Content Type
```

Examples:

```text
Angle A × Image
Angle A × Carousel
Angle A × Video Short
```

The Variant owns the concrete production structure:

```text
script
slides/shots
visual blueprint
prompts
asset requirements
editor mapping
QA
```

This prevents one generic blueprint from mixing incompatible production structures.

---

# 4. Content Production Blueprint Entity

Conceptual:

```text
ContentProductionBlueprint
```

Minimum fields:

| Field | Purpose |
|---|---|
| `blueprint_id` | stable identity |
| `content_slot_id` | production context anchor |
| `analyzer_run_id` | Analyzer source reference |
| `opportunity_id` | selected opportunity |
| `selected_angle_id` | selected angle |
| `audience_context` | inherited audience context |
| `objective` | production objective |
| `status` | overall blueprint status |
| `version` | blueprint version |
| `created_by` | creator |
| `created_at` | creation |
| `updated_at` | update |

---

# 5. Blueprint Status

Recommended:

```text
DRAFT
GENERATING
READY_FOR_REVIEW
NEEDS_REVISION
APPROVED
SUPERSEDED
```

The Blueprint status is separate from the Content Slot lifecycle:

```text
Draft
→ Source Needed
→ Script Ready
→ Asset Ready
→ Editing
→ Exported
```

A Blueprint may be approved while downstream assets have not yet been generated.

---

# 6. Blueprint Variant Entity

Conceptual:

```text
BlueprintVariant
```

Minimum:

| Field | Purpose |
|---|---|
| `blueprint_variant_id` | stable variant identity |
| `blueprint_id` | parent blueprint |
| `content_slot_id` | production context |
| `angle_id` | selected angle |
| `content_type` | Image / Carousel / Video Pendek |
| `variant_key` | deterministic combination key |
| `status` | variant lifecycle |
| `version` | variant version |
| `is_selected` | active/selected variant |
| `created_at` | creation |
| `updated_at` | update |

Recommended uniqueness:

```text
UNIQUE(
    content_slot_id,
    angle_id,
    content_type
)
```

This prevents duplicate production variants for the same:

```text
Content Slot
+
Angle
+
Content Type
```

unless an explicit version/supersede workflow creates a new revision.

---

# 7. Variant Lifecycle

Recommended:

```text
DRAFT
   ↓
GENERATING
   ↓
READY_FOR_REVIEW
   ↓
APPROVED
   ↓
SENT_TO_ASSET_PREPARATION
   ↓
SUPERSEDED
```

If production QA blocks:

```text
READY_FOR_REVIEW
→ NEEDS_REVISION
```

---

# 8. Angle × Content Type Matrix

A selected Analyzer angle may support one or more content types.

Example:

```text
Angle A
├── Image
├── Carousel
└── Video Pendek

Angle B
├── Carousel
└── Video Pendek
```

The system must only create variants for content types marked as compatible by the Analyzer/production policy.

Do not force every angle into every format.

---

# 9. One Variant = One Production Contract

A `BlueprintVariant` is the production contract consumed by downstream Asset Preparation and Editor.

It must contain or reference:

```text
1 content_slot_id
1 opportunity_id
1 angle_id
1 content_type
1 script structure
N slide/shot definitions
N visual blueprint items
N evidence bindings
N asset requirements
N prompts
N editor mappings
1 Production QA result
```

For Video, additionally:

```text
1 voiceover script
N subtitle segments
```

This matches the final PRD acceptance structure. fileciteturn27file0L4-L6

---

# 10. Analyzer → Blueprint Input Contract

Analyzer must provide structured context.

Minimum:

```text
content_slot_id

source
├── source_ids
├── source_type
├── source_quality
└── freshness

content_opportunity
├── opportunity_id
├── title
├── problem
├── why_now
├── utility
├── novelty
├── transformation_potential
└── visual_opportunity

selected_angle
├── angle_id
├── angle_family
├── title
├── description
├── differentiation
├── audience_fit
├── format_fit
├── pillar
├── confidence
└── claim_risk

audience
├── awareness_level
├── pain_points
├── desires
├── objections
├── intent
└── share_motive

hooks
├── candidates
├── selected_hook
└── hook_validation

evidence
├── evidence_items
├── claims
├── statistics
├── quotes
├── contradictions
└── missing_context

risk
├── claim_risk
├── verification_required
└── risk_flags

signals
├── emotional_signals
├── visual_signals
├── narrative
└── themes

scores
├── source_quality
├── content_value
├── transformation_potential
└── virality_potential

readiness
├── status
└── blockers
```

This inherits the structured Analyzer boundary from the final PRD.

---

# 11. Selected Angle Lock

Once the member selects an angle:

> **The selected angle is locked as the production intent.**

Module 3 must not silently replace the selected angle.

If the angle is weak for a chosen content type:

```text
Warning
```

must be shown.

Possible action:

```text
Create another Variant
```

rather than silently changing:

```text
angle_id
```

---

# 12. Variant-specific Adaptation

The same angle may be adapted to different content types.

Example:

```text
Angle A × Carousel
→ explanatory slide sequence

Angle A × Video
→ spoken narrative + shot list
```

The **core thesis and evidence basis remain shared**, while:

```text
narrative
hook phrasing
visual direction
asset strategy
editor mapping
```

may differ by variant.

---

# 13. Content Context

Every Blueprint Variant inherits:

```text
Content Slot
Content Type
Content Pillar
Audience
Objective
Selected Angle
Opportunity
Source Context
```

This context must remain traceable.

---

# 14. Objective

Configurable examples:

```text
Awareness
Education
Engagement
Authority
Conversion
Community
Promotion
```

Objective informs:

```text
hook
narrative
CTA
visual strategy
production emphasis
```

It does not override source evidence.

---

# 15. Narrative Structure

The Blueprint Variant creates a production-specific narrative.

Possible generic structure:

```text
Hook
→ Context
→ Development
→ Evidence / Example
→ Payoff
→ CTA
```

The exact sequence can vary according to:

```text
content_type
objective
angle_family
```

Video should use a structured narrative rather than a generic paragraph.

---

# 16. Script Entity

Conceptual:

```text
Script
```

Minimum:

```text
script_id
blueprint_variant_id
content_slot_id
version
language
hook
body
cta
word_count
estimated_duration
status
created_at
updated_at
```

Script ownership belongs to the Blueprint Variant.

---

# 17. Script Versioning

Important revisions create versions:

```text
v1
v2
v3
```

Historical versions remain available.

A new version must preserve:

```text
created_by
created_at
change_reason
```

where applicable.

---

# 18. Fact / Interpretation / Creative Copy

Blueprint Variant must preserve the distinction:

```text
Source Fact
↓
Interpretation
↓
Creative Copy
```

Creative Copy may be persuasive but must not:

- change factual meaning;
- change certainty;
- invent evidence;
- misattribute a source;
- imply unsupported causality.

---

# 19. Evidence Binding

Every important production claim can bind to:

```text
evidence_id
source_id
claim_id
```

Conceptual entity:

```text
EvidenceBinding
```

Fields:

```text
evidence_binding_id
blueprint_variant_id
target_type
target_id
evidence_id
claim_id
binding_type
```

Example targets:

```text
script paragraph
slide
shot
headline
hook
caption
```

---

# 20. Evidence Revalidation

When a member edits:

```text
hook
headline
statistics
claim wording
caption
```

the affected evidence bindings must be checked.

Result:

```text
PASS
WARNING
REQUIRES_VERIFICATION
BLOCKED
```

The system must not silently convert an evidence-backed statement into an unsupported claim.

---

# 21. Hook Pipeline

Canonical:

```text
Analyzer Hook Candidates
        ↓
Variant-compatible Hook
        ↓
Script Refinement
        ↓
Hook Validation
        ↓
Final Hook
```

If member manually changes the hook:

```text
Run Hook Accuracy Check
```

again.

---

# 22. Image / Carousel Variant

For:

```text
Image
Carousel
```

the production unit is:

```text
Slide
```

Each slide may contain:

```text
slide_id
sequence
purpose
headline
supporting_copy
visual_direction
asset_strategy
prompt_ref
evidence_refs
layout_template_ref
```

---

# 23. Carousel Copy Policy

Default guidance from the PRD:

```text
Headline:
8–12 words

Supporting Copy:
20–30 words

One Slide:
one core message

Text Blocks:
maximum 2–3

Text Hierarchy:
maximum 3 levels
```

If density is too high:

```text
QA Warning
```

with suggestions:

```text
Shorten copy
Split slide
Replace text with visual
```

---

# 24. Video Variant

Video Variant must contain:

```text
Voiceover Script
Subtitle Segments
Shot List
Visual Direction
Editing Direction
Asset Strategy
Prompts
Evidence Bindings
```

This preserves the production matrix required by the PRD.

---

# 25. Voiceover Script

Conceptual:

```text
VoiceoverScript
```

Fields:

```text
voiceover_script_id
blueprint_variant_id
version
full_text
estimated_duration
language
status
```

---

# 26. Subtitle Segment

Conceptual:

```text
SubtitleSegment
```

Fields:

```text
subtitle_segment_id
blueprint_variant_id
sequence
start_time
end_time
text
emphasis
```

Voiceover and subtitle are different production layers.

Subtitle does not need to repeat the voiceover word-for-word.

---

# 27. Shot

Conceptual:

```text
Shot
```

Fields:

```text
shot_id
blueprint_variant_id
sequence
start_time
duration
voiceover_ref
subtitle_refs
visual_direction
asset_strategy
prompt_ref
evidence_refs
editing_direction
```

---

# 28. Shot/Slide Asset Strategy

Allowed strategies may include:

```text
ai_image
ai_video
uploaded_image
uploaded_video
screenshot
graphic
screen_recording
text_only
```

The strategy tells Asset Preparation what should be produced.

It does not force AI generation.

---

# 29. Asset Requirement

Conceptual:

```text
AssetRequirement
```

Fields:

```text
asset_requirement_id
blueprint_variant_id
target_type
target_id
sequence
asset_type
required
strategy
prompt_id
continuity_group_id
source_reference
status
```

This is the checklist consumed by Asset Preparation.

---

# 30. Structured Prompt

Prompts are represented structurally:

```text
Subject
Action
Environment
Composition
Camera
Camera Movement
Lighting
Style
Mood
Palette
Aspect Ratio
Continuity Group
Negative Prompt
```

Then:

```text
Structured Prompt
→ Provider-specific final_prompt
```

via Provider Adapter.

Provider-specific fields do not contaminate the canonical Blueprint model.

---

# 31. Prompt Versioning

Prompt edits can create:

```text
Prompt v1
Prompt v2
```

A prompt may be:

```text
AI Generated
Member Edited
Auto-Fixed
Provider Optimized
```

The origin must be traceable.

---

# 32. Visual Signal Inheritance

Analyzer may provide:

```text
visual signals
```

Blueprint uses those signals to produce:

```text
visual direction
shot/slide concept
asset strategy
prompt
```

The production layer must preserve the relationship:

```text
Analyzer Visual Signal
→ Blueprint Visual Direction
→ Asset Requirement
```

---

# 33. Editor Mapping

Conceptual:

```text
EditorMapping
```

Fields:

```text
mapping_id
blueprint_variant_id
target_type
target_id
editor_template_id
position
duration
layer_or_track
source_reference
asset_requirement_id
```

For image:

```text
Headline
→ Headline Zone

Body
→ Body Zone

Image
→ Image Zone
```

For video:

```text
Shot
→ Timeline Range
→ Track
→ Asset
```

The final Editor receives a prepared structure rather than starting from an empty canvas.

---

# 34. Caption

Blueprint Variant may contain:

```text
Caption
```

as an editable output.

It remains separate from:

```text
script
```

and:

```text
voiceover
```

because these are different production outputs.

---

# 35. Hashtag Suggestions

Hashtag suggestions may be included as metadata.

They remain:

```text
suggestion
```

not mandatory final output.

Research remains the source of the broader topic/intent/angle context.

---

# 36. Production QA

Before approval:

```text
Production QA
```

must evaluate:

```text
Content QA
Evidence QA
Hook QA
Copy QA
Visual QA
Production QA
```

The PRD establishes these QA dimensions before Asset Preparation. fileciteturn18file5L215-L219

---

# 37. QA Result

Conceptual:

```text
ProductionQAResult
```

Fields:

```text
qa_id
blueprint_variant_id
content_qa
evidence_qa
hook_qa
copy_qa
visual_qa
production_qa
overall_status
warnings[]
blockers[]
run_at
```

Status:

```text
PASS
PASS_WITH_WARNING
BLOCKED
```

---

# 38. Editability Matrix

Edits are classified by downstream risk.

## Safe Edit

```text
style
wording
layout
visual palette
```

## Revalidate

```text
hook
headline
claim wording
statistics
CTA
caption
```

## Dependency-Rebuild

```text
angle
core thesis
selected source
content type
objective-critical changes
```

When a high-impact field changes:

```text
Affected downstream components
→ revalidate or regenerate
```

---

# 39. Upstream Change Detection

If Analyzer changes:

```text
angle
evidence
source version
claim risk
readiness
opportunity
```

the Blueprint Variant must become:

```text
UPSTREAM_CHANGED
```

It must not appear current without revalidation.

---

# 40. Variant Regeneration

Supported operations:

```text
Regenerate Variant
Regenerate Script
Regenerate Visual Blueprint
Regenerate Shot
Regenerate Slide
Regenerate Prompt
```

Manual locked sections should be preserved when requested.

---

# 41. Multi-Variant Regeneration

If a Blueprint has:

```text
Angle A × Image
Angle A × Video
```

regenerating the Video Variant must not automatically change the Image Variant.

Variants are independent production realizations.

Shared upstream evidence may still be referenced.

---

# 42. Manual Override Priority

Production authority:

```text
Explicit Member Edit
→ Approved Production Decision
→ Analyzer Validated Input
→ AI Recommendation
→ Default Template
```

AI must never silently overwrite an explicit member decision.

---

# 43. Blueprint Approval

Approval is variant-aware.

Example:

```text
Carousel Variant
→ Approved

Video Variant
→ Needs Revision
```

The system must support independent approval state per variant.

A parent Blueprint can summarize:

```text
1 of 2 variants approved
```

---

# 44. Asset Preparation Handoff

Each approved Blueprint Variant sends:

```text
content_slot_id
blueprint_id
blueprint_variant_id
angle_id
content_type
asset_requirements
prompts
asset_strategy
continuity_groups
source_references
evidence_bindings
```

Asset Preparation must not need to reconstruct production intent.

---

# 45. Editor Handoff

Approved Variant sends:

```text
content_slot_id
blueprint_variant_id
editor_mapping
asset_requirements
script
subtitle
timeline/canvas structure
```

Editor becomes the implementation/rendering layer.

---

# 46. Visual Continuity Engine — Add-on

The final PRD defines **Visual Continuity Engine** as a paid per-project add-on for repeated:

```text
character
product
environment
```

across carousel/video outputs. fileciteturn28file0L10-L12

This is an official Module 3 capability, not a future undefined feature.

---

# 47. Continuity Group

Conceptual entity:

```text
ContinuityGroup
```

Fields:

```text
continuity_group_id
blueprint_variant_id
entity_type
name
description
status
created_at
updated_at
```

Entity types:

```text
character
product
environment
visual_style
```

---

# 48. Continuity Attribute Lock

Conceptual:

```text
ContinuityAttribute
```

Examples:

```text
age
wardrobe
hair_style
facial_features
product_design
environment
visual_style
palette
```

Fields:

```text
continuity_attribute_id
continuity_group_id
attribute_name
locked_value
lock_status
source
version
```

---

# 49. Continuity Inheritance

When a new prompt belongs to a Continuity Group:

```text
Continuity Group
       ↓
Locked Attributes
       ↓
Structured Prompt
       ↓
New Prompt
```

The new prompt inherits locked attributes unless explicitly changed through authorized override.

---

# 50. Continuity Indicator

UI may display:

```text
Konsistensi Visual
├── Karakter Terkunci
├── Environment Terkunci
└── Product Terkunci
```

This is a member-facing status indicator for the add-on.

---

# 51. Continuity Entitlement Boundary

Visual Continuity Engine is a paid capability.

It uses:

```text
Product
→ Entitlement
→ Capability:
visual_continuity
```

Actual consumption occurs when the capability performs its billable operation according to the entitlement policy.

Module 3 must not directly manipulate balances/wallets.

---

# 52. Continuity Failure

If continuity generation cannot maintain a locked attribute:

```text
CONTINUITY_CONFLICT
```

The system must:

- identify the conflicting attribute;
- preserve locked values where required;
- ask for explicit override if allowed;
- never silently remove the lock.

---

# 53. Advanced Prompt Studio & Auto-Fix — Add-on

The final PRD defines **Advanced Prompt Studio & Auto-Fix** as the second official Module 3 add-on. fileciteturn28file1L47-L50

It provides:

```text
Advanced Prompt Editor
Provider-specific optimization
One-Click Fix
```

while default structured prompt functionality remains available without the add-on. fileciteturn28file0L16-L23

---

# 54. Advanced Prompt Editor

Default:

```text
Structured Prompt
```

Add-on:

```text
Raw / Final Prompt Editor
```

Member can inspect/edit:

```text
final_prompt
```

for individual elements.

---

# 55. Provider-Specific Prompt Optimization

Advanced Prompt Studio may optimize prompts for:

```text
Provider A
Provider B
Provider C
```

But the canonical Blueprint retains the provider-neutral structured prompt.

Architecture:

```text
Canonical Structured Prompt
          ↓
Prompt Optimizer
          ↓
Provider-specific Prompt
          ↓
Provider Adapter
```

Provider-specific optimization must not overwrite the canonical semantic intent.

---

# 56. One-Click Auto-Fix

When Production QA identifies fixable issues:

```text
QA
→ Issue
→ One-Click Fix
```

The system may automatically:

```text
shorten copy
adjust layout
update prompt
update asset requirement
```

The PRD explicitly gives text-density correction as an example. fileciteturn28file1L47-L50

---

# 57. Auto-Fix Safety Rules

Auto-Fix must preserve:

```text
selected angle
evidence
claim meaning
audience
objective
content type
```

It may change:

```text
wording
layout
prompt wording
asset strategy detail
```

only within the approved dependency boundary.

---

# 58. Auto-Fix Scope

Auto-Fix should produce:

```text
Before
→ After
→ Reason
→ QA result
```

Example:

```text
Issue:
Text density high

Before:
30 words

After:
22 words

Reason:
Reduce text density while preserving claim

QA:
PASS
```

---

# 59. Auto-Fix Versioning

Auto-Fix creates a new revision.

Example:

```text
Blueprint Variant v4
→ Auto-Fix
→ Blueprint Variant v5
```

Do not mutate approved version silently.

---

# 60. Auto-Fix Entitlement Boundary

Advanced Prompt Studio & Auto-Fix uses its own capability:

```text
advanced_prompt_studio
```

or equivalent configurable capability.

The precise capability code is Admin/Product configurable, but it must exist in Core before the add-on can be sold.

---

# 61. Add-on Availability

Default Module 3 capabilities:

```text
Analyzer → Script Input
Selected Angle Lock
Content Context
Script
Fact/Interpretation/Creative Copy
Evidence Binding
Hook Validation
Structured Prompt
Editor Mapping
Production QA
Editability Matrix
Granular Regeneration
Versioning / Undo
```

Official add-ons:

```text
Visual Continuity Engine
Advanced Prompt Studio & Auto-Fix
```

This matches the final PRD's default/add-on division. fileciteturn28file0L16-L23

---

# 62. Add-on Access Check

Before invoking an add-on capability:

```text
Authenticated User
        ↓
Authorization
        ↓
Entitlement Check
        ↓
Capability Available?
        ↓
Execute
```

If entitlement is unavailable:

```text
ADDON_ENTITLEMENT_REQUIRED
```

No wallet/PAYG fallback exists.

---

# 63. Persistence

Blueprint and Variants must persist server-side.

Must survive:

```text
Refresh
Navigation
Logout
Later Login
```

provided retention and authorization conditions remain satisfied.

---

# 64. Autosave

Autosave should include:

```text
Script
Hook
Slide
Shot
Prompt
Asset Strategy
Editor Mapping
Continuity Attributes
```

User-visible save status:

```text
Saving...
Saved
Save Failed
```

---

# 65. Versioning and Undo

Blueprint Variant supports:

```text
Version
Undo
Restore
```

where implemented.

A restore creates a new current revision rather than deleting history.

---

# 66. Events

Production domain events:

```text
BlueprintCreated
BlueprintUpdated
BlueprintVariantCreated
BlueprintVariantGenerated
BlueprintVariantApproved
BlueprintVariantSuperseded

ScriptUpdated
HookUpdated
SlideUpdated
ShotUpdated
PromptUpdated
AssetRequirementUpdated
EditorMappingUpdated

ProductionQACompleted
BlueprintNeedsRevision

ContinuityGroupCreated
ContinuityAttributeLocked
ContinuityAttributeChanged

AdvancedPromptOptimizationStarted
AutoFixApplied
AutoFixRejected
```

All integrate with Core Contract #8.

---

# 67. Audit

Audit:

```text
Angle Selected
Variant Created
Variant Approved
Manual Script Edit
Evidence-sensitive Edit
Prompt Edited
Continuity Lock Changed
Auto-Fix Applied
Variant Regenerated
Blueprint Approved
Blueprint Sent to Asset Preparation
```

For evidence-sensitive operations:

```text
before
after
actor
reason
```

should be retained where applicable.

---

# 68. API Contract

Conceptual:

```text
GET  /content-slots/:id/blueprint
POST /content-slots/:id/blueprint

GET  /blueprints/:id/variants
POST /blueprints/:id/variants

PATCH /blueprint-variants/:id
POST  /blueprint-variants/:id/generate
POST  /blueprint-variants/:id/regenerate
POST  /blueprint-variants/:id/qa
POST  /blueprint-variants/:id/approve

GET  /blueprint-variants/:id/versions
POST /blueprint-variants/:id/undo
POST /blueprint-variants/:id/restore

POST /blueprint-variants/:id/continuity/groups
PATCH /continuity/groups/:id

POST /blueprint-variants/:id/prompt-studio/optimize
POST /blueprint-variants/:id/auto-fix
```

Exact REST paths remain subject to Core Architecture/API Architecture.

---

# 69. Blueprint Service Boundary

Conceptual operations:

```text
BlueprintService.initialize()
BlueprintService.generateVariant()
BlueprintService.generateNarrative()
BlueprintService.generateScript()
BlueprintService.refineHook()
BlueprintService.generateVisualBlueprint()
BlueprintService.generateStructuredPrompts()
BlueprintService.generateAssetRequirements()
BlueprintService.generateEditorMapping()
BlueprintService.runQA()
BlueprintService.approveVariant()
BlueprintService.regenerateVariant()
```

---

# 70. Continuity Service Boundary

Conceptual:

```text
ContinuityService.createGroup()
ContinuityService.lockAttribute()
ContinuityService.updateAttribute()
ContinuityService.resolveInheritance()
ContinuityService.validateConsistency()
```

It operates on Blueprint Variant prompt/asset requirements but does not own provider invocation.

---

# 71. Prompt Studio Boundary

Conceptual:

```text
PromptStudioService.inspect()
PromptStudioService.edit()
PromptStudioService.optimizeForProvider()
PromptStudioService.autoFix()
PromptStudioService.validate()
```

Provider-specific conversion remains under Provider Adapter.

---

# 72. Entitlement / Billing Boundary

Module 3 does not implement:

```text
Order
Payment
Wallet
Balance
```

It only asks Entitlement:

```text
Can this capability be used?
```

and records usage/consumption through the Entitlement contract where applicable.

---

# 73. Content Slot Relationship

One Content Slot can have:

```text
multiple angles
×
multiple content types
```

through separate Blueprint Variants, but the current production execution should explicitly identify the selected active variant(s).

Example:

```text
Content Slot CS-100
├── Angle A × Carousel
├── Angle A × Video
└── Angle B × Video
```

This supports the PRD's multi-angle / multi-format behavior without mixing production states.

---

# 74. Variant Selection

A member may select:

```text
Angle A × Carousel
```

and later also approve:

```text
Angle A × Video
```

Each becomes its own downstream production path.

The system must not assume:

```text
one Content Slot = one script
```

when multiple angle × content-type variants are explicitly supported by the product.

---

# 75. Production Status vs Variant Status

Keep separate:

```text
Content Slot Status
```

from:

```text
Blueprint Variant Status
```

and:

```text
Asset Status
```

Example:

```text
Content Slot
→ Asset Ready

Variant A × Video
→ Approved

Variant A × Carousel
→ Needs Revision
```

This allows multiple production variants without corrupting the master content lifecycle.

---

# 76. Core Invariants

```text
1. Module 3 is a production layer, not a second Analyzer.

2. Analyzer remains the source of truth for source/evidence/opportunity/angle validation.

3. Blueprint and Blueprint Variant are separate concepts.

4. Blueprint Variant is uniquely identified by Content Slot + Angle + Content Type
   for an active production version.

5. Each Angle × Content Type production path has its own script,
   slides/shots, asset requirements, prompts, editor mapping, and QA.

6. Selected angle cannot be silently replaced.

7. Fact, Interpretation, and Creative Copy remain distinct.

8. Evidence bindings survive into production.

9. Evidence-sensitive edits trigger revalidation.

10. Variants can be independently regenerated.

11. Regenerating one variant does not silently modify another variant.

12. Structured prompts remain provider-neutral.

13. Provider-specific prompt optimization does not replace the canonical
    semantic prompt.

14. Visual Continuity is an add-on capability with its own entitlement boundary.

15. Advanced Prompt Studio & Auto-Fix is an add-on capability with its own
    entitlement boundary.

16. Auto-Fix cannot change selected angle, evidence meaning, audience,
    objective, or other protected upstream intent silently.

17. Actual image/video generation occurs outside Module 3.

18. Module 3 itself does not consume generation entitlement merely by
    preparing prompts/assets.

19. Production QA must run before variant approval.

20. Approved variants are immutable historical versions; new changes create
    new revisions.

21. Content Slot remains the cross-module production anchor.

22. Workspace/Role/Tenant authorization remains enforced.

23. Blueprint/Variant state persists across refresh/logout/login.

24. Historical versions and audit records are not silently rewritten.

25. A Content Slot may contain multiple Angle × Content Type variants,
    provided the product workflow explicitly supports them.
```

---

# 77. Definition of Done

Core Contract #13 Revised is complete when:

### Blueprint Core

1. Analyzer → Blueprint Input Contract exists.
2. Content Production Blueprint exists.
3. Blueprint Variant exists.
4. Angle × Content Type is explicit.
5. Variant uniqueness is defined.
6. Selected angle lock exists.
7. Content context is inherited.
8. Script exists.
9. Script versioning exists.
10. Fact/Interpretation/Creative Copy separation exists.
11. Evidence Binding exists.
12. Hook pipeline and validation exist.

### Image / Carousel

13. Slide model exists.
14. Carousel copy QA exists.
15. Visual direction exists.
16. Asset strategy exists.
17. Evidence binding per relevant claim exists.

### Video

18. Voiceover Script exists.
19. Subtitle Segment exists.
20. Shot model exists.
21. Timeline mapping exists.
22. Video asset strategy exists.

### Prompts / Assets / Editor

23. Structured Prompt exists.
24. Prompt versioning exists.
25. Asset Requirement exists.
26. Editor Mapping exists.
27. Asset Preparation handoff exists.
28. Editor handoff exists.

### QA / Editing

29. Production QA exists.
30. Editability Matrix exists.
31. Upstream Change Detection exists.
32. Granular regeneration exists.
33. Multi-Variant regeneration independence exists.
34. Member override priority exists.
35. Versioning/Undo exists.

### Add-ons

36. Visual Continuity Engine exists as a formal capability boundary.
37. Continuity Group exists.
38. Locked continuity attributes exist.
39. Continuity inheritance is defined.
40. Advanced Prompt Studio exists as a formal capability boundary.
41. Provider-specific prompt optimization exists.
42. Auto-Fix exists.
43. Auto-Fix versioning exists.
44. Auto-Fix safety rules exist.
45. Add-on entitlement checks exist.

### Governance

46. Persistence/autosave exists.
47. Event integration exists.
48. Audit integration exists.
49. Authorization boundary exists.
50. Entitlement boundary remains outside Blueprint business logic.

---

# 78. Implementation Priority

Based on the final PRD:

## P0 — Default Module 3

```text
Analyzer → Script Input Contract
Selected Angle Lock
Content Context
Script — Image/Carousel/Video
Fact / Interpretation / Creative Copy
Claim Risk Inheritance
Evidence Binding
Visual Signal Inheritance
Hook Pipeline + Revalidation
Structured Prompt
Editor Mapping
Production QA
Editability Matrix
Granular Regeneration
Versioning / Undo
Output Contract Blueprint
```

The PRD explicitly marks these as default, required initial-release capabilities. fileciteturn28file0L16-L23

## P1 — Official Add-ons

```text
Visual Continuity Engine
Advanced Prompt Studio
Provider-specific Prompt Optimization
One-Click Auto-Fix
```

These are explicitly defined as paid Module 3 add-ons. fileciteturn28file0L10-L23

## P2 / Later Roadmap

```text
Analytics-driven Re-edit
Visual Performance Learning
Template A/B Testing
Visual Fatigue Detection
Automatic Prompt Optimization
```

These are explicitly future/roadmap capabilities, not initial Module 3 requirements. fileciteturn28file0L16-L23

---

# 79. Dependencies

Depends on:

```text
Core Contract #1
Identity / User / Session

Core Contract #2
Role / Permission

Core Contract #3
Configuration

Core Contract #4
Product / Entitlement

Core Contract #6
Provider Pool

Core Contract #8
Audit / Events / Notifications

Core Contract #9
Workspace / Content Slot / Project Context

Core Contract #10 Revised
Research Intelligence

Core Contract #11
Planner

Core Contract #12
Analyzer
```

Produces:

```text
ContentProductionBlueprint
BlueprintVariant
Script
VisualBlueprint
Shot / Slide
VoiceoverScript
SubtitleSegment
StructuredPrompt
AssetRequirement
EditorMapping
ProductionQA
```

Consumed by:

```text
Asset Preparation
Editor
Export
Analytics
```

---

# 80. Architecture Boundary Notes

This is a **domain contract**, not a forced microservice map.

Potential domain boundaries:

```text
Blueprint Domain
Variant/Production Structure
Prompt/Continuity Capability
Production QA
```

However, Core Architecture must decide whether these become:

```text
one application module
multiple modules
separate services
workers
shared capabilities
```

The following must remain outside Module 3:

```text
Provider Routing
Actual Image/Video Generation
Payment
Order
Entitlement Ledger
Editor Rendering
Export Rendering
Analytics Ingestion
```

---

# 81. Replacement Note

This document supersedes the previous Core Contract #13.

The major corrections are:

```text
1. Blueprint Variant is now explicit.

2. Angle × Content Type is now a first-class production relationship.

3. One Content Slot may have multiple production variants.

4. Each variant owns its own script/slide/shot/prompt/asset/editor/QA state.

5. Visual Continuity Engine is now an official Module 3 add-on boundary.

6. Advanced Prompt Studio & Auto-Fix is now an official Module 3 add-on boundary.

7. Add-on entitlement checks are explicit.

8. Provider-neutral structured prompts remain canonical even when
   Advanced Prompt Studio performs provider-specific optimization.

9. Auto-Fix safety and versioning are explicit.

10. Variant-specific approval/regeneration is independent.
```

---

# 82. Next Step

After this revision, Core Contract #10 and #13 are aligned substantially closer to the final PRD.

The next recommended work item is:

> **Core Architecture V1 — Boundary Map**

Before building Core Contract #14, define the actual architecture boundaries for Core Contracts #1–#13:

```text
Domain
Service / Module
Data Ownership
API
Events
Workers
Provider boundary
Storage boundary
Security boundary
Transaction boundary
```

This is the point where the contracts should stop expanding and begin turning into an implementable system design.
