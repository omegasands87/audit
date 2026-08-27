# Core Contract #11 — Planner, Content Idea & Calendar

## Status

**Draft for Core Design — based on the finalized PRD, Final Business Decision Register, and Core Contracts #1–#10**

This is the first major domain contract for the **Content Planning Engine**.

Planner is not merely a calendar UI.

Its responsibility is to transform:

```text
Research Opportunities
+
Analytics Recommendations
+
Idea Pool
+
Strategy Configuration
+
Planning Constraints
        ↓
Planning Engine
        ↓
Content Plan
        ↓
Content Slots
        ↓
Calendar
```

The Planner must remain:

> **Strategy-first, constraint-based, evidence-based, production-aware, explainable, persistent, reversible, incremental, and fully under member control.**

The PRD explicitly describes Planner as a constraint-based planning engine rather than a calendar-only UI. fileciteturn16file3L121-L139

---

# 1. Scope

This contract covers:

- Content Plan;
- Planning Configuration;
- Strategy Configuration;
- Idea Pool;
- Content Idea;
- Research Opportunity integration;
- Analytics Recommendation integration;
- Content Type Allocation;
- Content Pillar Allocation;
- integer allocation;
- active days;
- posting frequency;
- timezone;
- schedule preferences;
- candidate slots;
- scheduling;
- Smart Time Recommendation;
- hard constraints;
- soft constraints;
- diversity / anti-repetition;
- production workload;
- calendar scoring;
- Calendar Health;
- conflict detection;
- manual override;
- locked slots;
- regenerate selected;
- regenerate from date;
- rebalance;
- bulk actions;
- duplicate;
- change preview;
- undo;
- version history;
- autosave;
- persistence;
- Planner → Analyzer handoff.

It does not define:

- Research provider implementation;
- Research data normalization;
- Analyzer internals;
- Script generation;
- Asset generation;
- Editor implementation;
- Analytics ingestion;
- payment/billing.

---

# 2. Source Principles

The PRD defines Planner as the center of strategy before production and explicitly states:

- no social-platform selection inside Planner;
- planning is based on content type;
- member remains the decision-maker;
- hard constraints cannot be violated;
- soft constraints are optimized;
- AI recommendations require explanation;
- calendar state persists;
- calendar changes are reversible;
- planning is incremental rather than all-or-nothing. fileciteturn16file3L121-L139

The PRD also defines the Planning Engine pipeline:

```text
Planning Configuration
→ Strategy Configuration
→ Research Opportunities
→ Analytics Insights
→ Idea Pool
→ Candidate Slot Generation
→ Hard Constraints
→ Pillar Allocation
→ Content Type Allocation
→ Time Scheduling
→ Diversity Optimization
→ Production Workload Check
→ Calendar Scoring
→ Calendar Health Check
→ Generated Calendar
→ Member Review
→ Lock/Edit/Approve
```

fileciteturn16file0L27-L33

---

# 3. Core Principle — Planning vs Production

Planner decides:

```text
What
When
How often
Which pillar
Which content type
Priority
Planning context
```

Planner does not produce:

```text
Final Script
Final Assets
Final Edit
```

Those belong to Content Engine stages after Planner.

The Planner creates the `ContentSlot` context that downstream modules use.

---

# 4. Content Plan

Conceptual entity:

```text
ContentPlan
```

Minimum fields:

| Field | Purpose |
|---|---|
| `content_plan_id` | stable identity |
| `workspace_id` | workspace |
| `name` | plan name |
| `start_date` | planning period start |
| `end_date` | planning period end |
| `timezone` | scheduling timezone |
| `active_days` | enabled days |
| `daily_frequency` | frequency |
| `status` | plan state |
| `strategy_version` | strategy snapshot/version |
| `created_by` | creator |
| `created_at` | creation |
| `updated_at` | update |

---

# 5. Planning Period

Member provides:

```text
Start Date
End Date
```

Duration is calculated automatically.

Duration is not an independent user-entered source of truth.

If a period changes after slots exist:

> **the system must ask for confirmation before changing/deleting affected planning data.**

It must not silently delete slots.

---

# 6. Timezone

Each Content Plan has an explicit:

```text
timezone
```

All planning calculations use that timezone.

The same timezone must govern:

- active days;
- scheduled time;
- daily frequency;
- date boundaries;
- conflict detection;
- Calendar display.

