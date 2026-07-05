# seo-geo

Audit and fix discoverability for search engines, AI systems, and MCP directory crawlers.

## Usage

```
/seo-geo
/seo-geo learn.html
```

Pass a specific file path to run against one page. Omit the argument to run against the entire codebase.

## Instructions

Read the argument as `$PAGE`. If provided, scope all tasks to that file only. If omitted, run against every public-facing page found in the codebase.

---

### TASK 1 — Audit every public page

Find all public-facing HTML pages and routes in the codebase. List each one with its URL path before doing anything else. Flag any pages where content is rendered via JavaScript rather than existing in static HTML — these are invisible to crawlers and AI systems and need to be fixed.

If `$PAGE` is set, confirm whether it exists and flag immediately if its content is JS-rendered.

---

### TASK 2 — Create a shared `<head>` template

Create a reusable head partial or component (depending on the stack) that all pages will include. It must contain:

**Primary SEO:**
- `<title>` unique per page, under 60 chars, includes primary keyword
- `<meta name="description">` 120–160 chars, specific, factually precise, leads with what it does not what it is
- `<meta name="keywords">` 5–10 relevant terms
- `<link rel="canonical">` pointing to the correct full `https` URL for each page

**Open Graph:**
- `og:title`
- `og:description`
- `og:url` (matches canonical)
- `og:type`
- `og:site_name`

**Twitter/X card:**
- `twitter:card: summary_large_image`
- `twitter:title`
- `twitter:description`

Write descriptions that are factually precise — AI systems use the meta description as their primary source for citations. Lead with what the product does, not what it is.

If `$PAGE` is set, apply the head template to that page only.

---

### TASK 3 — Add JSON-LD structured data to every page

Place a JSON-LD block before `</body>` on every page (or `$PAGE` if set). Use the correct schema type per page:

- Tool / MCP / app pages → `SoftwareApplication`
- Learn / guide pages → `TechArticle`
- Marketing / landing pages → `WebPage`
- Organization / about pages → `Organization`

Fill all fields. No placeholders.

---

### TASK 4 — Fix JS-rendered content on all flagged pages

For every page flagged in Task 1 where content is rendered via JavaScript:
- Move critical content into static HTML so it exists in the raw page source
- This includes product descriptions, feature lists, how-to steps, and any MCP or API instructions
- The content must be readable by a `fetch()` call with no JS execution
- Do not change the visual output — only ensure the content exists statically
- Write key facts as standalone sentences, not marketing copy buried in JS

If `$PAGE` is set, scope this fix to that file only.

---

### TASK 5 — Add the `.well-known/mcp/server-card.json` endpoint

Only run this task if `$PAGE` or `$ARGUMENTS` references an MCP server, or if an MCP server is found during the codebase audit.

Create a static file at `/.well-known/mcp/server-card.json` containing:
- `name`, `description`, `url`, transport type, auth method
- `tools` array with `name` and `description` for each tool the MCP exposes
- `homepage`, category tags, license

Ensure it is served as `Content-Type: application/json` with no auth required.

---

### TASK 6 — Add a sitemap and robots.txt

Create `/sitemap.xml` listing all public pages with canonical URLs and `lastmod` dates.

Create or update `/robots.txt` to:
- Allow all crawlers
- Reference the sitemap
- Explicitly allow AI crawlers:

```
User-agent: GPTBot
Allow: /

User-agent: ClaudeBot
Allow: /

User-agent: anthropic-ai
Allow: /
```

If `$PAGE` is set, ensure the sitemap includes that page and skip creating new entries for others.

---

### TASK 7 — Create a reusable page template for future pages

Create a template file appropriate for the project stack that every future page must start from. It must include:
- The shared `<head>` from Task 2 with placeholder comments marking what to change per page
- The JSON-LD block with placeholder comments
- Semantic HTML structure: `<main>`, `<article>`, `<section>` with `id` attributes
- Analytics script placeholder before `</body>`
- A comment at the top:

```html
<!-- SEO/GEO template — fill all placeholders before shipping.
     No page goes live without a unique title, description, canonical URL, and JSON-LD. -->
```

Skip this task if `$PAGE` is set to a single file.

---

### TASK 8 — Add analytics event tracking

On every page (or `$PAGE` if set) add tracking for:
- CTA clicks (any button linking to a key conversion action)
- Scroll depth — when the user reaches the primary value section or install steps
- Use Vercel Web Analytics if the project is hosted on Vercel:

```html
<script defer src="/_vercel/insights/script.js"></script>
```

- Otherwise use Google Analytics 4 with the project's measurement ID.

---

### TASK 9 — Internal linking audit

Check that:
- Every page links back to the homepage
- No orphan pages exist (pages with no inbound links from other pages on the site)
- Hub pages link to all child pages
- Child pages link back to the hub

Fix any gaps found. If `$PAGE` is set, check inbound and outbound links for that page only.

---

### TASK 10 — Final verification checklist

Run through this checklist for every page (or `$PAGE` if set) and report results. Flag anything that needs manual input.

**SEO:**
- [ ] `<title>` is unique, under 60 chars, includes primary keyword
- [ ] `<meta name="description">` is 120–160 chars, specific, no placeholders
- [ ] `<link rel="canonical">` points to the correct URL
- [ ] One `<h1>` on the page
- [ ] Headings are hierarchical (h1 → h2 → h3)
- [ ] Images have `alt` attributes

**Open Graph:**
- [ ] `og:title` set
- [ ] `og:description` set
- [ ] `og:url` matches canonical
- [ ] `og:type` set

**Structured data:**
- [ ] JSON-LD present before `</body>`
- [ ] Schema type matches page type
- [ ] All fields filled, no placeholders
- [ ] URL matches canonical

**Crawler access:**
- [ ] `/sitemap.xml` is valid and lists all public pages
- [ ] `/robots.txt` allows all crawlers including GPTBot, ClaudeBot, anthropic-ai
- [ ] `/.well-known/mcp/server-card.json` returns valid JSON (if project has an MCP)

**GEO:**
- [ ] Key facts written as standalone sentences
- [ ] Meta descriptions are factually precise
- [ ] JS-rendered content moved to static HTML
- [ ] Every page links back to root

Report every checkbox result and flag anything incomplete.
