# UIUX Design Plan

## Status

**UI/UX Design System Baseline — Derived from PRD Final**

This document defines the UI/UX system required for implementation.

It exists because the PRD states that the platform uses:

- Dual Theme Engine;
- Card-Based Architecture;
- Responsive Grid;
- desktop-first workstation usage;
- client-side processing where practical;
- persistent work state across navigation/session;
- detailed visual tokens referenced by the PRD as `UIUX_Design_Plan`. fileciteturn33file0L6-L15

This document therefore becomes the design reference for:

```text
Web UI
Admin UI
Member UI
Research
Planner
Analyzer
Blueprint
Editor
Support
Commerce
```

---

# 1. Source Boundary

The PRD is the source of truth for product behavior.

This document defines:

```text
visual system
component system
interaction conventions
responsive behavior
UI state conventions
accessibility baseline
design tokens
```

It must not redefine:

```text
business rules
pricing
entitlement
referral logic
support SLA
research scoring
planner constraints
```

When a UI requirement conflicts with a business rule:

> **Business rule wins.**

---

# 2. Design Principles

The platform UI follows these principles:

```text
1. Clear before decorative
2. Functional before ornamental
3. Consistent across modules
4. Explainable AI
5. Persistent work state
6. Responsive
7. Accessible
8. Dense enough for workstation productivity
9. Calm enough for long sessions
10. AI remains assistive, not visually authoritarian
```

---

# 3. Visual Foundation Required by PRD

The PRD explicitly establishes:

```text
Dual Theme Engine
Card-Based Architecture
Rounded Corner 12–16px
1px Border
Subtle Shadow / Glassmorphism
Responsive Grid
Desktop / Tablet / Mobile
```

fileciteturn33file0L6-L11

The remaining exact color/typography token values were referenced by the PRD as belonging to this `UIUX_Design_Plan`; the values below are therefore the **design-system proposal to operationalize that requirement**, not existing PRD business rules.

---

# 4. Design Tokens

## 4.1 Token Architecture

Use semantic tokens rather than hard-coding raw colors in individual components.

```text
Primitive Tokens
        ↓
Semantic Tokens
        ↓
Component Tokens
        ↓
Page
```

Example:

```text
blue-500
↓
color-primary
↓
button-primary-bg
```

---

# 5. Color System

## 5.1 Brand Direction

Recommended visual direction:

```text
Modern
AI-native
Professional
Calm
High information density
Minimal visual noise
```

A neutral foundation with one clear brand accent should be used rather than many competing accent colors.

---

## 5.2 Light Theme — Proposed Starting Tokens

These are proposed design tokens for implementation and can be adjusted before final visual sign-off.

```text
Background:
#F8FAFC

Surface:
#FFFFFF

Surface Secondary:
#F1F5F9

Text Primary:
#0F172A

Text Secondary:
#475569

Text Muted:
#64748B

Border:
#E2E8F0

Border Strong:
#CBD5E1

Primary:
#4F46E5

Primary Hover:
#4338CA

Primary Soft:
#EEF2FF
```

---

## 5.3 Dark Theme — Proposed Starting Tokens

```text
Background:
#0B1120

Surface:
#111827

Surface Secondary:
#1E293B

Text Primary:
#F8FAFC

Text Secondary:
#CBD5E1

Text Muted:
#94A3B8

Border:
#334155

Border Strong:
#475569

Primary:
#818CF8

Primary Hover:
#A5B4FC

Primary Soft:
#1E1B4B
```

---

# 6. Semantic Status Colors

Use semantic meaning consistently.

```text
Success:
#16A34A

Success Soft:
#DCFCE7

Warning:
#D97706

Warning Soft:
#FEF3C7

Error:
#DC2626

Error Soft:
#FEE2E2

Info:
#0284C7

Info Soft:
#E0F2FE
```

Do not use status colors merely for decoration.

---

# 7. Color Usage Rules

```text
Primary
→ primary actions

Success
→ completed / valid / paid / ready

Warning
→ caution / incomplete / stale / needs attention

Error
→ failed / blocked / invalid

Info
→ explanation / neutral system information
```

Never rely on color alone to communicate status.

Combine:

```text
color
+
icon
+
text
```

---

# 8. Typography

The PRD requires a complete typography token system but does not specify a fixed font family in the supplied final document. Therefore font family is a **design decision to be finalized before implementation**, not a business requirement.

## Recommended UI font