---

# 7. Active Days

Active days are explicitly selected:

```text
Monday
Tuesday
Wednesday
Thursday
Friday
Saturday
Sunday
```

Frequency is calculated from active days, not raw calendar days.

Example:

```text
7 calendar days
5 active days
2 posts/day

Planned Slots:
5 × 2 = 10
```

not:

```text
7 × 2 = 14
```

This is a core planning rule. fileciteturn16file1L44-L47

---

# 8. Posting Frequency

Current supported frequency:

```text
1x / day
2x / day
3x / day
```

The architecture should support future:

```text
Custom Frequency
```

without redesigning the planning model.

Total planned slots:

```text
Active Days × Daily Frequency
```

before adjustments for:

- locked/existing slots;
- excluded dates;
- campaign constraints;
- other valid hard constraints.

The system must display:

> **Planned Slots: N**

before generation.

---

# 9. Content Type

Planner is platform-agnostic.

It plans by content type, such as:

```text
Single Post
Carousel
Video Pendek
```

The exact configured content type catalog remains Admin/Configuration driven.

Planner must not ask:

```text
Instagram?
TikTok?
YouTube?
```

as a platform selection.

This is explicitly excluded from Planner. fileciteturn16file3L121-L139

---

# 10. Strategy Configuration

Strategy configuration contains:

```text
Content Type Allocation
Content Pillar Allocation
Brand Profile
Audience
Diversity Preference
```

The configuration becomes planning input, not merely UI decoration.

---

# 11. Content Type Allocation

When multiple content types are active, member can choose:

### Recommended Mix

AI recommends distribution based on:

- Research Insight;
- Own Content Intelligence;
- content objective;
- audience;
- performance history.

### Manual Mix

Member chooses percentages.

Total must:

```text
= 100%
```

---

# 12. Content Pillar Allocation

Content Pillar allocation is an **actual planning target**, not a visual-only slider.

Example:

```text
Education 40%
Story     30%
Promotion 30%
```

The engine attempts to satisfy the target while respecting:

- hard constraints;
- diversity;
- production workload;
- opportunity quality.

The PRD explicitly states that pillar allocation is a target, not a reason to create a poor editorial calendar. fileciteturn16file0L11-L17

---

# 13. Integer Allocation

Allocation must produce whole slot counts.

Recommended approach:

> **Largest Remainder method or equivalent.**

Example:

```text
7 slots

40% / 30% / 30%

→ 3 / 2 / 2
```

Never:

```text
2.8 / 2.1 / 2.1
```

The final integer allocations must total exactly:

```text
planned_slots
```

fileciteturn16file0L11-L13

---

# 14. Brand Profile

Brand Profile may include:

```text
Tone of Voice
Audience description
Brand characteristics
```

Examples:

```text
Casual
Professional
Gen-Z
B2B
```

Brand Profile informs:

- idea fit;
- opportunity selection;
- recommendation;
- downstream content context.

It does not become a hard scheduling constraint unless explicitly configured.

---

# 15. Diversity Preference

Member may configure desired variation across:

```text
Pillar
Format
Topic
```

This is input to the Anti-Repetition/Diversity Engine.

---

# 16. Idea Pool

Idea Pool is intentionally separate from the Calendar.

It represents:

> **ideas available for planning, not yet scheduled.**

Possible Idea Pool sources:

```text
Research Opportunity
Trend
Keyword Opportunity
Audience Question
Saved Idea
Manual Idea
Analytics Recommendation
```

This separation is explicitly defined in the PRD. fileciteturn16file0L19-L25

---

# 17. Content Idea Entity

Conceptual:

```text
ContentIdea
```

Minimum:

| Field | Purpose |
|---|---|
| `content_idea_id` | stable identity |
| `workspace_id` | workspace |
| `content_plan_id` | optional associated plan |
| `title` | idea title |
| `description` | idea description |
| `pillar` | suggested pillar |
| `suggested_content_type` | suggested type |
| `priority` | High/Medium/Low |
| `source_type` | research/trend/manual/etc. |
| `source_id` | source reference |
| `opportunity_score` | if available |
| `confidence` | if available |
| `suggested_timing` | if available |
| `status` | lifecycle |
| `created_at` | creation |
| `updated_at` | update |

---

# 18. Idea Status

Minimum:

```text
Available
Planned
Used
Archived
```

Meaning:

### Available

Ready to be scheduled.

### Planned

Already associated with a planned slot.

### Used

Already converted/consumed by a production workflow.

### Archived

Removed from active planning.

---

# 19. Research → Planner

Research Opportunity can enter Idea Pool with:

```text
topic
recommended pillar
recommended format
priority
suggested timing
opportunity score
confidence
recommended angle
evidence reference
```

Member may choose:

```text
Send to Planner
```

or:

```text
Send to Planner & Schedule
```

The latter can create/schedule a Content Slot directly. fileciteturn16file2L74-L82

---

# 20. Analytics → Planner

Analytics may return recommendations based on actual member performance.

Example:

> "Video naik dari 40% menjadi 55%."

Planner must show:

```text
Recommendation
Reason
Affected Strategy
Expected Benefit
```

Member chooses:

```text
Apply Recommendation
Review Changes
Keep Current Strategy
```

Strategy is **never changed silently**. fileciteturn16file2L74-L76

---

# 21. Manual Idea

Member may create:

```text
Manual Idea
```

without Research or Analytics.

Manual ideas must have the same basic lifecycle as other ideas.

The Planner does not require every slot to originate from AI.

---

# 22. Opportunity-to-Idea Conversion

A Research Opportunity does not have to become a permanent duplicate data object unless useful.

Recommended:

```text
Research Opportunity
→ Content Idea reference
```

or direct:

```text
Research Opportunity
→ Content Slot
```

The original research provenance must remain available.

---

# 23. Generate Content Plan

Primary planning action:

```text
Generate Content Plan
```

Before execution, show:

```text
Period
Active Days
Frequency
Total Slots
Pillar Distribution
Content Type Distribution
Ideas Available
Research Opportunities Available
```

Member must know what will be generated before executing.

---

# 24. Planning Pipeline

Canonical pipeline:

```text
1. Planning Configuration
2. Strategy Configuration
3. Research Opportunities
4. Analytics Insights
5. Idea Pool
6. Candidate Slot Generation
7. Hard Constraints
8. Pillar Allocation
9. Content Type Allocation
10. Time Scheduling
11. Diversity Optimization
12. Production Workload Check
13. Calendar Scoring
14. Calendar Health Check
15. Generated Calendar
16. Member Review
17. Lock / Edit / Approve
```

This is the canonical planning pipeline from the PRD. fileciteturn16file0L27-L33

---

# 25. Candidate Slot Generation

The engine creates candidate slots using:

```text
active days
frequency
planning period
existing slots
excluded dates
campaign rules
```

Candidates are not final until constraints are validated.

---

# 26. Hard Constraints

Hard constraints must never be violated.

Minimum:

```text
Slot inside planning period
Slot on active day
Daily frequency not exceeded
Content type allowed
Content pillar allowed
No duplicate timestamp
Locked slot cannot be moved by auto-planner
```

This is explicitly defined in the PRD. fileciteturn16file2L84-L89

---

# 27. Soft Constraints

Soft constraints may be traded off.

Examples:

```text
Pillar diversity
Format diversity
Topic diversity
Time optimization
Research relevance
Analytics fit
Production workload
Promotional spacing
Trend freshness
```

The engine should optimize them rather than treat them as impossible constraints.

---

# 28. Hard vs Soft Conflict

If a hard constraint conflicts with a soft preference:

```text
Hard Constraint Wins
```

Example:

```text
Preferred time
vs
daily frequency exceeded
```

Frequency constraint wins.

---

# 29. Content Slot

Every scheduled calendar entry becomes a:

```text
ContentSlot
```

with:

```text
content_slot_id
```

as the stable cross-module identity.

Minimum Planner-level fields from the PRD:

```text
content_slot_id
content_plan_id
date
scheduled_time
content_type
content_pillar
topic
title
priority
status
source_id
angle_id
script_id
asset_project_id
editor_project_id
is_auto_generated
is_locked
```

fileciteturn17file0L12-L14

---

# 30. Content Slot as Downstream Anchor

The Content Slot connects:

```text
Planner
→ Analyzer
→ Script
→ Assets
→ Editor
→ Export
→ Analytics
```

Planner must not create separate production project identities unnecessarily.

This matches Core Contract #9.

---

# 31. Auto-Generated Slot

