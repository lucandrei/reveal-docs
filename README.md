# Handoff: Reveal — Onboarding Guide Dashboard

## Overview
Reveal is a SaaS dashboard for managing video-driven onboarding guides. It pairs with a Chrome extension that lets creators record themselves walking through a product UI step-by-step. The dashboard handles guide management, analytics, checklists, embedding, and workspace settings.

The core product concept: a founder or team member records a guide inside the Chrome extension. Their face appears as a circular "video bubble" — first as a large central bubble in an optional **Personal Intro**, then as a smaller bubble that follows the user through each tooltip step. Recording uses a method called **Speak then Click**: the founder speaks while pointing at a UI element, and *clicking* that element ends the recording for that step. This produces one short clip per step, scoped to the action it describes, which makes per-step re-recording and uploads possible later.

Guides can be grouped into Checklists for structured onboarding journeys.

The product domain is **reveal.software**.

## About the Design Files
The files bundled in this package are **high-fidelity HTML prototypes** built with React + Babel (inline JSX) and Tailwind CSS. They are design references — not production code. The task is to **recreate these designs in a real codebase** using **React + Shadcn/ui + Tailwind CSS** (which the team has already chosen). Do not ship the HTML files directly.

## Fidelity
**High-fidelity.** All colours, typography, spacing, interactions, and copy are final or near-final. Recreate pixel-faithfully using Shadcn/ui primitives where applicable (Card, Badge, Button, Input, Select, Tabs, Switch, Slider, Dialog, etc.), overriding styles as needed to match the design tokens below.

> **Note:** The full design language (tokens, typography rules, spacing scale, etc.) is currently **work in progress**. Treat what's below as a starting point — expect revisions before lock.

---

## Design Tokens *(work in progress)*

### Colours
```
--bg:           #F4F4F5       /* zinc-100 — page background */
--card:         #FFFFFF       /* white cards */
--border:       #E8E8EB       /* card/input borders */
--border-strong:#DCDCE0       /* hover borders */
--text:         #18181B       /* primary text / zinc-900 */
--text-2:       #52525B       /* secondary text / zinc-600 */
--text-3:       #A1A1AA       /* muted / zinc-400 */
--accent:       oklch(0.64 0.18 28)   /* Reveal red-orange — primary action */
--accent-soft:  oklch(0.96 0.04 28)   /* accent tint background */
--pos:          oklch(0.64 0.14 150)  /* green — positive delta / published */
--neg:          oklch(0.62 0.17 25)   /* red — negative / warning */
--draft:        oklch(0.65 0.12 75)   /* amber — draft status */
```

### Typography
- **Font family:** Inter (all weights). No serif. Use `font-feature-settings: "tnum"` on numeric/code elements for tabular figures.
- **Page headings:** 28px / semibold / letter-spacing -0.02em
- **Section headings:** 15–16px / semibold
- **Body:** 13–13.5px / regular or medium
- **Labels / caps:** 10.5–11px / mono uppercase / tracking-widest / color: --text-3
- **Large numbers (KPIs):** 24–30px / semibold / letter-spacing -0.035em

### Spacing
- Page outer padding: 24px horizontal, 24px top
- Card inner padding: 16–24px (use p-4 / p-5 / p-6)
- Gap between cards: 16px (gap-4)
- Sidebar width: 244px fixed

### Border radius
- Cards: 14px (rounded-[14px])
- Buttons: 8px (rounded-lg)
- Chips/badges: 999px (rounded-full)
- Inputs: 8px

### Shadows
- Cards: `0 1px 0 rgba(24,24,27,.02), 0 1px 2px rgba(24,24,27,.04)` (very subtle)
- Modals: `0 24px 60px rgba(0,0,0,.18)`
- Elevated panels: `0 12px 40px rgba(0,0,0,.14)`

### Icons
Lucide icons, 16px default, 1.6px stroke, round linecap/linejoin. Use `lucide-react` package.

---

## Global Layout

```
┌─────────────────────────────────────────────────────────┐
│  Sidebar (244px fixed, sticky)  │  Main content (flex-1) │
└─────────────────────────────────────────────────────────┘
```