```text
Inter
```

with a system fallback stack:

```text
Inter,
ui-sans-serif,
system-ui,
-apple-system,
BlinkMacSystemFont,
"Segoe UI",
sans-serif
```

For an AI/content platform, this provides a clean productivity-oriented baseline.

---

# 9. Typography Scale

Proposed:

| Token | Size | Weight | Line Height | Use |
|---|---:|---:|---:|---|
| Display | 32px | 700 | 40px | rare page hero |
| H1 | 28px | 700 | 36px | page title |
| H2 | 24px | 700 | 32px | major section |
| H3 | 20px | 600 | 28px | card/section title |
| H4 | 18px | 600 | 26px | subsection |
| Body L | 16px | 400 | 24px | primary readable text |
| Body | 14px | 400 | 22px | standard UI text |
| Small | 13px | 400 | 20px | supporting information |
| Caption | 12px | 400 | 18px | metadata |
| Button | 14px | 600 | 20px | action text |

---

# 10. Typography Rules

```text
Page titles
→ H1

Primary section
→ H2/H3

Body
→ Body 14–16px

Metadata
→ 12–13px

Avoid
→ excessive font-size variation
```

Do not use font weight alone to communicate semantic state.

---

# 11. Spacing System

Use an 8-point foundation with 4px support values.

```text
4
8
12
16
20
24
32
40
48
64
80
```

Typical usage:

```text
4–8
→ icon/text spacing

12
→ compact component padding

16
→ normal card padding

24
→ section spacing

32
→ major content separation

48+
→ page-level separation
```

---

# 12. Border Radius

The PRD explicitly requires card rounding of:

```text
12–16px
```

fileciteturn33file0L8-L11

Recommended tokens:

```text
radius-sm: 8px
radius-md: 12px
radius-lg: 16px
radius-xl: 20px
radius-full: 9999px
```

Use:

```text
Cards:
12–16px

Buttons:
10–12px

Inputs:
10–12px

Modal:
16px

Pills:
9999px
```

---

# 13. Border System

Default:

```text
1px solid border
```

Strong border only when required for:

```text
focus
selected
error
high-contrast distinction
```

Avoid thick decorative borders.

---

# 14. Shadow / Glassmorphism

The PRD requires subtle shadow/glassmorphism.

Use it selectively.

Recommended:

```text
Card:
subtle shadow

Floating panel:
medium shadow

Modal:
stronger shadow

Glass:
only where background/contrast remains clear
```

Do not make every component translucent.

---

# 15. Layout System

## Desktop

Primary workstation.

Recommended content model:

```text
Global Sidebar
+
Top Utility Bar
+
Main Content
```

Example:

```text
┌───────────────┬─────────────────────────────────┐
│               │ Header / Breadcrumb / Actions │
│    Sidebar    ├─────────────────────────────────┤
│               │                                 │
│               │ Main Content                    │
│               │                                 │
└───────────────┴─────────────────────────────────┘
```

Exact navigation items remain owned by product/module requirements.

---

# 16. Tablet

Use:

```text
condensed navigation
collapsible sidebar
reduced card columns
preserved primary actions
```

Do not simply shrink desktop into a narrow layout.

---

# 17. Mobile

The PRD requires responsive support for mobile/tablet.

Core principle:

> Mobile is a functional adaptation, not merely a scaled desktop screenshot.

Recommended:

```text
Sidebar
→ drawer

Dense data table
→ card/list

Multi-column panels
→ stacked sections

Secondary actions
→ overflow menu
```

---

# 18. Responsive Breakpoints

Proposed starting tokens:

```text
Mobile:
< 640px

Tablet:
640–1023px

Desktop:
1024–1439px

Large Desktop:
≥ 1440px
```

Exact breakpoint values are implementation recommendations, not PRD business rules.

---

# 19. Page Container

Recommended:

```text
max-width:
1440px
```

with fluid gutters.

Desktop:

```text
24–32px horizontal padding
```

Tablet:

```text
20–24px
```

Mobile:

```text
16px
```

---

# 20. Grid System

Use:

```text
12-column desktop
8-column tablet
4-column mobile
```

where useful.

However, components should remain content-driven rather than forced into fixed grid geometry.

---

# 21. Card System

The PRD requires a card-based architecture. fileciteturn33file0L8-L11

Card anatomy:

```text
Card
├── Header
├── Content
├── Optional Meta
└── Optional Footer Actions
```