`is_auto_generated` indicates whether the slot originated from the planning engine.

Possible values:

```text
true
false
```

A manually edited auto-generated slot remains:

```text
is_auto_generated = true
```

but receives manual change history.

Origin and current decision are different concepts.

---

# 32. Manual Override

Manual decisions have higher authority than AI planning.

Final authority order:

```text
Locked Manual Decision
        ↓
Manual Unlocked Decision
        ↓
Hard Constraint
        ↓
Approved Strategy
        ↓
AI Recommendation
        ↓
Default Heuristic
```

AI must never override an explicit member decision. fileciteturn17file0L49-L57

---

# 33. Lock

A member can lock a slot.

```text
is_locked = true
```

Locked slot:

> **must not be moved or replaced by automatic planning operations.**

During regenerate/rebalance:

```text
Locked Slot
→ preserved
```

---

# 34. Unlock

Only an authorized member/Admin may unlock according to permission.

Unlock returns the slot to normal planning behavior.

The lock/unlock operation must be audited.

---

# 35. Smart Time Recommendation

Three modes:

### AI Recommended

If Analytics data is available:

```text
best-performing windows
```

adjusted where data is sufficient by:

- content type;
- pillar.

If there is insufficient member data:

> Use general benchmarks and explicitly label them as recommendations, not facts.

### Fixed Schedule

Member chooses fixed time:

```text
09:00
13:00
19:00
```

### Custom

Member determines different time per day.

These modes are defined in the PRD. fileciteturn17file0L16-L23

---

# 36. Time Slot Intelligence

When 2–3 slots/day exist, ordering can consider:

```text
Content Type
Pillar
Audience Behavior
Performance History
Production Readiness
```

The engine must not simply order by database creation sequence.

---

# 37. Anti-Repetition Engine

The planner should penalize repetitive patterns.

Examples:

```text
Education
Education
Education
```

or:

```text
Video
Video
Video
Video
```

unless the strategy deliberately requires it.

Other patterns:

```text
same topic too close
promotional clustering
same hook/angle repeatedly
```

The engine uses a repetition penalty internally.

Member does not need to see the mathematical formula.

Instead:

> "Calendar adjusted to improve content variety."

fileciteturn17file0L25-L29

---

# 38. Topic Spacing

Same/similar topics should not be scheduled too close unless:

```text
campaign
series
strategy
```

explicitly requires it.

The spacing logic is a soft constraint unless a hard campaign rule exists.

---

# 39. Promotional Spacing

Promotional posts should not cluster unnecessarily.

Example warning:

```text
3 promotional posts in a row
```

Planner may recommend spacing them.

---

# 40. Trend-Aware Scheduling

If Research provides:

```text
trend lifecycle
velocity
expected shelf life
```

Planner may prioritize the opportunity within its relevance window.

Example:

```text
Trend shelf life = 72 hours

Planner:
→ prioritize near-term scheduling
```

It should not place an expiring trend weeks later merely because a slot exists. fileciteturn17file0L85-L89

---

# 41. Priority

Content priority:

```text
High
Medium
Low
```

If available slots are limited:

> Higher-priority opportunities are scheduled first.

Priority does not automatically mean:

```text
locked = true
```

---

# 42. Production Workload

Planner considers production difficulty.

Levels:

```text
Low
Medium
High
Critical
```

Workload may consider:

```text
Content Type
Production Status
Unfinished Assets
Video Count
Production Complexity
```

Example:

> "8 video contents are scheduled in 3 days and most have not started production."

Planner may recommend distributing production-heavy content across more days.

fileciteturn17file0L37-L39

---

# 43. Calendar Scoring

Every generated calendar can receive internal scores:

```text
Pillar Balance
Format Diversity
Topic Diversity
Time Optimization
Research Fit
Analytics Fit
Production Load
Overall Score
```

These are:

> **decision-support scores, not predictions of viral performance.**

---

# 44. Calendar Health Score

User-facing summary:

```text
Calendar Health
88 / 100
```

with concrete warnings.

Example:

```text
⚠ 3 educational videos are scheduled consecutively
```

Potential action:

```text
Fix Automatically
```

The fix must respect locked/manual decisions.

---

# 45. Calendar Health Explanation

Health score should not be a black box.

Show:

```text
Pillar Balance
Format Diversity
Topic Diversity
Time Optimization
Research Fit
Analytics Fit
Production Load
```

