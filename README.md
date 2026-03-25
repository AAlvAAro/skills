# Skills

Custom Claude Code skills.

## Available Skills

### frontend-design

Create distinctive, production-grade frontend interfaces with high design quality. Auto-detects project context:

- **Rails + Inertia mode** — generates pages, controllers, routes directly into a Rails + Vite + Inertia.js project
- **Standalone React mode** — scaffolds Vite + React SPAs deployable to Vercel/Netlify/Amplify
- **Plain HTML mode** — single-file static pages

```bash
npx skills add AAlvAAro/skills@frontend-design
```

### feature-planner

Interactive feature planning assistant that goes from idea to implementation-ready spec:

- Pulls a spec template from Notion and walks you through each section
- Supports **new projects**, **new features for existing apps**, and **redesigns**
- Generates TypeScript types, page routes, component lists, and acceptance criteria
- Creates a completed Notion page ready to paste into Claude Code
- Includes brand asset planning (logos, favicons, OG images)

```bash
npx skills add AAlvAAro/skills@feature-planner
```

### content-media-plan

Research-driven content calendar and media plan generator:

- **Document-first** — reads a brief or strategy doc directly if you have one, or guides you through 3 rounds of focused questions
- **Reference research** — analyzes competitor/inspiration social accounts (Instagram, TikTok, Facebook, YouTube) to extract real content patterns
- **Strategy definition** — proposes content pillars, platform mix, and posting frequency based on your goals and capacity
- **Flexible calendar** — 1, 2, or 4-week day-by-day schedule with platform, format, pillar, and topic for each post
- **Format examples** — fully written example copy + AI-generated sample images for every format type (Feed Post, Carousel, Reels, Stories, Tweet, FB Post, Blog, Newsletter)
- **Visual HTML output** — color-coded calendar grid + format gallery, opened live in the browser
- **Flexible storage** — save to Notion and/or export a local bundle for Google Drive

```bash
npx skills add AAlvAAro/skills@content-media-plan
```

### landing-page

Research-driven landing page generator that builds pages from real data:

- **Research phase** — pulls from Instagram, TikTok, Facebook, YouTube, Google Maps, website scraping, forums, news, and optionally a CV
- **Analysis phase** — extracts competitive advantages, value props, audience insights, brand voice, and SEO keywords like a marketer
- **Competitor fallback** — when data is sparse, studies similar businesses to inform professional design
- **Image generation** — creates AI-generated visuals matched to the brand aesthetic (via `infsh` CLI)
- **Map integration** — embeds Google Maps / OpenStreetMap with "Get Directions" from GPS coordinates
- **Multiple output formats** — pure HTML (beginners), Vite+React (developers), Rails+Inertia (SaaS), or client handoff bundle
- Uses **Social Toolkit MCP** and **SearchAPI MCP** for data gathering

```bash
npx skills add AAlvAAro/skills@landing-page
```

### presentation

Research-driven presentation generator that creates shareable HTML slide decks:

- **Flexible input** — starts from a document, URL, file, pasted text, image, or guided questions
- **Multi-source research** — social media, news, finance, trends, ads, YouTube, app stores, Google Maps reviews, web scraping via Social Toolkit and SearchAPI MCPs
- **Presentation types** — pitch deck, sales/investment, marketing, business model canvas, product launch, portfolio, educational, market analysis
- **Visual styles** — minimalist executive, bold & dynamic, editorial, data-driven, creative/storytelling
- **Interactive HTML** — keyboard navigation (arrows, fullscreen, overview grid), touch/swipe, progress bar, speaker notes, direct slide linking via URL hash
- **Instant sharing** — deploys to Cloudflare Workers via wrangler for a shareable URL

```bash
npx skills add AAlvAAro/skills@presentation
```

## Workflow

```
/feature-planner     →  plan the feature (idea → Notion spec)
/frontend-design     →  generate the UI (spec → working code)
/content-media-plan  →  research + generate a full content calendar with format examples and visual deliverable
/landing-page        →  research + generate a complete landing page from social/web data
/presentation        →  research + generate an interactive HTML slide deck deployable via URL
```