Cards should have:

```text
consistent padding
consistent radius
consistent border
clear hierarchy
```

---

# 22. Card Variants

Recommended:

```text
Default
Interactive
Selected
Warning
Error
Success
Compact
Dense
Metric
Insight
AI Recommendation
```

---

# 23. Buttons

Primary button:

```text
filled primary color
```

Secondary:

```text
neutral surface/border
```

Tertiary:

```text
text/ghost
```

Danger:

```text
error semantic color
```

Destructive action should not look identical to normal confirmation.

---

# 24. Button Rules

Each button should communicate:

```text
What action happens
```

Avoid:

```text
OK
Submit
Do It
```

Prefer:

```text
Generate Plan
Save Changes
Approve Payment
Regenerate Selected
Setujui & Siapkan Aset
```

This is especially important for AI operations.

---

# 25. Loading State

Use:

```text
Skeleton
Spinner
Progress
Status text
```

depending on operation duration.

For AI jobs:

```text
Analyzing source...
Generating...
Preparing assets...
```

Do not display an indefinite spinner without context.

---

# 26. Empty State

Every major page should have an empty state with:

```text
What is empty
Why
What user can do next
Primary action
```

Example:

```text
Belum ada Opportunity

Research belum menghasilkan opportunity pada konteks ini.

[Mulai Research]
```

---

# 27. Error State

Error UI should include:

```text
What happened
What can be done
Retry
Support path where relevant
```

Avoid exposing technical stack traces to members.

---

# 28. Saving State

The PRD explicitly requires a visual **"Tersimpan otomatis"** indication for persistent research/analyzer work. fileciteturn33file2L70-L78

Standard states:

```text
Saving...
Tersimpan otomatis
Gagal menyimpan
Ada perubahan belum tersimpan
```

---

# 29. AI Processing State

Use explicit language.

Examples:

```text
Menganalisis sumber...
Mencari pola...
Menyusun opportunity...
Membuat angle...
Menyusun script...
Menyiapkan prompt...
```

Avoid vague:

```text
Processing...
Loading...
```

when a more meaningful status is known.

---

# 30. Explainable AI UI

AI recommendations should show:

```text
Recommendation
Why
Evidence / Signals
Confidence where relevant
Affected Area
Action
```

The PRD requires significant planning recommendations to include a reason. fileciteturn32file3L119-L123

---

# 31. AI Recommendation Card

Recommended anatomy:

```text
┌─────────────────────────────────┐
│ Recommendation                  │
│                                │
│ What: Increase Video 10%       │
│ Why: Performance +14%          │
│ Evidence: 16 recent posts      │
│                                │
│ [Review Changes] [Apply]       │
└─────────────────────────────────┘
```

The exact data differs by module.

---

# 32. AI Confidence

Confidence should not be represented only by color.

Use:

```text
High
Medium
Low
```

plus supporting explanation where important.

Never imply:

```text
AI score = guaranteed outcome
```

---

# 33. Modal

Use modals for:

```text
confirmation
focused editing
short forms
important decisions
```

Avoid large workflows inside modal if they require:

```text
many sections
long scrolling
complex data analysis
```

Use full page or side panel instead.

---

# 34. Drawer / Side Panel

Recommended for:

```text
detail preview
filters
AI recommendations
source evidence
secondary configuration
```

Especially useful in:

```text
Research
Planner
Analyzer
Blueprint
```

because the user often needs context without leaving the current page.

---

# 35. Tabs

Use tabs when content belongs to one context but has distinct views.

Examples:

```text
Research:
Overview
Competitor
Trend
Keyword
Audience

Blueprint:
Angle 1 — Gambar
Angle 1 — Video
```

The PRD explicitly describes small tabs for multiple Blueprint combinations within one source context. fileciteturn32file8L311-L315

---

# 36. Tables

Tables should be used for:

```text
dense comparison
administrative data
transactions
audit
provider management
finance
```

Use cards/list patterns for mobile.

---

# 37. Status Badge

Standard anatomy:

```text
Icon + Label
```

Examples:

```text
● Paid
● Pending
● Failed
● Locked
● Ready
● Needs Revision
```

Do not use color without text.

---

# 38. Navigation

Global navigation should remain consistent.

Recommended hierarchy:

```text
Platform
├── Dashboard
├── Research
├── Planner
├── Analyzer
├── Content Production
├── Editor
├── Analytics
├── Support
└── Account
```