### Sidebar (all pages)
- **Workspace switcher** (top): logo square + workspace name + plan badge + chevron. Clicking opens a workspace selector dropdown (not yet designed — placeholder).
- **Search bar**: full-width, `⌘K` shortcut badge on right.
- **Primary nav** (4 items): Home, Guides (with guide count badge), Checklists, Settings. Active item: white bg, border, subtle shadow.
- **Pinned guides** section: small coloured dot + truncated guide name. Expandable list.
- **Bottom area**: Chrome extension card (version, status, "Open editor" button) + user avatar row (name, email, `…` menu).

---

## The Recording Flow

This is the foundational creator UX. The dashboard never records — the Chrome extension does. The dashboard inspects, organises, and publishes.

### 1. Trigger
Creator clicks **"Record new guide"** on the dashboard's Guides page (top-right). This deep-links into the Chrome extension.

### 2. Personal Intro (optional, on by default)
Before pressing Record, the creator can toggle **Personal Intro** on or off in the extension. It defaults to on.

When recording starts:
1. A **3-second countdown** plays.
2. If Personal Intro is enabled, a **large central camera bubble** comes into view. The creator speaks an opening message — like a presentation — that precedes the actual walkthrough.
3. When done, the creator clicks **"Finish Intro"** to move into the guide proper.

### 3. Speak then Click
Once the intro ends (or immediately, if it was disabled), the guide begins. Each step works like this:
- The creator speaks while pointing the user toward a UI element.
- The creator *clicks* that UI element.
- The click **ends the recording** for that step.

This produces one short, self-contained clip per step. Element selector and clip are bundled together as one Step.

### 4. Why Speak then Click matters
Because each step is its own clip, **each step can later be re-recorded individually** or **replaced with a pre-recorded video** — without touching the rest of the guide. Per-step uploads exist *because* of this method, not as a separate feature.

---

## Pages

---

### 1. Home (`Home.html`)

**Purpose:** Dashboard overview for the workspace. Quick health check across all guides.

#### Layout
```
Header (greeting + actions)
KPI row (4 cards, equal width)
Top performer / Needs attention (2 columns)
Recent guides table (2/3) + Latest feedback (1/3)
Footer
```

#### Header
- Left: `"Good afternoon, [Name]"` (28px semibold). Below: contextual insight string — e.g. "3 guides gained traction this week. 1 guide is losing users at step 2." Highlight counts in semibold, negative figures in `--neg`.
- Right: date-range picker button, bell icon button, "New guide" primary button (accent colour).

#### KPI Cards (×4)
Each card contains:
- Icon + label (11px caps, --text-3)
- Large value (30px semibold)
- Optional sub-label (e.g. "/ 5.0")
- Delta chip (green/red with up/down arrow icon + percentage mono)
- Sparkline (84×28px SVG, area fill, matches delta colour)

KPIs: **Impressions**, **Guide starts**, **Completion rate**, **Avg. rating**

#### Top Performer / Needs Attention cards
Side-by-side. Each contains:
- Status chip ("Top performer" green / "Needs attention" red) + timeframe
- "Open" button (right-aligned)
- Guide title + author bubble + face rating icon
- 3-column stat row: completion %, starts count, WoW delta
- Step funnel: mini bar chart (one bar per step, scaled by reach %). Drop step bar is red.
- Insight callout: icon + descriptive text in a muted box.

#### Recent Guides Table
Columns: Guide (bubble + title + status chip) | Steps | Impressions | Completion (progress bar + %) | Rating (face icon) | Updated | Actions (…)

Hover row: show `…` menu button.

#### Latest Feedback Panel
3 most recent feedback entries. Each: face rating icon + quoted text + user email + guide name + timestamp.

---

### 2. Guides (`Guides.html`)

**Purpose:** Browse, filter, and manage all guides in the workspace.

#### Page Header
- Title "Guides" + total count chip
- Description string
- Right: "Record new guide" accent button (deep-links to Chrome extension)

