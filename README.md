# Claude Skills

A collection of reusable slash commands for Claude Code. Drop any skill into a project's `.claude/commands/` folder to make it available as `/skill-name` in that project, or copy to `~/.claude/commands/` to make it global across all projects.

---

## `/scaffold-project`

Creates a full documentation structure for any project, pre-filled with real content from the codebase and your answers. The goal is to give Claude a source of truth before it generates a single line of code.

### Usage

```
/scaffold-project <project-name>
```

### The 4 steps

**Step 1 — Reads the codebase**

Scans the project automatically and extracts:

- SEO/GEO metadata (`<title>`, description, OG tags, schema, geo tags)
- Analytics setup (GA4 measurement ID, PostHog key and host)
- Design tokens (CSS custom properties from `styles.css`)
- Components, props, and states (from component files)
- User actions and flows (from event listeners and API calls in `script.js`)
- Routes, redirects, and external services (from `vercel.json`, `sitemap.xml`, `robots.txt`)

**Step 2 — Asks you the questions code can't answer**

In a single message, asks only what it couldn't find:

- What problem does this solve and who has it?
- What does success look like for the user?
- What's explicitly out of scope?
- What 2–3 UX principles should govern every decision?
- Are any features gated, whitelisted, or Pro-only?
- Stack, analytics, and auth — only if not already found in files

Waits for your answers before creating anything.

**Step 3 — Creates 7 files** (Thanks uxplanet.org)

```
/project-name
  CLAUDE.md                        ← index that points Claude to every doc
  /docs
    prd.md                         ← problem, goal, scope, requirements
    user-flows.md                  ← step-by-step user journeys from code
    ux-principles.md               ← design decisions and the WHY behind them
    design-system.md               ← tokens, components, SEO, analytics
  /design
    tokens.md                      ← color, spacing, type values
    components.md                  ← component inventory and variant rules
    interaction-patterns.md        ← hover, focus, loading, error, animation rules
```

**Step 4 — Prints a summary**

Table of every file created, with ⚠️ on anything that still needs attention.

### Why it exists

Claude starts every session cold. Without context, it guesses — wrong tokens, wrong patterns, wrong scope. These docs give it the full picture upfront so the first thing it generates is already correct.

### How to install and use

**Step 1 — Open your project in Claude Code**

Any existing project or a new empty folder works.

**Step 2 — Run this in your terminal**

```bash
mkdir -p ~/.claude/commands && curl -o ~/.claude/commands/scaffold-project.md \
  https://raw.githubusercontent.com/leobyday/Claude-Design-Scaffolding/main/scaffold-project.md
```

This installs the skill globally — you only need to do this once. It will be available in every project from that point on.

**Step 3 — Type this in Claude Code**

```
/scaffold-project your-project-name
```

Claude will scan your files, ask you a few questions, then generate the full doc structure.