Admin navigation is separate from member navigation and should reflect permissions.

The exact menu set is governed by the final product scope.

---

# 39. Sidebar Behavior

Desktop:

```text
Expanded
```

Tablet:

```text
Collapsible
```

Mobile:

```text
Drawer
```

The sidebar should preserve current section and indicate active route.

---

# 40. Breadcrumb

Use breadcrumbs when navigation depth exceeds one or two levels.

Example:

```text
Research
→ Opportunity
→ Opportunity Detail
```

Keep breadcrumb secondary to the page title.

---

# 41. Header

Page header may contain:

```text
Title
Subtitle
Breadcrumb
Primary Action
Secondary Action
Status
```

Avoid overcrowding.

---

# 42. Search

Search input should visually distinguish:

```text
global search
module search
filter
```

Do not make one search field appear to search everything if scope differs.

---

# 43. Filter Pattern

Use:

```text
Filter Button
→ Filter Panel
→ Active Filter Chips
→ Clear All
```

for dense data modules such as:

```text
Research
Planner
Analytics
Admin
Support
Referral
```

---

# 44. Form System

All forms should support:

```text
Label
Helper text
Required indicator
Validation
Error
Success
Disabled
Loading
```

Never rely on placeholder as the only label.

---

# 45. Destructive Actions

Destructive actions require:

```text
explicit label
confirmation
impact explanation
```

Examples:

```text
Delete
Reset Research
Reset Analyzer
Cancel Subscription
Approve Refund
```

For irreversible actions, confirmation should explain consequences.

---

# 46. AI Action Safety

Before AI performs a potentially broad change, show:

```text
Affected Items
Before
After
Reason
```

This follows the PRD's explicit Change Preview requirement for planning. fileciteturn32file5L201-L215

---

# 47. Research UI

Research should visually prioritize:

```text
Data
→ Insight
→ Opportunity
→ Action
```

The user should not have to interpret raw data without context.

---

# 48. Research Evidence UI

Evidence should be accessible without forcing navigation away from the current context.

Recommended:

```text
View Evidence
→ Side Panel / Drawer
```

with:

```text
source
timestamp/page
metric
confidence
freshness
```

---

# 49. Planner UI

Planner requires:

```text
Monthly
Weekly
Grid
```

views according to PRD. fileciteturn32file7L271-L277

Calendar cards show at minimum:

```text
Date
Time
Content Type
Title/Topic
Pillar
Priority
Content Status
Production Indicator
Lock Indicator
```

---

# 50. Planner Interaction

Drag & drop must show:

```text
valid move
conflict
locked state
```

The PRD requires hard validation around:

```text
active day
daily frequency
timestamp conflict
locked slot
```

fileciteturn32file7L279-L287

---

# 51. Analyzer UI

Analyzer should make visible:

```text
Source
Quality
Evidence
Angles
Hook
Score
Confidence
Readiness
```

Add-on indicators should clearly show which intelligence capabilities are active.

---

# 52. Blueprint UI

Blueprint should present:

```text
Content Context
Script
Hook
Evidence
Visual Blueprint
Prompts
Asset Strategy
Editor Mapping
QA
```

The PRD specifically requires Module 3 to show relevant Analyzer context and explain "Kenapa Script Ini?", rather than making the AI decision opaque. fileciteturn32file0L15-L25

---

# 53. Blueprint Variant UI

When several Angle × Content Type combinations exist:

```text
Angle 1 — Gambar
Angle 1 — Video
Angle 2 — Video
```

Use compact tabs or segmented navigation inside the same content context.

Each variant must visibly indicate:

```text
Draft
Ready
Approved
Needs Revision
```

---

# 54. Prompt UI

Default:

```text
Structured Prompt
```

Editable by member.

Advanced Prompt Studio:

```text
Raw final_prompt
Provider-specific optimization
Auto-Fix
```

The PRD explicitly separates default structured prompt from the paid Advanced Prompt Studio add-on. fileciteturn32file2L97-L104

---

# 55. Editor UI

For the image/carousel editor, the PRD explicitly includes:

```text
Auto-Populate
Template Selection
Multi-Slide Layout Bar
Sidebar
Editing Panel
Font
Brand Kit Colors
Logo
Background Remover
Layer Repositioning
```

fileciteturn33file1L21-L26

For video, use:

```text
Timeline
Track
Clip
Voiceover
Subtitle
Graphic
Asset
```