#### Filter / Tab Bar (two rows)
Row 1 — Status tabs: All | Published | Drafts | Archived (each with count and coloured dot)
Row 2 — Search input (240px) | Performance toggle group (Any / High / Low) | "Has feedback / issues" toggle button | View switcher (Gallery / List icons)

All controls in row 2 are `h-9` (36px) to match the tab bar height.

#### Gallery View (default)
3-column grid (4-column in compact mode via Tweaks). Each card:
- **Poster** (16:9 aspect ratio): gradient background, mock UI skeleton, dashed tooltip target circle, tooltip callout, founder bubble (bottom-right), step count chip (top-left), completion progress bar (bottom edge)
- **Draft posters**: desaturated (CSS filter), "DRAFT · UNPUBLISHED" pill overlay, dashed card border, off-white card background
- **Card body**: title + author + updated, status chip, completion bar (published) or "Finish & publish" CTA (draft), impressions + feedback count, drop-off warning pill ("Leaks at step N" in red)
- Checkbox appears on hover (top-left), selection highlights border

#### List View
Dense table. Columns: Checkbox | Guide (mini thumbnail + title + author) | Status | Steps | Views | Completion (bar + %) | Drop-off | Feedback count | Updated | Actions

Mini thumbnail: 64×36px, gradient bg, play button on hover overlay, author bubble.

#### Bulk Selection
Floating sticky bar appears when ≥1 guide selected: "N selected" + Clear + Archive button + Delete button (red text).

#### Empty State
Centered card: illustration (circular gradient + founder bubble + pulsing record dot), headline, description, "Record a guide" accent CTA.

#### Tweaks Panel
Floating bottom-right panel (toggled via toolbar): Layout (Gallery/List), Density (Comfortable/Compact), Show drop-off badges (on/off), State (With data/Empty).

---

### 3. Guide Detail (`Guide Detail.html`)

**Purpose:** Detailed view of a single guide — analytics, steps management, embedding, and behaviour configuration.

#### Hero Card
- Left: 280×168px video poster (gradient bg + mock UI + founder bubble + play button overlay)
- Right: breadcrumb, title, status chip, author + meta, inline KPI ribbon (Impressions / Starts / Completion / Rating)
- Actions: Unpublish/Publish toggle, Duplicate, **Open in Editor** (accent, opens Chrome extension)

#### Sticky Tab Bar
4 tabs: **Overview** | **Steps** | **Embed** | **Behavior**

---

#### Overview Tab (4 layout variations via Tweaks)

**Shared KPIs (×5):** Impressions, Starts, Completion rate, Avg. watch time, Avg. rating

**Layout 1 — Classic Split**
KPIs → Step funnel chart → Steps list → Rating breakdown + Feedback

**Layout 2 — Funnel-First**
KPIs → Steps ARE the funnel: each step is a full-width bar that scales with completion %. Bar background = poster gradient. Inside: thumbnail + title + element selector + completion % + duration. Hover reveals "Edit" button.

**Layout 3 — Two-Column Narrative**
Left: Timeline steps list (numbered circles on a vertical line, coloured by completion health). Each step: thumbnail + title + element selector + duration + completion bar + drop-off callout.
Right (sticky): KPI summary card + Rating breakdown + Feedback stream.

**Layout 4 — Journey Curve**
KPIs → SVG completion curve (accent colour, area fill under curve, dots at each step, dashed red ring on drop-off steps) → 8-column step card grid below curve → Rating + Feedback.

**Step Funnel Chart (shared component)**
Horizontal bar chart. One row per step. Columns: step label | bar (scaled width = completion %) | % value | drop delta. Drop ≥5% highlighted in red.

**Rating Breakdown**
Stacked full-width bar (love/happy/neutral/sad segments). Below: per-face row with icon + label + count + %.

**Latest Feedback**
3 entries: face icon + quoted text + user email + timestamp.

---

