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

## Workflow

```
/feature-planner  →  plan the feature (idea → Notion spec)
/frontend-design  →  generate the UI (spec → working code)
/landing-page     →  research + generate a complete landing page from social/web data
```