consistent with the blueprint mapping.

---

# 56. Client-Side Processing UI

The PRD explicitly expects browser-side processing whenever possible, including:

```text
cropping
resizing
filter preview
text/layer placement
background remover preview
video trimming
waveform
drag & drop timeline
live text overlay
member media preparation
```

Heavy AI operations and final export remain server-side. fileciteturn33file2L59-L68

---

# 57. Autosave UX

Persistent modules should always provide a lightweight status indicator.

Example:

```text
✓ Tersimpan otomatis
```

The indicator should not interrupt the workflow.

---

# 58. Unsaved Changes

When navigation risks losing changes:

```text
No unsaved state
→ navigate

Unsaved state
→ save or confirm
```

However, the system should prefer autosave so confirmation dialogs remain uncommon.

---

# 59. Toasts

Use toast notifications for:

```text
short-lived confirmation
success
warning
non-blocking errors
```

Examples:

```text
Opportunity disimpan
Perubahan tersimpan
3 slot berhasil diregenerate
```

Avoid using toast as the only place to communicate critical information.

---

# 60. Notifications

Persistent notifications belong to Notification infrastructure.

Use:

```text
notification center
badge
in-app notification
email where configured
```

for:

```text
payment
support
referral
generation completion
important system events
```

---

# 61. Accessibility Baseline

The PRD does not provide a full accessibility specification, so the following is a recommended engineering baseline:

```text
Keyboard navigation
Visible focus
Semantic HTML
Readable contrast
Accessible labels
Screen-reader-compatible controls
44px minimum touch target where practical
```

Accessibility should be validated independently of theme.

---

# 62. Dark Mode

Dark Mode is not a recoloring exercise.

Components must use semantic tokens:

```text
surface
text
border
interactive
status
```

rather than hard-coded light-theme colors.

---

# 63. Theme Persistence

User theme preference should persist across:

```text
page navigation
refresh
logout/login
```

unless a global market/system policy explicitly overrides it.

---

# 64. Responsive Priority

The platform is desktop-first for productivity, but the same business action should remain accessible on mobile.

Priority:

```text
Desktop
→ full productivity layout

Tablet
→ condensed productivity

Mobile
→ essential actions + readable content
```

---

# 65. Design Density

Recommended density modes:

```text
Comfortable
Compact
```

Default should prioritize readability.

Dense views are appropriate for:

```text
Admin
Finance
Research tables
Planner Grid
Analytics
```

---

# 66. Iconography

Use one consistent icon system throughout the application.

Icons should:

```text
support meaning
not replace critical labels
remain recognizable in dark/light themes
```

Do not mix visual icon styles arbitrarily.

---

# 67. Illustration / Empty-State Art

Use sparingly.

Primary focus remains:

```text
task
data
action
```

rather than decoration.

---

# 68. Motion

Use subtle motion for:

```text
loading
navigation
panel opening
drag/drop
success
focus
```

Avoid excessive animation in:

```text
dense research
planner
analytics
admin
```

Motion should not block work.

---

# 69. Hover / Focus / Active

Interactive components need distinct:

```text
default
hover
focus
active
disabled
selected
error
```

States must work in both themes.

---

# 70. Component Naming

Use shared component names.

Recommended:

```text
Button
IconButton
Input
Select
Combobox
Textarea
Card
Badge
Tabs
Modal
Drawer
Toast
Tooltip
DataTable
EmptyState
ErrorState
Skeleton
Progress
StatusBadge
ConfirmDialog
```

---

# 71. Component Ownership

Global components belong to:

```text
Design System / UI Foundation
```

Domain-specific components belong to their module.

Example:

```text
CalendarCard
→ Planner

OpportunityCard
→ Research

BlueprintVariantCard
→ Blueprint
```

Do not move domain logic into generic UI components.

---

# 72. UI Component API Rule

Shared components should accept semantic props:

```text
variant
size
state
disabled
loading
```

rather than arbitrary style overrides everywhere.

This keeps visual consistency enforceable.

---

# 73. Design Token Rule

Developers should use:

```text
semantic tokens
```

instead of raw:

```text
#4F46E5
#E2E8F0
16px
```

inside page-level CSS unless the token itself is being defined.

---

# 74. Theme Token Structure

Recommended conceptual structure:

```text
color/
  background/
  surface/
  text/
  border/
  primary/
  status/

typography/
  family/
  size/
  weight/
  line-height/

spacing/
radius/
shadow/
motion/
breakpoint/
```

