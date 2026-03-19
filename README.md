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

## Workflow

```
/feature-planner  →  plan the feature (idea → Notion spec)
/frontend-design  →  generate the UI (spec → working code)
```