Where relevant, show reason/warning.

---

# 46. Calendar View

Three views:

### Monthly

Focus:

```text
density
pillar distribution
campaign
workload
status
```

### Weekly

Focus:

```text
execution
time
daily ordering
drag & drop
```

### Grid

Focus:

```text
bulk management
comparison
filtering
sorting
```

This matches the PRD structure. fileciteturn17file0L41-L47

---

# 47. Calendar Card

Minimum display:

```text
Date
Time
Content Type
Title / Topic
Pillar
Priority
Content Status
Production Indicator
Lock Indicator
```

---

# 48. Card Actions

Possible actions:

```text
Open Content
Add Source
Change Date
Change Time
Change Pillar
Change Content Type
Duplicate
Lock / Unlock
Regenerate
Move
Delete
```

Availability depends on current status and permission.

---

# 49. Drag & Drop

When a slot is moved, validate in order:

```text
Active Day
Daily Frequency
Timestamp Conflict
Locked Status
```

Then recalculate:

```text
Pillar Distribution
Diversity
Production Workload
```

If conflict exists:

```text
Move Anyway
Rebalance Calendar
Cancel
```

fileciteturn17file0L49-L55

"Move Anyway" cannot violate hard constraints.

---

# 50. Conflict Types

Minimum conflict classifications:

```text
Schedule Conflict
Frequency Conflict
Active Day Conflict
Locked Conflict
Pillar Conflict
Format Conflict
Production Conflict
Trend Freshness Conflict
```

The UI should explain the conflict rather than merely showing an error.

---

# 51. Locked Conflict

If an automatic action attempts to alter:

```text
is_locked = true
```

the planner must exclude that slot from automatic changes.

This is not a normal error requiring a different value; it is a planning boundary.

---

# 52. Regenerate Selected

Member can select specific slots:

```text
Slot A
Slot C
Slot F
```

and regenerate only those.

Unselected slots remain unchanged.

Locked selected slots cannot be automatically changed.

---

# 53. Regenerate From Date

Member chooses:

```text
Regenerate from:
1 September
```

Rules:

```text
Before 1 September
→ preserved

After 1 September
→ eligible for regeneration

Locked slots
→ preserved
```

The PRD explicitly requires that slots before the selected date remain intact. fileciteturn17file0L59-L63

---

# 54. Rebalance Calendar

If manual edits cause allocation drift:

```text
Target:
Education 40%

Current:
55%
```

Planner may offer:

```text
Rebalance Automatically
```

Only unlocked slots may be changed.

---

# 55. Partial Update

Small strategy changes must not automatically regenerate the entire calendar.

Example:

```text
Education:
40% → 50%
```

The system calculates:

```text
Unlocked slots potentially affected = N
```

and previews the changes.

Member chooses:

```text
Review Changes
Apply
Cancel
```

---

# 56. Change Preview

Mass changes must show explicit diff:

```text
Slot
Before
After
Reason
```

Example:

```text
Sep 4 — Video
Before: Education
After: Story

Reason:
Restore pillar balance
```

This keeps AI planning controllable.

---

# 57. Bulk Actions

Supported:

```text
Select Multiple
Select All Visible
Change Pillar
Change Content Type
Change Date
Change Time
Lock
Unlock
Regenerate
Duplicate
Delete
```

All affected slots must still pass hard constraints.

---

# 58. Duplicate Content

Duplicate action should create:

> **a new content plan / new planning content rather than a blind identical copy.**

Example:

```text
Original:
Carousel

Duplicate:
Video Pendek
```

Source/angle may be referenced.

But:

```text
Script
Production Flow
Editor Project
```

remain separate entities.

---

# 59. Version History

Calendar changes are versioned.

Examples:

```text
Plan v1
→ Calendar Generated

Plan v2
→ 3 slots moved

Plan v3
→ Pillar Allocation Changed

Plan v4
→ Rebalanced
```

Version should record:

```text
version_number
snapshot
created_by
created_at
change_summary
```

---

# 60. Undo

At minimum:

> **Undo Last Change**

Undo should restore the previous valid calendar state.

Do not treat undo as rewriting historical audit.

It creates a new state/change event.

---

# 61. Restore Version

Where supported:

```text
Version 3
→ Restore
```

This creates a new current version based on Version 3.

Old versions remain preserved.