---

# 75. Brand Kit Integration

Brand Kit values may influence:

```text
template colors
logo
content canvas
export visuals
```

but should not accidentally override the core application UI theme.

Distinguish:

```text
App Theme
```

from:

```text
Content Brand Kit
```

---

# 76. UI vs Content Design

The application's UI:

```text
navigation
cards
forms
tables
buttons
```

is governed by this design system.

Generated content:

```text
carousel
video
brand visuals
templates
```

is governed by:

```text
Brand Kit
Template
Blueprint
Prompt
Editor
```

Do not merge these systems.

---

# 77. Security UI

Security controls should be communicated clearly when appropriate.

Examples:

```text
Protected Content
Session Revoked
Permission Denied
```

However, the PRD's finalized Content Protection rule states that when protection is active, the member does not receive an indicator that the page is currently protected. That rule remains authoritative.

---

# 78. Error Language

Use consistent language.

Recommended:

```text
Gagal memuat data
Tidak memiliki akses
Perubahan belum tersimpan
Terjadi konflik versi
Penyedia sedang tidak tersedia
Coba lagi
```

Avoid exposing infrastructure terms unless relevant.

---

# 79. Localization Rule

UI strings must not be hard-coded across components.

Use:

```text
translation key
→ locale resource
```

Minimum:

```text
English
Indonesian
```

Additional languages can be added later.

---

# 80. Date / Time / Currency Display

Display should respect:

```text
market
locale
currency
timezone
```

Never hard-code:

```text
Rp
USD
date format
24h/12h
```

into reusable components.

---

# 81. Form Validation Language

Validation should be:

```text
specific
short
actionable
```

Bad:

```text
Invalid value
```

Better:

```text
Minimum 2 MB
Format yang didukung: PDF, PNG, JPG
```

---

# 82. Confirmation Pattern

Use confirmation for:

```text
delete
reset
refund
cancel
approve payment
bulk changes
large regeneration
```

Include:

```text
Action
Impact
Confirm
Cancel
```

---

# 83. AI Regeneration UI

For large regeneration:

```text
Selection
→ Preview
→ Confirm
→ Processing
→ Result
```

For granular regeneration:

```text
Regenerate Hook
Regenerate Slide 3
Regenerate Shot 4
```

The PRD explicitly requires granular regeneration without deleting manual changes in unrelated sections. fileciteturn32file2L77-L95

---

# 84. Version History UI

Use a consistent pattern:

```text
Version
Actor
Time
Reason
Change Summary
Restore
Compare
```

Old versions should not disappear merely because a new version exists.

---

# 85. AI Explainability Pattern

Recommended card:

```text
┌────────────────────────────────────┐
│ AI Recommendation                  │
├────────────────────────────────────┤
│ What                               │
│ Increase Video 10%                 │
│                                    │
│ Why                                │
│ Recent account data shows...      │
│                                    │
│ Evidence                           │
│ 16 posts / 30 days                 │
│                                    │
│ Impact                             │
│ 2 unlocked slots                   │
│                                    │
│ [Review Changes] [Apply]          │
└────────────────────────────────────┘
```

---

# 86. Responsive AI Panels

On mobile:

```text
AI Side Panel
→ Bottom Sheet / Full-screen Panel
```

Do not compress long AI explanations into unreadable narrow cards.

---

# 87. Research Data Visualization

Visualizations should emphasize:

```text
trend
comparison
baseline
median
change
confidence
freshness
```

Avoid chart decoration that does not help decision-making.

---

# 88. Chart Color Rule

Use semantic categorical colors sparingly.

Do not assign arbitrary rainbow colors.

Maintain:

```text
theme consistency
status semantics
contrast
```

---

# 89. Planner Calendar Visual Rules

Locked slot:

```text
lock icon
```

Production status:

```text
badge
```

Priority:

```text
text/badge
```

AI recommendation:

```text
subtle AI indicator
```

Conflict:

```text
warning/error marker
```

Do not overwhelm cards with too many badges.

---

# 90. Research Opportunity Card

Suggested hierarchy:

```text
Opportunity Title
Why Now
For Whom
Angle
Score / Confidence
Evidence
Recommended Action
```

Action:

```text
Send to Planner
Send to Analyzer
Save
```

---

# 91. Blueprint Variant Card

Suggested hierarchy:

```text
Angle
Content Type
Status
Readiness
Evidence Count
QA
Primary Action
```

Example:

```text
Angle 1 — Video
Ready for Review
Evidence 7
QA: PASS

[Review Blueprint]
```

---

# 92. Admin UI Density

Admin requires higher information density.

Use:

```text
tables
filters
bulk actions
audit
advanced search
side panels
```

But retain the same:

```text
theme
typography
spacing
component language
```

---

# 93. Design Review Checklist

Before accepting a screen:

```text
[ ] Uses design tokens
[ ] Works light theme
[ ] Works dark theme
[ ] Responsive
[ ] Loading state
[ ] Empty state
[ ] Error state
[ ] Success state
[ ] Permission state
[ ] Keyboard/focus
[ ] Correct typography
[ ] Consistent spacing
[ ] Correct card radius
[ ] Correct status semantics
[ ] No hard-coded business rule
[ ] No raw provider logic
```

---

# 94. UI Implementation Rules for Vertical Slices

Every vertical slice that includes UI must implement:

```text
Visual System
+
Real API
+
Real Persistence
+
Real State
```

The sequence is:

```text
Implement UI skeleton
→ connect API
→ connect real data
→ test states
→ refine UI
→ acceptance
```

Do not postpone UI behavior validation until the end of the platform.

---

# 95. P0 UI Priority

P0 should establish the reusable design system early.

First shared components:

```text
AppShell
Sidebar
Header
Button
Input
Select
Card
Badge
Tabs
Modal
Drawer
Toast
DataTable
EmptyState
ErrorState
Skeleton
ConfirmDialog
LoadingIndicator
```

Then domain screens use them.

---

# 96. P0.00 UI Responsibility

P0.00 should establish:

```text
Theme tokens
Typography tokens
Spacing
Radius
Core components
Light mode
Dark mode
App Shell
Responsive structure
Accessibility baseline
```

This should be done before the first major business page so later slices do not invent independent visual systems.

---

# 97. Design Token Decision Status

### PRD-defined

```text
Dual Theme Engine
Card-Based Architecture
12–16px Card Radius
1px Border
Subtle Shadow / Glassmorphism
Responsive Desktop / Tablet / Mobile
```

### Recommended in this Design Plan

```text
Color token values
Font family
Typography scale
Spacing scale
Breakpoints
Component variants
Accessibility baseline
```

Recommended values are not business requirements and may be adjusted during visual review before implementation lock.

---

# 98. Open UI Decisions

Before P0.00 visual implementation is locked, confirm:

```text
1. Final Brand Primary Color
2. Final Font Family
3. Whether Inter is accepted
4. Exact Light/Dark semantic token values
5. Logo / Brand Mark
6. Final App Shell navigation arrangement
7. Glassmorphism strength
8. Exact responsive breakpoints
```

These are visual design decisions, not PRD business rules.

---

# 99. Final UI/UX Principle

The platform should visually communicate:

```text
Professional
Clear
Trustworthy
AI-assisted
Productive
Calm
Consistent
```

The UI should never make AI appear to have authority beyond what the business/domain rules actually allow.

The member should always be able to understand:

```text
What happened
Why it happened
What the AI recommends
What will change
What is saved
What requires action
```

---

# 100. UI/UX Design Plan Completion Criteria

This document is ready to support implementation when:

```text
[ ] Theme architecture defined
[ ] Light/Dark semantic tokens defined
[ ] Typography defined
[ ] Spacing defined
[ ] Radius defined
[ ] Component system defined
[ ] Responsive rules defined
[ ] Loading/Empty/Error patterns defined
[ ] Autosave pattern defined
[ ] AI recommendation pattern defined
[ ] Research UI pattern defined
[ ] Planner UI pattern defined
[ ] Analyzer UI pattern defined
[ ] Blueprint UI pattern defined
[ ] Editor UI relationship defined
[ ] Admin density defined
[ ] Accessibility baseline defined
[ ] Localization rules defined
[ ] UI vs Content Brand Kit distinction defined
[ ] Open visual decisions identified
```

---

# 101. Next Step

The next document should be:

> **P0.00 — Implementation Specification: Architecture & Development Skeleton**

During P0.00, the reusable UI foundation from this document should be established alongside the application skeleton:

```text
Theme
Typography
Tokens
App Shell
Shared Components
Responsive Layout
Accessibility
```

After that, the UI system is reused rather than reinvented on every subsequent vertical slice.