#### Steps Tab
Read-only table. Columns: index (padded) | thumbnail (72×40px) | title | element selector (monospace) | duration | completion bar | "Open in Editor" button (hover-reveal) | "Upload video" button (hover-reveal — replaces this step's clip with a pre-recorded video)

Header: "Open in Editor" primary button opens Chrome extension at the guide's URL.

> **Note:** A "step" always has a video clip. There are no silent / video-less steps. Re-recording and per-step uploads happen in the extension (the dashboard inspects).

---

#### Embed Tab — 3 cards

**Card 1: Pixel Installation**
- Status chip: "Pixel detected on acme.com · 4 pages" (green, pulsing dot) or "Not detected" (red)
- Site-wide toggle switch
- Dark code block (`#18181B` bg) with copy button — shows `<script>` tag with workspace key
- 3 stat tiles: detected domain + last ping, pageviews today, users mid-guide (live count with pulsing dot)
- Action buttons: toggle detection demo, test on domain, copy pixel key

> **Why the pixel matters.** Without the pixel installed, end users **cannot trigger a guide at all** — not via the Reveal button, not via dynamic link, not at all. Guides live inside the pixel script (or, alternatively, inside the Reveal Chrome extension). The pixel is the foundation.
>
> **The extension exception:** a user with the Reveal Chrome extension installed *can* experience a guide on a site that doesn't have the pixel. This is reserved for a future direction — community-recorded tutorials where users teach other users on apps they don't own the code to. Not v1.

**Card 2: Reveal Button**
- Left: live button preview on dotted canvas (scales 1.1×). Style switcher: Solid / Reveal red / Outline. Label text input.
- Right: generated embed code block (updates live with selected style/label). "Preview on site" + "Edit brand" buttons.

**Card 3: Dynamic Link**
- Full URL (`?reveal=guide_id`) in a copy-ready row
- 3 meta tiles: param name, expiry (never), uses (unlimited)
- HTML usage example in a muted box

---

#### Behavior Tab
Form layout: 220px label column + content column. Sections:

| Field | Type | Options | Status |
|---|---|---|---|
| Trigger | Radio cards | Auto on load / After delay (slider 0–30s) / Manual / On element click | "On element click" is **TBD** |
| Audience | Radio cards | New users / Returning / All / Custom segment | "Custom segment" is **TBD** |
| Identity ingestion | n/a | `reveal.identify(userId, traits)` — Segment-style identify call | **TBD** |
| On skip | Radio cards | Resume next session / Restart next session / Never show again | — |
| Skip threshold | Slider | 1–10 skips before permanent dismiss | — |
| Frequency cap | Segmented control | Once / Daily / Weekly / Unlimited | — |
| Feedback screen | Toggle | Show face rating after final step / Off | — |

"Save changes" primary button (top-right of card).

---

### 4. Checklists (`Checklist.html`)

**Purpose:** Browse and manage guide checklists — ordered journeys for new users.

#### Page Header
Title "Checklists" + count chip + description + "New checklist" accent button

#### Status Tabs
All | Published | Drafts (with counts and status dots)

#### Checklist Cards (3-column grid)
Each card:
- **Header band**: status chip, title, description (2 lines max)
- **Guide stack preview**: row of 24px poster thumbnails + "+N more" if overflow + guide count label
- **Body** (published): full-completion progress bar + % | starts count | audience
- **Body** (draft): "Finish & publish" dashed CTA box
- Footer: author bubble + updated timestamp + "View →" link
- `…` menu on hover

**Ghost "New checklist" card**: dashed border, centered icon + label. Same grid position.

#### New Checklist Modal (2-step)
**Step 1 — Details:**
- Name input (required)
- Description textarea (optional)
- Audience: 2×2 radio card grid (New users / Returning / All / Custom segment)

**Step 2 — Add Guides:**
- Search input
- Scrollable guide list: checkbox + poster thumbnail + title + step count + completion %
- Selected count footer
- "Create checklist" accent button (disabled until ≥1 guide selected)

---

### 5. Checklist Detail (`Checklist Detail.html`)

**Purpose:** Analytics, guide reordering, embedding, and behaviour for a single checklist.

#### Hero Card
- Left: 120×75px custom thumbnail (mini progress list with stacked avatar bubbles)
- Right: breadcrumb, title, status chip, description, author + meta (guide count, audience, updated)
- Inline stats: Starts / Full completion / Avg. time to finish / Guide count
- Actions: Unpublish/Publish, Duplicate, "Add guide" accent button

#### Tabs: Overview | Guides (N) | Embed | Behavior

---

#### Overview Tab

**KPIs (×5):** Starts, Full completion %, Avg. time to finish, Avg. guides finished per user, Avg. rating

**Checklist Funnel Card:**
- SVG completion curve (accent, area fill, dots at each guide, guide index labels below)
- Below curve: per-guide rows (index | poster | title | completion bar + % | starts | drop-off chip)

**Drop-off Insight Callout:**
Accent-tinted card: icon + auto-generated insight e.g. "34% of users who finish guide 2 don't start guide 3. Consider triggering guide 3 automatically."

---

#### Guides Tab (Reorderable)
Table. Columns: Drag handle (grip icon) | Order number (filled circle) | Guide (poster + title + steps) | Steps | Impressions | Completion (bar) | Status in list (Healthy / Leaks step N) | ↑↓ buttons + remove

- ↑/↓ buttons reorder. First item's ↑ and last item's ↓ are disabled.
- Remove button: red trash icon, hover-reveal.
- "Save order" primary button in sticky footer.
- "Add guide" accent button in header.

---

#### Embed Tab — 3 cards

**Card 1: Pixel** — same as Guide Detail pixel card. Note: "Already installed? Checklists use the same pixel as your guides."

**Card 2: Checklist Widget**
- Style picker: Floating panel / Inline embed
- Position picker (floating only): 2×2 grid (top-left / top-right / bottom-left / bottom-right)
- Live widget preview: dotted canvas bg, widget positioned per selection. Widget shows: dark header (checklist name + "N of M complete" + % circle), guide list (completed = strikethrough + grey, next = "Next" chip in accent), progress bar footer.
- Code block: auto-injected `<div data-reveal-checklist="...">` tag.

**Card 3: Dynamic Link** — same pattern as Guide Detail.

---

#### Behavior Tab
Same form pattern as Guide Detail. Additional fields:

| Field | Options |
|---|---|
| Guide order | Sequential (must follow order) / Free (any guide first) |
| On full completion | Show celebration screen / Quietly dismiss / Redirect to URL |
| Frequency cap | Once only / Per session / Unlimited |
| Feedback after each guide | Toggle — show face rating after each guide completes |

---

### 6. Settings (`Settings.html`)

**Purpose:** Workspace, team, account, integrations, API, extension, and billing management.

#### Layout
```
Page header ("Settings" + description)
Left inner nav (196px) | Main content (flex-1)
```

Inner nav items: Workspace | Team | Account | Notifications | Integrations | API & Webhooks | Extension | Billing + Danger zone (red, bottom)

Active item: white bg, border, shadow.

---

#### Workspace Section
Fields (label | hint | control):
- Workspace name → text input
- Logo → avatar preview + "Upload logo" button + format hint
- Workspace URL → prefix "reveal.software/" + slug input
- Site domain → text input + "Verified" green chip

Save button bottom-right.

---

#### Team Section
**Invite card:** email input + role segmented control (Admin / Editor / Viewer) + "Invite" accent button. Role description below.

**Members table:** per-member row: avatar + name (+ "You" chip if self) + email + join date | inline role switcher (same segmented control, disabled for self) | remove button (trash, hidden for self).

Roles:
- **Admin** — manage workspace + billing.
- **Editor** — create + publish guides.
- **Viewer** — browse only (analytics + guides). No editing, no commenting on feedback.

---

#### Account Section
**Profile card:** avatar + "Change photo" | name input | email input + "Verified" chip. Save button.

**Password card:** current password | new password | confirm. Update button.

**2FA card:** toggle switch + status chip ("Active" green when on).

---

#### Notifications Section
**Email card:**
- Digest frequency: Off / Daily / Weekly / Monthly (segmented)
- Toggles: Drop-off spike alert | New feedback | New user started | Guide published

**Slack card:** Slack logo + connection status chip + channel name (when connected) | Connect/Disconnect button.

---

#### Integrations Section
List card. Per integration: logo square (brand colour) + name + connection chip + description | Connect / Disconnect button + `…` settings button (when connected).

Integrations: Segment | Mixpanel | Amplitude | HubSpot | Intercom

---

#### API & Webhooks Section
**API Keys card:** live key (masked `sk_live_8f4e…a1c0`) + Copy + Rotate buttons. "New API key" button.

**Webhooks card:** per-webhook row: toggle switch + endpoint URL + event name + delete. Add form: URL input + event type picker (multi-option) + "Add" button.

Events: `guide.started` | `guide.completed` | `step.skipped` | `guide.rated`

---

#### Extension Section
**Connected extension card:** extension icon + name + "Connected" chip + version + last seen + device | "Open editor" accent button + "Disconnect" button.

**Permission tiles (×2):**
- **Camera** — required for the founder's video bubble.
- **Microphone** — required for audio. Reveal records audio through a *dedicated* microphone (not through the camera stream), so the creator can pick a specific mic — e.g. a podcast mic or external interface — and the extension respects that selection.

**Install card:** description + "Install extension" accent button (for new installs).

---

#### Billing Section
**Plan card:** plan name + "Current plan" chip | description (price, seats, impressions) | billing date | "Upgrade" button. Impressions usage bar (% used, resets on billing date).

**Payment card:** card brand logo + last 4 + expiry | "Update" button.

**Invoices card:** per-invoice row: date + "Paid" chip + amount + "Download" button.

---

#### Danger Zone
Styled separately: red-tinted border + light red bg. Rows: Transfer workspace | Export all data | Delete all guides (red button) | Delete workspace (red button).

---

## Data Models (implied by UI)

### Guide
```typescript
interface Guide {
  id: string
  title: string
  description?: string
  status: 'published' | 'draft' | 'archived'
  personalIntro?: PersonalIntro    // optional intro recorded before steps
  steps: Step[]
  audience: string
  author: User
  workspaceId: string
  embedConfig: EmbedConfig
  behaviorConfig: BehaviorConfig
  analytics: GuideAnalytics
  createdAt: Date
  updatedAt: Date
}

interface PersonalIntro {
  videoUrl: string        // recorded large central bubble
  duration: number        // seconds
  enabled: boolean        // creator can toggle without deleting
}

interface Step {
  id: string
  guideId: string
  order: number
  title: string
  elementSelector: string  // CSS selector for tooltip target
  videoUrl: string         // every step has a video — recorded or uploaded
  videoSource: 'recorded' | 'uploaded'  // tracks per-step replacements
  duration: number         // seconds
}

interface GuideAnalytics {
  impressions: number
  starts: number
  completionRate: number   // 0-1
  avgWatchTime: number     // seconds
  avgRating: number        // 0-5
  stepFunnel: StepFunnelData[]
  ratingBreakdown: RatingBreakdown
}

interface StepFunnelData {
  stepId: string
  order: number
  reachRate: number  // % of starters who reached this step
  dropRate: number   // % who dropped at this step
}
```

### Checklist
```typescript
interface Checklist {
  id: string
  title: string
  description?: string
  status: 'published' | 'draft'
  guideIds: string[]       // ordered
  audience: string
  author: User
  workspaceId: string
  embedConfig: ChecklistEmbedConfig
  behaviorConfig: ChecklistBehaviorConfig
  analytics: ChecklistAnalytics
  createdAt: Date
  updatedAt: Date
}

interface ChecklistAnalytics {
  starts: number
  fullCompletionRate: number
  avgTimeToFinish: number    // seconds
  avgGuidesFinished: number
  avgRating: number
  perGuideFunnel: PerGuideData[]
}
```

### EmbedConfig (Guide)
```typescript
interface EmbedConfig {
  sitewide: boolean          // pixel active across all pages
  revealButton: {
    enabled: boolean
    style: 'solid' | 'accent' | 'outline'
    label: string
  }
  dynamicLink: string        // generated URL param
}
```

### BehaviorConfig (Guide)
```typescript
interface BehaviorConfig {
  trigger: 'auto' | 'delay' | 'manual' | 'element'  // 'element' is TBD
  triggerDelay?: number      // seconds (when trigger = 'delay')
  triggerSelector?: string   // CSS selector (when trigger = 'element') — TBD
  audience: 'new' | 'returning' | 'all' | 'segment' // 'segment' is TBD
  segmentRules?: object      // custom segment filter — TBD
  onSkip: 'return' | 'dismiss' | 'never'
  maxSkips: number           // 1-10
  frequencyCap: 'once' | 'daily' | 'weekly' | 'unlimited'
  showFeedback: boolean
}
```

### ChecklistBehaviorConfig
Extends BehaviorConfig with:
```typescript
  guideOrder: 'sequential' | 'free'
  onComplete: 'celebrate' | 'dismiss' | 'redirect'
  redirectUrl?: string
```

### Workspace
```typescript
interface Workspace {
  id: string
  name: string
  slug: string              // workspace URL: reveal.software/{slug}
  logoUrl?: string
  domain: string             // pixel target domain
  pixelKey: string           // unique install key
  plan: 'free' | 'pro' | 'scale' | 'enterprise'
  members: WorkspaceMember[]
  apiKeys: ApiKey[]
  webhooks: Webhook[]
  integrations: Integration[]
}

interface WorkspaceMember {
  userId: string
  role: 'admin' | 'editor' | 'viewer'
  joinedAt: Date
}
```

### Analytics Events (for webhooks + integrations)
```
guide.started      { guideId, userId, sessionId }
guide.completed    { guideId, userId, sessionId, timeElapsed }
guide.skipped      { guideId, userId, stepId, skipCount }
step.reached       { guideId, stepId, userId }
step.skipped       { guideId, stepId, userId }
guide.rated        { guideId, userId, rating: 'sad'|'neutral'|'happy'|'love', comment? }
checklist.started  { checklistId, userId }
checklist.completed { checklistId, userId, timeElapsed }
```

---

## Pixel / Embed System

The Reveal pixel is a single `<script>` tag installed in the customer's site `<head>`. **Without it, end users cannot experience guides at all** (button or dynamic link triggers still require the pixel to be present, because the guide payload lives inside it).

What it does:
1. Identifies the current user via `reveal.identify(userId, traits)` *(TBD — Segment-style API)*
2. Checks if any published guide/checklist targets this user (audience rules)
3. Injects the guide tooltip layer or checklist widget into the DOM
4. Tracks all analytics events and posts them to the Reveal API
5. Respects skip counts, frequency caps, and completion state stored in `localStorage` + server-side

The Chrome extension uses DOM permissions to detect whether the pixel is installed on the customer's domain and reports this back to the dashboard.

**Future direction (not v1):** A user with the Reveal Chrome extension installed can experience a guide on a site that doesn't have the pixel. This unlocks community-recorded tutorials — users teaching each other on apps they don't own the code to.

---

## Chrome Extension Integration

The extension is a separate product (already built). It is where guides are *authored* — recording, re-recording, retargeting, trimming, per-step video upload. The dashboard inspects; the extension authors.

The dashboard communicates with it via:
- Deep-link URLs: `reveal://guide/{guideId}/step/{stepN}` — opens extension editor at the correct step
- Extension detects pixel on customer domains and sends heartbeat to API
- "Open in Editor" buttons in the dashboard trigger these deep-links

---

## Key Interactions

| Interaction | Behaviour |
|---|---|
| New guide CTA | Deep-links to Chrome extension to start recording |
| Personal Intro toggle | Set in extension before recording — defaults to on |
| Speak then Click | Click on a UI element ends recording for that step |
| Per-step "Upload video" | Replace a step's clip with a polished pre-recorded video; element selector preserved |
| Guide card click (Guides page) | Navigate to Guide Detail page |
| Checklist card click | Navigate to Checklist Detail page |
| "Open in Editor" | Fire `reveal://guide/{id}` deep-link |
| Status tab filter | Client-side filter, no network request |
| Performance filter | Client-side filter |
| Bulk select | Hover reveals checkbox; floating action bar appears when ≥1 selected |
| Guide reorder (Checklist) | ↑/↓ buttons update local order; "Save order" POSTs to API |
| Toggle switch (embed, notifications) | Optimistic update → PATCH API |
| Pixel detection status | Polled from extension heartbeat via API; displayed as live chip |

---

## Files in This Package
- `Home.html` — Dashboard home page
- `Guides.html` — Guide list (gallery + list views)
- `Guide Detail.html` — Single guide detail (analytics, steps, embed, behavior)
- `Checklist.html` — Checklist list page + creation modal
- `Checklist Detail.html` — Single checklist detail (overview, guides, embed, behavior)
- `Settings.html` — All settings sections

---

## Suggested Development Milestones *(work in progress — do not size against; engineering owns sizing)*

These are listed in dependency order, not duration order. Each milestone is meant to produce something demo-able.

### Milestone 1 — Foundation
- Project setup: Next.js App Router + Shadcn/ui + Tailwind + TypeScript
- Auth: NextAuth or Clerk (email + Google)
- Database schema: Workspace, User, Guide, Step, Checklist (Postgres via Prisma)
- Sidebar layout shell with navigation
- Workspace switcher (basic, no invite yet)

### Milestone 2 — Guide CRUD
- Guides list page (gallery view, status tabs, search)
- Guide detail page shell (hero + tabs)
- Guide creation (title, description, audience — extension handles recording)
- Step list (read-only table, element selector display)
- Per-step "Upload video" hook (replaces a step's clip)
- Publish / unpublish / archive actions
- Draft vs. published visual distinction

### Milestone 3 — Pixel & Embed
- Pixel script generation (unique key per workspace)
- Embed code generation (Reveal Button, dynamic link)
- Pixel detection endpoint (extension heartbeat)
- Pixel status display in dashboard (detected / not detected)
- Basic guide widget (renders tooltip layer on customer site)

### Milestone 4 — Analytics
- Analytics event ingestion API (guide.started, step.reached, guide.completed, guide.rated)
- Step funnel query + display
- KPI aggregation (impressions, starts, completion rate, avg rating)
- Guide detail Overview tab (Layout 1 — Classic)
- Home page KPIs + top/worst performer cards

### Milestone 5 — Checklists
- Checklist CRUD (create, add guides, reorder, publish)
- Checklist detail page (overview funnel, guide reorder table)
- Checklist widget (floating to-do panel on customer site)
- Checklist analytics (full completion rate, per-guide funnel)

### Milestone 6 — Behavior & Targeting
- BehaviorConfig persistence (trigger, audience, skip rules, frequency cap)
- Pixel respects behavior config at runtime
- Audience matching (new / returning / all)
- Skip counting + suppression logic (localStorage + server)
- *Deferred (TBD):* "On element click" trigger, "Custom segment" audience, `reveal.identify()` ingestion

### Milestone 7 — Team & Settings
- Workspace settings (name, logo, domain — under reveal.software)
- Team invitations (email invite → join flow)
- Role-based access control (admin / editor / viewer)
- Account settings (profile, password, 2FA)
- Notification preferences (email digest, Slack webhook)

### Milestone 8 — Integrations & API
- API key management (generate, rotate, revoke)
- Webhook delivery (guide events → customer endpoints)
- Segment integration (identify passthrough)
- Billing (Stripe — plan, usage metering, invoices)

### Milestone 9 — Polish & Launch
- Remaining Overview tab layouts (Funnel-first, Two-column, Journey curve)
- Empty states (new workspace, no guides, no analytics)
- Responsive sidebar (collapsible on smaller screens)
- Error states + loading skeletons
- E2E tests (Playwright) for critical paths
- Performance (ISR for analytics pages, edge caching for pixel script)