---

# 62. Plan Approval

Planner may have a plan-level status:

```text
Draft Plan
Ready
```

This is separate from:

```text
Content Status
Draft
Source Needed
Script Ready
Asset Ready
Editing
Exported
```

Planner must display the official content lifecycle rather than replace it. fileciteturn17file0L81-L83

---

# 63. Planner Status

Recommended:

```text
DRAFT
GENERATED
UNDER_REVIEW
APPROVED
ARCHIVED
```

This is a planning-level state.

Content Slot has its own content lifecycle.

---

# 64. Search

Planner search:

```text
Title
Topic
Source
Pillar
Content Type
```

Search must respect workspace/user authorization.

---

# 65. Filters

Minimum:

```text
Date
Content Type
Pillar
Status
Priority
Locked / Unlocked
Production Load
Research Source
Opportunity Score
```

These are directly defined in the PRD. fileciteturn17file0L91-L99

---

# 66. Calendar Summary

Real-time summary:

```text
Total Days
Total Slots
Pillar Breakdown
Content Type Breakdown
Content Status Breakdown
```

Summary updates after manual or automated changes.

---

# 67. AI Planning Recommendations

Recommendation object should include:

```text
recommendation_id
reason
affected_slots
expected_benefit
action
priority
```

Examples:

```text
Increase Video 10%
based on account performance

Spread 2 promotional posts

Use emerging trend within 72 hours

Reduce production workload on Sep 3
```

Every recommendation includes a reason.

The system must never present an AI recommendation as fact.

---

# 68. Member Control

The Planner must preserve:

```text
Suggest
→ Review
→ Approve
```

rather than:

```text
AI
→ silently changes strategy
```

Explicit member decisions always have higher authority.

---

# 69. No Empty Calendar Filling

The engine must not invent topics simply to fill dates.

If insufficient valid ideas exist:

```text
Planned Slots:
10

Valid Opportunities:
7

Remaining:
3
```

The engine should warn the member rather than fabricate low-quality topics merely to reach 10.

This is an explicit Planner constraint in the PRD. fileciteturn16file8L353-L364

---

# 70. Strategy vs Calendar Quality

The engine must not blindly satisfy:

```text
Pillar Ratio
```

if that makes the editorial calendar materially worse.

Example:

```text
Education 40%
```

is a target.

If available ideas make the target harmful:

```text
Warning
```

rather than forced low-quality scheduling.

---

# 71. Planner-to-Analyzer Handoff

When a member opens Analyzer from a Content Slot:

```text
content_slot_id
```

is preserved.

Planner may pass:

```text
topic
pillar
content_type
priority
opportunity
angle
evidence
source
```

Analyzer becomes the next production/intelligence stage.

---

# 72. Opportunity Metadata Preservation

When Opportunity becomes a Content Slot, preserve:

```text
opportunity_id
source_id
angle_id
evidence_refs
confidence
opportunity_score
```

so downstream modules can trace where the planned content came from.

---

# 73. Analytics Feedback

Analytics recommendations can alter:

```text
Future Strategy
```

but should not silently mutate historical plans.

Example:

```text
Analytics
→ recommends video ratio 55%

Next Plan
→ can adopt 55%

Existing Plan
→ unchanged unless member applies recommendation
```

---

# 74. Persistence

Autosave should occur at minimum for:

```text
Planning Configuration
Slot movement
Slot editing
Locking
Pillar change
Content Type change
Schedule change
```

Calendar must survive:

```text
Refresh
Navigation
Logout
Later Login
```

This is consistent with the platform persistence requirement and Planner's explicit autosave rule. fileciteturn16file8L349-L371

---

# 75. Concurrency

Planner edits must use optimistic concurrency/version checks.

Example:

```text
Calendar Revision:
10

User saves with expected_revision=10

If current revision=11:
→ VERSION_CONFLICT
```

Never silently overwrite a newer manual decision.

---

# 76. Calendar Version Snapshot

A calendar version should contain enough information to restore the planning state.

Possible snapshot:

```text
content_slot assignments
strategy allocation
schedule
constraints
lock state
plan metadata
```

Large downstream domain payloads should remain referenced rather than embedded.

---

# 77. Planning Event Integration

Important events:

```text
ContentPlanCreated
ContentPlanGenerated
ContentPlanApproved

ContentSlotCreated
ContentSlotUpdated
ContentSlotMoved
ContentSlotLocked
ContentSlotUnlocked
ContentSlotRegenerated
ContentSlotArchived

CalendarRebalanced
CalendarVersionCreated
CalendarVersionRestored
```

These integrate with Core Contract #8.

---

# 78. Audit Integration

Audit:

```text
Strategy Changed
Pillar Allocation Changed
Content Type Allocation Changed
Slot Locked
Slot Unlocked
Mass Change Applied
Calendar Restored
Manual Override
```

AI-generated planning changes must remain distinguishable from manual changes.

---

# 79. Planning Source Attribution

A slot may originate from:

```text
Research
Analytics
Idea Pool
Manual
AI Planner
Duplicate
```

Store:

```text
source_type
source_id
```

where applicable.

This is different from:

```text
content source
```

in the Analyzer domain.

---

# 80. Content Type / Pillar Configuration

The Planner should consume the active Content Type and Pillar catalog.

The actual catalog is configurable by Admin/Configuration.

Planner should not hard-code business-specific content types permanently.

---

# 81. API Contract

Conceptual:

```text
GET    /content-plans
POST   /content-plans
GET    /content-plans/:id
PATCH  /content-plans/:id

POST   /content-plans/:id/generate
POST   /content-plans/:id/rebalance
POST   /content-plans/:id/regenerate

GET    /content-plans/:id/slots
PATCH  /content-slots/:id
POST   /content-slots/:id/lock
POST   /content-slots/:id/unlock
POST   /content-slots/:id/duplicate

GET    /content-plans/:id/versions
POST   /content-plans/:id/versions/:version/restore
POST   /content-plans/:id/undo
```

Exact API paths remain subject to API Architecture.

---

# 82. Planning Engine Service

Conceptual internal service:

```text
PlanningEngine.generate(plan_context)
PlanningEngine.rebalance(plan_context)
PlanningEngine.regenerate(selection)
PlanningEngine.validate(calendar)
PlanningEngine.score(calendar)
PlanningEngine.health(calendar)
PlanningEngine.preview_change(change)
```

---

# 83. Calendar Validation

Before a generated or edited calendar is committed:

```text
Validate Hard Constraints
→ Validate Strategy
→ Calculate Diversity
→ Calculate Workload
→ Calculate Health
→ Persist
```

A calendar can be persisted with warnings if soft constraints are imperfect.

It cannot be committed if hard constraints are violated.

---

# 84. Planning Engine Output

Generate should return:

```text
Calendar
+
Health Score
+
Warnings
+
Recommendations
+
Changed Slots
+
Version
```

Example:

```text
Generated:
12 slots

Warnings:
2 production workload warnings

Health:
88/100

Changed:
10 unlocked slots

Preserved:
2 locked slots
```

---

# 85. Explainability

Every significant automatic decision should have:

```text
reason
```

Examples:

```text
"Moved because this trend's expected shelf life ends in 72 hours."

"Changed pillar to improve target balance without moving locked slots."

"Kept slot unchanged because member locked it."
```

---

# 86. Error States

Recommended:

```text
PLAN_NOT_FOUND
WORKSPACE_ACCESS_DENIED
INVALID_PLANNING_PERIOD
NO_VALID_ACTIVE_DAYS
INVALID_FREQUENCY
INVALID_ALLOCATION
HARD_CONSTRAINT_CONFLICT
VERSION_CONFLICT
LOCKED_SLOT_CONFLICT
INSUFFICIENT_VALID_IDEAS
GENERATION_FAILED
REBALANCE_FAILED
```

---

# 87. Empty / Insufficient Opportunity State

If the planner needs more slots than available valid ideas:

```text
Valid Ideas:
7

Target Slots:
10
```

Return:

```text
INSUFFICIENT_VALID_IDEAS
```

with actionable explanation.

Possible options:

```text
Use Manual Ideas
Import Research Opportunities
Reduce Frequency
Extend Period
Generate Fewer Slots
```

The system should not fabricate unsupported content topics.

---

# 88. Core Invariants

