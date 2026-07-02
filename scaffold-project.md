# scaffold-project

Creates the standard project folder structure with documentation files **pre-filled from the existing codebase**. Run this before writing any code — these docs become the source of truth Claude reads before generating anything.

## Usage

```
/scaffold-project <project-name>
```

## Instructions

Read the argument as `$PROJECT_NAME`. If none provided, ask before proceeding.

### Step 1 — Read the codebase first

Before creating any files, scan the existing project to extract real content. Read the following in order:

1. **`index.html`** — extract:
   - `<title>` tag
   - `<meta name="description">` content
   - `<meta name="keywords">` content
   - `<meta name="robots">` content
   - GEO tags: `geo.region`, `geo.placename`, `geo.position`, `ICBM`
   - Open Graph tags: `og:title`, `og:description`, `og:url`, `og:image`, `og:type`
   - Twitter Card tags: `twitter:card`, `twitter:image`, `twitter:title`, `twitter:description`
   - JSON-LD schema blocks (`<script type="application/ld+json">`) — extract `@type`, `name`, `description`, `url`
   - `<link rel="canonical">` if present
   - Google Analytics: `gtag.js` script src, GA measurement ID (e.g. `G-XXXXXXXXXX`)
   - PostHog: `posthog.init(...)` — extract project API key and `api_host`
   - Any other analytics or tracking scripts
   - Nav links, main UI sections, feature flags, auth checks

2. **`script.js`** (or main JS file) — extract: user-triggered actions (clicks, submits, spins), state transitions, API calls, error handling flows, any `posthog.capture()` or `gtag()` calls

3. **`styles.css`** — extract: CSS custom properties (design tokens), font families, spacing values, border radii

4. **`vercel.json`** — extract: routes, redirects, CSP domains (these reveal which external services are in use)

5. **`sitemap.xml`** — extract: all URLs, priorities, change frequencies

6. **`robots.txt`** — extract: allowed/disallowed crawlers and paths

7. **`CLAUDE.md`** — extract: any existing architecture notes, known constraints, approved patterns

8. **Any component files** in `dashboard-app/src/components/` — extract: component names, props, states, button variants in use

9. **`api/`** folder if it exists — extract: endpoints, parameters, response shapes

Use what you find to populate the doc files with **real content** rather than placeholder comments. Where you genuinely cannot infer the answer, leave the placeholder comment so the user knows what to fill in manually.

### Step 2 — Ask before writing

After scanning, identify every section that cannot be filled from code alone. Then ask the owner the questions below **in a single message** — list only the ones that are still unknown after Step 1. Do not create any files yet.

Format the questions as a numbered list. Wait for the user to answer all of them before proceeding.

**Always ask:**