```text
1. Planner is platform-agnostic.

2. Content Slot is the stable planning/production identity.

3. Hard constraints cannot be violated.

4. Locked member decisions cannot be overridden by AI.

5. AI recommendations require explanation.

6. Member strategy is never changed silently.

7. Pillar/content-type allocation produces integer slots.

8. Integer allocations total exactly the planned slot count.

9. Active-day frequency is not equivalent to calendar-day frequency.

10. Planner does not fabricate low-quality topics just to fill slots.

11. Manual edits are preserved during regenerate.

12. Regenerate Selected changes only selected eligible slots.

13. Regenerate From Date preserves slots before the chosen date.

14. Rebalance changes only unlocked slots.

15. Historical plan versions are preserved.

16. Undo does not rewrite audit history.

17. Server state is authoritative.

18. Calendar changes are persisted.

19. Analytics recommendations affect future strategy only after member action.

20. Research evidence/provenance remains traceable.

21. Content Slot state is separate from module-specific states.

22. Planner does not become the source of truth for Analyzer/Script/Editor internals.
```

---

# 89. Definition of Done

Core Contract #11 is complete when:

1. Content Plan exists.
2. Planning period exists.
3. Timezone is explicit.
4. Active Days exist.
5. Frequency exists.
6. Content Type allocation exists.
7. Content Pillar allocation exists.
8. Integer allocation is implemented.
9. Idea Pool exists.
10. Research Opportunity integration exists.
11. Analytics Recommendation integration exists.
12. Content Slot generation exists.
13. Hard constraints exist.
14. Soft constraints exist.
15. Smart Time Recommendation exists.
16. Anti-Repetition exists.
17. Production workload awareness exists.
18. Calendar scoring exists.
19. Calendar Health exists.
20. Monthly/Weekly/Grid views can consume the same calendar model.
21. Drag & Drop is supported.
22. Conflict detection exists.
23. Lock/Unlock exists.
24. Regenerate Selected exists.
25. Regenerate From Date exists.
26. Rebalance exists.
27. Bulk Actions exist.
28. Duplicate exists.
29. Change Preview exists.
30. Undo/version history foundation exists.
31. Autosave exists.
32. Cross-session persistence exists.
33. Planner → Analyzer handoff preserves `content_slot_id`.
34. Research/Analytics provenance is preserved.
35. Manual decisions remain authoritative.
36. Calendar does not silently mutate after member review.

---

# 90. Implementation Priority

Based on the PRD:

## P0

```text
Planning Period
Active Days
Frequency
Content Type Selection
Pillar Allocation
Integer Allocation
Auto Calendar Generation
Monthly / Weekly / Grid
Drag & Drop
Manual Edit
Lock / Unlock
Content Status
Content Slot ID
Conflict Detection
Regenerate Selected
Rebalance
Autosave / Persistence
Basic Anti-Repetition
```

## P1

```text
Idea Pool
Research Opportunity Integration
Analytics Recommendation
Smart Posting Time
Calendar Health Score
Production Workload
Bulk Actions
Duplicate
Calendar Change Preview
Undo
Basic Version History
Trend-Aware Scheduling
```

## P2

```text
Advanced Scoring
Predictive Scheduling
Scenario Planning
Multiple Calendar Variants
Advanced Performance Optimization
Automatic Strategy Optimization
Long-term Content Fatigue Modeling
```

These priorities are taken directly from the PRD Planner specification. fileciteturn16file4L164-L176

---

# 91. Dependencies

Depends on:

```text
Core Contract #1
Identity / User / Session

Core Contract #2
Role / Permission

Core Contract #3
Configuration

Core Contract #8
Audit / Events / Notifications

Core Contract #9
Workspace / Content Slot / Project Context

Core Contract #10
Research Intelligence
```

Consumes:

```text
Content Opportunity
Research Evidence
Analytics Recommendation
```

Produces:

```text
Content Plan
Content Idea
Content Slot
Planning Recommendations
```

Used by:

```text
Analyzer
Script / Blueprint
Asset Preparation
Editor
Analytics
```

---

# 92. Next Contract

The next recommended contract is:

> **Core Contract #12 — Analyzer & Multi-Source Content Intelligence**

It should define:

```text
Source Ingestion
Source Identity
Quality Gate
Structured Extraction
Fact / Opinion
Evidence
Claim
Angle
Virality Potential
Deep Source Intelligence
Media Intelligence
Cross-Source Analysis
Multi-AI
Content Readiness
Analyzer → Script Input Contract
```

This should reuse the normalized Research entities from Contract #10 rather than creating a second incompatible source/evidence model.