1. **Problem** — In one or two sentences, what problem does this solve and who has it? (This is the human "why" — code can't tell us this.)
2. **Goal** — One sentence: what does success look like for the user when this is done?
3. **Out of scope** — What are you explicitly NOT building in this version? List anything that might seem obvious but won't be included.
4. **UX principles** — What are 2–3 short rules that should govern every design decision here? Think: what would you always do, and what would you never do?
5. **Feature gating** — Are any features hidden, whitelisted, or Pro-only? If so, which ones and why?

**Ask only if not found in code:**

6. **Stack** — What framework and styling approach is this using? (Ask only if not clear from package.json or existing files.)
7. **Analytics** — Are GA4 and/or PostHog already set up? Should new user actions be tracked? (Ask only if no analytics tags found in index.html.)
8. **Auth** — Is there a login/auth requirement? Who can access this? (Ask only if no auth checks found in code.)

After the user answers, proceed to Step 3.

### Step 3 — Create the files

Create all files using the templates below, substituting real content from Step 1 (codebase scan) and Step 2 (owner answers). Every section should have real content — no unanswered placeholders.

### Step 4 — Confirm what was created

Print a single summary table:

| File | Status | Notes |
|---|---|---|
| CLAUDE.md | ✅ Created | |
| docs/prd.md | ✅ Created | |
| ... | | |

Flag any section that still needs attention with ⚠️ and explain why.

---

## Files to create

### `$PROJECT_NAME/CLAUDE.md`

```markdown
# $PROJECT_NAME

## Before you generate anything, read:
- [docs/prd.md](docs/prd.md) — what we're building and why
- [docs/user-flows.md](docs/user-flows.md) — how users move through it
- [docs/ux-principles.md](docs/ux-principles.md) — design decisions and constraints
- [docs/design-system.md](docs/design-system.md) — which tokens and components to use
- [design/tokens.md](design/tokens.md) — exact color, spacing, and type values
- [design/components.md](design/components.md) — component inventory and variant rules
- [design/interaction-patterns.md](design/interaction-patterns.md) — hover, focus, animation rules

## Stack
<!-- Fill in: e.g. Vanilla JS + CSS, React 18 + Vite + shadcn/ui -->

## Key files
<!-- Fill in once source files exist -->

## Never
<!-- Fill in: things Claude should never do in this project -->
```

---

### `$PROJECT_NAME/docs/prd.md`

```markdown
# Product Requirements — $PROJECT_NAME

> **What this file is:** The single source of truth for what we're building and why.
> Sections marked ⚠️ were not inferrable from the codebase — fill these in manually.

## Problem
<!-- ⚠️ Infer from context if possible, otherwise leave for user -->

## Goal
<!-- ⚠️ One sentence: what does success look like for the user? -->

## Scope

### In scope
<!-- Fill from: existing routes, UI sections, and features found in index.html / script.js -->
-

### Out of scope
<!-- ⚠️ Leave for user -->
-

## Requirements

### Functional
<!-- Fill from: every user-triggered action found in script.js (clicks, submits, API calls) -->
| # | Requirement | Priority |
|---|---|---|
| F1 | | Must |

### Non-functional
<!-- Fill from: CSP rules in vercel.json, auth checks, analytics calls -->
| # | Requirement |
|---|---|
| N1 | |

## Open questions
<!-- ⚠️ Leave for user -->
-
```

---

### `$PROJECT_NAME/docs/user-flows.md`

```markdown
# User Flows — $PROJECT_NAME

> **What this file is:** Step-by-step walkthroughs of how a user accomplishes their goal.
> Flows marked ✅ were inferred from the codebase. Flows marked ⚠️ need manual review.
> One flow per distinct user task. Name the exact UI element the user touches.

<!-- 
  HOW TO FILL THIS IN FROM CODE:
  - Each event listener / onClick in script.js = a user action = a step in a flow
  - Each if/else or try/catch = a branch = a happy path vs error state
  - Each fetch() or supabase call = a loading state
  - Each auth check = a gate that defines who can access this flow
-->

## Flow 1: [Infer name from first major user action in script.js]

**Entry point:** <!-- Which page/URL? Which element is visible on arrival? -->
**Goal:** <!-- What is the user trying to accomplish? -->
**Auth required:** <!-- Yes / No / Pro only -->

### Happy path
1. <!-- Step inferred from code -->
2.
3.

**End state:** <!-- What confirms success? Toast? Modal? URL change? -->

### Edge cases
- **Empty state:** <!-- What renders when the data array is empty? -->
- **Error state:** <!-- What renders inside the catch block? -->
- **Loading state:** <!-- Spinner? Skeleton? Disabled button? -->

---

## Flow 2: [Next major user action]

<!-- Repeat pattern above. Add as many flows as there are distinct user tasks. -->
```

---

### `$PROJECT_NAME/docs/ux-principles.md`

```markdown
# UX Principles — $PROJECT_NAME

> **What this file is:** The design decisions and constraints that govern this project.
> Write the WHY, not just the WHAT. Future decisions should reference these.
> Claude reads this to avoid generating UI that violates the intent of the design.

## Principles
<!-- 3–5 short statements. Each one should rule something IN and something OUT. -->
1.
2.
3.

## Decisions log
<!-- Every time you make a non-obvious design call, record it here with the reason. -->

| Decision | Why | Date |
|---|---|---|
| | | |

## Constraints
<!-- What limits our design options? Brand, accessibility, technical, legal. -->
-
```

---

### `$PROJECT_NAME/docs/design-system.md`

```markdown
# Design System — $PROJECT_NAME

> **What this file is:** The bridge between the global design system and this project.
> List every token and component this project actually uses. Claude reads this to know
> which values to reach for without having to scan the whole codebase.

## Color tokens
| Token | Value | Used for |
|---|---|---|
| `--bg` | `#0f1623` | Page background |
| `--surface` | `#161d29` | Cards, panels |
| `--surface-2` | `#1c2535` | Hover states, inner panels |
| `--surface-3` | `#232e42` | Action bars, footers |
| `--text-1` | `#f0f4f8` | Primary text |
| `--text-2` | `rgba(255,255,255,0.55)` | Secondary / meta text |
| `--text-3` | `rgba(255,255,255,0.3)` | Placeholder, disabled text |
| `--navy` | `#294473` | Primary buttons, CTAs |
| `--blue` | `#5475ae` | Accent, links |
| `--border` | `rgba(255,255,255,0.07)` | Dividers, card outlines |

## Typography
| Use | Font | Size | Weight |
|---|---|---|---|
| Body / UI | Jost | 14px | 400 |
| Headings | Jost | 20–28px | 400–500 |
| Code / mono | Source Code Pro | 14px | 400 |

## Components used in this project
<!-- List each component and which variants are needed -->
| Component | Variants | Notes |
|---|---|---|
| Button | default, outline, ghost, destructive | |

## Spacing
`4 · 8 · 12 · 16 · 24 · 32 · 48px`

## Border radius
`10px` cards · `8px` inputs and nav items · `999px` pills and tags

## SEO & GEO metadata
<!-- Fill from: <title>, <meta>, og:*, twitter:*, JSON-LD in index.html -->
| Tag | Value |
|---|---|
| Title | |
| Meta description | |
| Canonical URL | |
| OG image | |
| Schema @type | |
| geo.region | |

## Analytics
<!-- Fill from: gtag.js and posthog.init() in index.html -->
| Service | ID / Key | Events tracked |
|---|---|---|
| GA4 | | |
| PostHog | | |
```

---

### `$PROJECT_NAME/design/tokens.md`

```markdown
# Design Tokens — $PROJECT_NAME

> **What this file is:** Project-specific token overrides or additions on top of the global system.
> Tokens marked ✅ were extracted from styles.css. Tokens marked ⚠️ need manual review.

## Status
<!-- Fill from: `:root` block in styles.css -->

## Color tokens in use
<!-- Fill from: every `var(--*)` reference found in the component files -->
| Name | Value | Used for |
|---|---|---|

## Overrides (differs from global)
| Name | Value | Replaces | Reason |
|---|---|---|---|

## New tokens (project-specific only)
| Name | Value | Used for |
|---|---|---|
```

---

### `$PROJECT_NAME/design/components.md`

```markdown
# Component Inventory — $PROJECT_NAME

> **What this file is:** A catalogue of every UI component in this project.
> For each one: where it comes from, which variants are in use, and any project-specific rules.
> Claude reads this to know which component to reach for and how to configure it.

## How to add an entry
Copy the block below for each new component.

---

## [ComponentName]

**Source:** `shadcn/ui` · `custom` · `Figma`
**File:** `src/components/...`
**Variants in use:** default · outline · ghost
**States:** Default · Hover · Focus · Disabled
**Notes:**
<!-- Any project-specific rules, overrides, or usage constraints -->
```

---

### `$PROJECT_NAME/design/interaction-patterns.md`

```markdown
# Interaction Patterns — $PROJECT_NAME

> **What this file is:** The motion, feedback, and state-change rules for this project.
> Claude reads this before adding hover effects, transitions, or loading states
> so that every interaction feels consistent — not whatever the component default happens to be.

## Hover
- Background: transitions to `--surface-2` over `150ms ease`
- Text/icon opacity: rests at `0.55`, rises to `1.0` on hover
- Cursor: `pointer` on all interactive elements

## Focus
- Ring: `3px solid hsl(var(--ring) / 0.5)`, offset `2px`
- Never remove focus outlines — only restyle them

## Transitions
- Color / opacity: `150ms ease`
- Position / size: `200ms ease`
- No transitions on page load or initial render

## Loading states
<!-- Spinner, skeleton, or disabled + opacity? Pick one per context and note it here -->
- Inline actions (buttons): spinner replaces icon, button disabled
- Full sections: skeleton placeholder at the same dimensions as the content

## Empty states
<!-- What does the UI show when a list or panel has no data? -->
- Centered text + muted icon
- No borders or card chrome — just the message

## Error states
<!-- Inline, toast, or modal? -->
- Field errors: inline red text below the input
- Action errors: toast (bottom-right, auto-dismiss 4s)
- Fatal errors: full-panel message with retry button

## Animation rules
- Motion is functional, not decorative
- No entrance animations on page load
- Reserve animation for state changes that need user attention
```
