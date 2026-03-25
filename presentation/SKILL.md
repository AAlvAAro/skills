---
name: presentation
description: "Research-driven presentation generator. Creates self-contained HTML slide decks with keyboard navigation, transitions, and responsive design. Supports single presentations or multi-material projects (pitch deck + business plan + financial report + more) with a shared research phase and a fancy index page linking all deliverables. Starts from a document, URL, file, pasted text, or image — or walks through guided questions and multi-source research (social media, news, finance, trends, ads, YouTube, app stores, Google Maps reviews, web scraping). Supports multiple presentation types (pitch deck, sales, marketing, business model canvas, portfolio, etc.) and visual styles (minimalist, bold, editorial, data-driven, creative). Generates AI images and deploys to Cloudflare Workers for instant sharing. Use this skill when the user says things like: 'create a presentation', 'build a slide deck', 'make a pitch deck', 'presentation about...', 'I need slides for...', 'help me prepare a presentation', 'investor pitch', 'sales presentation', 'workshop slides', 'product launch deck', 'business model canvas presentation', 'create slides from this document', 'create a project with multiple presentations', 'pitch deck and business plan', or provides a document/URL and wants presentation slides built from it."
---

# Presentation Generator

A research-driven skill that builds compelling HTML slide presentations by first understanding the subject deeply — through documents, web research, and multi-source data gathering — then crafting a visually impressive, interactive slide deck with keyboard navigation, smooth transitions, and professional design.

Supports two modes:
- **Single** — one slide deck (default)
- **Multi-material** — multiple deliverables (pitch deck, business plan, financial report, etc.) sharing one research phase, with a fancy index page linking all materials

This is NOT a generic slide template. It's a content-driven approach: understand the topic deeply, structure the narrative, then design slides that communicate with impact.

## Workflow Overview

```
Input (document, URL, file, pasted text, image, or guided questions)
  → Research Source Selection (multi-source data gathering)
    → Mode Selection (single or multi-material)
      ┌─ Single → Type & Style → Outline → Content → HTML → Deploy
      └─ Multi  → Select materials (multi-select) → Style (shared)
                   → Outline per material → Content per material
                     → HTML per material + Index Page → Deploy
```

---

## Step 0: Input Check

**Always ask this first, before anything else:**

> "Do you have a document, URL, Notion page, file, or content I should start from? You can share a file path, paste text directly, provide a URL to scrape, share an image, or point me to a Notion page. Otherwise, I'll walk you through it step by step."

**If YES (file path provided):**
- Read the file using the Read tool
- Extract: topic, thesis, key arguments, data points, audience signals, structure, and any visual identity cues
- Present a summary: *"Here's what I extracted from your document: [summary]. Anything to add or correct?"*
- Ask: *"Would you like me to research additional sources to enrich the presentation, or should I proceed directly to designing the slides?"*
- If additional research → jump to Step 2
- If no additional research → jump to Step 3 (Mode Selection)

**If YES (URL provided — non-Notion):**
- Use `FirecrawlScrapeTool` to scrape the URL content
- Extract the same information as above
- Same flow: summarize, confirm, ask about additional research

**If YES (Notion page — URL, page ID, or user mentions "Notion"):**

Detect Notion input when:
- The user provides a Notion URL (contains `notion.so` or `notion.site`)
- The user provides a Notion page ID
- The user mentions the word "Notion" in the context of a source (e.g., "I have it in Notion", "check my Notion page", "the info is on Notion")

If the user mentions Notion but doesn't provide a URL or page ID, ask:
> "What's the name of the Notion page? I'll search for it."

Then use `notion-search` to find it.

**Notion page fetching flow:**

1. **Fetch the parent page:**
   ```
   notion-fetch → page URL or page ID
   ```

2. **Discover child and linked pages:**
   - Scan the fetched content for internal page links and child page references
   - Build a page tree: parent → children → linked pages

3. **Recursively fetch all discovered pages:**
   ```
   notion-fetch → each child/linked page ID
   ```
   Limit to 2 levels deep (parent → children → grandchildren) to avoid runaway recursion. If deeper nesting is detected, inform the user and ask if they want to go deeper.

4. **Present the page tree:**
   > "I found the following pages in your Notion workspace:"
   >
   > - **[Parent Page Title]** (main page)
   >   - [Child Page 1 Title]
   >   - [Child Page 2 Title]
   >   - [Child Page 3 Title]
   > - **Linked pages:**
   >   - [Linked Page A Title]
   >   - [Linked Page B Title]

5. **Map pages to materials (if multi-material is likely):**

   **Auto-mapping:** If child page titles clearly match material types (e.g., a page titled "Financiero" or "Sales" or "Plan de Negocios"), suggest automatic mapping:
   > "Some of your Notion pages seem to match presentation types. Should I map them like this?"
   >
   > - "Financiero" → Reporte Financiero
   > - "Ventas Q1" → Reporte de Ventas
   > - "Modelo de Negocio" → Business Model Canvas
   >
   > "Or should I treat all pages as shared research for a single presentation?"

   **Manual mapping:** If titles don't clearly match, ask the user:
   > "How should I use these pages?"
   >
   > - **Shared research** — all pages feed into one research pool (single or multi-material)
   > - **One page per material** — each page becomes its own presentation
   > - **Let me assign** — I'll tell you which page goes where

6. **Extract content from all fetched pages:**
   - Combine all page content into a structured research corpus
   - Preserve which content came from which page (for attribution and material assignment)
   - Extract: topics, data points, key arguments, structure, visual identity cues, language

7. **Proceed:**
   - If pages were mapped to materials → skip to Step 3 (Mode Selection) with multi-material pre-selected and types pre-filled
   - If shared research → summarize findings, ask about additional research sources, then proceed normally

**If YES (pasted text or image):**
- Process the pasted content directly
- Same flow as file path: summarize, confirm, ask about additional research

**If NO:**
- Proceed to Step 1 (Guided Questions)

**Implicit detection:** If the user's opening message already contains substantial content about a topic, treat it as the document input — parse it directly and confirm what was extracted. If the message mentions "Notion" in the context of a source, trigger the Notion flow above.

---

## Step 1: Guided Topic Intake

Only run this step if no document was provided in Step 0. Use a maximum of 2 rounds of questions — batch related questions together, never ask one at a time.

### Round 1 — Core Topic

Ask all of these in a single message:
- What is the presentation about? (topic, thesis, or subject)
- Who is the audience? (investors, clients, team, students, conference attendees, general public)
- What is the goal? (inform, persuade, sell, educate, inspire, report)
- Approximate slide count? (5-10 quick pitch / 10-20 standard / 20-40 comprehensive)

### Round 2 — Key Content

Ask all of these in a single message:
- What are the 3-5 key points or messages you want to communicate?
- Do you have any specific data, statistics, or facts to include?
- Any competitor or industry context that should be referenced?
- Preferred language? (default: auto-detect from content)

After Round 2, confirm the extracted brief before proceeding:
> "Here's what I have: [brief summary of topic, audience, goal, key points]. I'll now ask you about research sources. Ready?"

---

## Step 2: Research Source Selection

Present a checklist of available sources and let the user pick which ones to search. If the user came from a document and declined additional research, skip this step entirely.

**Ask this question:**

> "Which sources should I research to enrich the presentation? Select all that apply:"
>
> - [ ] **Social Media** — Instagram, TikTok, Facebook, YouTube profiles
> - [ ] **News** — recent press, industry headlines
> - [ ] **Finance** — financial data, market reports, earnings
> - [ ] **Trends** — trending topics and search queries
> - [ ] **SERP** — general web research and synthesis
> - [ ] **Ads** — Meta Ad Library, TikTok Ads, Google Ads Transparency
> - [ ] **YouTube** — video transcripts, channel analysis, audience comments
> - [ ] **App Stores** — app reviews, ratings, feature comparisons
> - [ ] **Google Maps Reviews** — business reviews, ratings, location data
> - [ ] **Perplexity Sonar** — AI-synthesized research with citations
> - [ ] **Web Scrape** — scrape specific URLs for content extraction

### Research Execution

Run all selected sources in parallel. For each source, construct queries relevant to the presentation topic.

**Tool mapping per source:**

**Social Media** (Direct tools):
```
FetchInstagramProfileTool → Instagram handles
FetchTiktokProfileTool → TikTok handles
FacebookBusinessPageInfoTool → Facebook pages
FetchYoutubeChannelTool → YouTube channels
FetchYoutubeChannelVideosTool → YouTube channels (recent videos)
```
Ask for handles/page names if not already provided.

**News** (Direct tool):
```
GoogleNewsSearchTool → "[topic] [industry]" and related queries
```

**Finance** (Approximated — no dedicated tool):
```
PerplexitySonarSearchTool → "[company/industry] financial performance [year]"
PerplexitySonarSearchTool → "[company] revenue market cap earnings"
```
Note: Finance data is gathered via Perplexity Sonar synthesis, not a dedicated financial data API. Results are AI-synthesized from web sources — verify critical numbers independently.

**Trends** (Direct tools):
```
GoogleTrendsTrendingNowTool → current trending queries related to topic
GoogleTrendsTrendingNewsTool → trending news in the space
```

**SERP** (Direct tool):
```
PerplexitySonarSearchTool → topic-specific research questions (2-3 queries)
```

**Ads** (Direct tools):
```
MetaAdLibraryPageSearchTool → search for advertisers in the space
TiktokAdsLibrarySearchTool → search TikTok ads by keyword
GoogleAdsTransparencyAdvertiserSearchTool → find Google advertisers by domain/keyword
```

**YouTube** (Direct tools):
```
FetchYoutubeTranscriptTool → top 2-3 video transcripts by relevance
FetchYoutubeChannelVideosTool → channel video listings
FetchYoutubeCommentsTool → top video comments for audience sentiment
```

**App Stores** (Approximated — no dedicated tool):
```
PerplexitySonarSearchTool → "[app name] app store reviews ratings features"
PerplexitySonarSearchTool → "[app category] best apps comparison [year]"
```
Note: App store data is gathered via Perplexity Sonar synthesis, not direct app store API access.

**Google Maps Reviews** (Direct tools via SearchAPI MCP):
```
google_maps_search → business name + location
google_maps_place → place_id from search (reviews, rating, hours, GPS coordinates)
```

**Perplexity Sonar** (Direct tool):
```
PerplexitySonarSearchTool → 2-3 synthesized research queries about the topic
```

**Web Scrape** (Direct tools):
```
FirecrawlScrapeTool → user-provided URLs or auto-discovered relevant pages
FirecrawlMapTool → discover all pages on a given domain
```

### Research Summary

After all research completes, present a structured summary (5-8 bullet points) of key findings organized by relevance to the presentation. Let the user correct or add context before moving to style selection.

> "Here's what I found: [research summary]. Any corrections or additional context before we move on to presentation design?"

---

## Step 3: Mode Selection

Ask the user whether they want a single presentation or a multi-material project:

> "Do you want a single presentation, or a multi-material project with several deliverables sharing the same research?"
>
> - **Single** — one slide deck (default)
> - **Multi-material** — multiple deliverables with an index page linking them all (e.g., Pitch Deck + Business Plan + Financial Report)

---

## Step 4: Presentation Type & Style

### 4A: Presentation Type

**If Single mode** — present as a single-select list:

> "What type of presentation is this?"

**If Multi-material mode** — present as a multi-select checklist:

> "Which materials should I generate? Select all that apply:"

| Type | Best For |
|------|----------|
| **Pitch Deck** | Startup fundraising, accelerator applications |
| **Plan de Negocios** | Full business plan overview, strategy documentation |
| **Reporte Financiero** | Financial report, projections, earnings |
| **Reporte Operativo** | Operations report, KPIs, process documentation |
| **Reporte de Ventas** | Sales report, pipeline, revenue analysis |
| **Planeacion de Proyectos** | Project planning, roadmap, timeline, milestones |
| **Sales / Investment Pitch** | Revenue-focused, investor meetings, sales decks |
| **Marketing / Campaign** | Campaign proposals, brand presentations, creative briefs |
| **Business Model Canvas** | Business model visualization, strategy workshops |
| **Product Launch** | New product announcements, feature reveals |
| **Educational / Workshop** | Training sessions, course material, tutorials |
| **Portfolio / Case Study** | Project showcases, agency work, before/after stories |
| **Market Analysis / Research Report** | Data-driven insights, competitive analysis |
| **Professional / Corporate** | Internal reports, board updates, company overviews |

Also propose 1-2 context-specific types based on the topic (e.g., if the topic is about a new app, suggest "Product Demo / Walkthrough").

For multi-material: user must select at least 2 types. All selected materials will share the same visual style and design preferences for consistency.

### 4B: Presentation Style (2-3 Quick Questions)

Instead of showing a menu of predefined styles, ask 2-3 quick questions to understand the user's visual preferences, then derive the style from their answers. **Always use light backgrounds** — dark mode is hard to read in presentations and should never be used.

Ask all in one message:

> "A few quick questions to nail the visual direction:"
>
> 1. **What's the vibe?** Corporate & clean, bold & energetic, elegant & editorial, data-heavy & analytical, or creative & immersive?
> 2. **Color mood?** Any brand colors (hex codes)? Or a general feel — e.g., "warm earth tones", "corporate blue", "vibrant and modern", "monochrome"
> 3. **Content density?** Spacious with lots of breathing room, or content-rich with more data per slide?

**Deriving the style from answers:**

| User vibe | Mapped style | Description |
|-----------|-------------|-------------|
| Corporate & clean | **Minimalist Executive** | Light backgrounds, generous whitespace, subtle fade transitions, muted palette, authoritative typography |
| Bold & energetic | **Bold & Dynamic** | Light backgrounds with strong saturated accent colors, large display typography, impactful slide transitions, high contrast elements |
| Elegant & editorial | **Editorial / Magazine** | Light cream/warm backgrounds, editorial grid layouts, pull quotes, sophisticated serif + sans-serif pairing, photography-forward |
| Data-heavy & analytical | **Data-Driven / Analytical** | Light backgrounds, chart emphasis, structured grids, clear visual hierarchy, monospace accents, dashboard-inspired |
| Creative & immersive | **Creative / Storytelling** | Light backgrounds with atmospheric accents, narrative flow between slides, cinematic transitions, full-bleed imagery with light overlays |

**IMPORTANT — Light mode only:** All presentations must use light backgrounds (white, off-white, cream, light gray). Dark backgrounds are hard to read in presentation settings (projectors, screen sharing, printed handouts). Use dark/bold colors for text, accents, and decorative elements — never for the main slide background. This is non-negotiable regardless of user preference.

### 4C: Design Finalization

Based on the user's answers above, determine:
- **Color palette**: derive from brand colors or mood — always with light backgrounds and dark text
- **Typography mood**: derive from the vibe (corporate = modern sans-serif, editorial = serif + sans-serif, etc.)
- **Visual density**: directly from their answer

After all selections, confirm the complete direction:

**Single mode:**
> "Got it — I'll build a [type] presentation in [style] style with [color/typography/mode] preferences. [N] slides approximately. Let me draft the outline."

**Multi-material mode:**
> "Got it — I'll build [N] materials ([list of types]) in [style] style with [color/typography/mode] preferences. Let me draft the outlines for all of them."

---

## Step 5: Outline & Narrative Structure

Based on the topic, research, type(s), and style, propose a slide-by-slide outline for each material.

**Single mode:** Propose one outline.

**Multi-material mode:** Propose all outlines together in one message, grouped by material type. The user reviews and confirms the full project structure at once before any content is generated.

### Default Structures by Type

**Pitch Deck (Startup):**
1. Title / Hook
2. Problem
3. Solution
4. Market Opportunity
5. Product / Demo
6. Business Model
7. Traction / Metrics
8. Team
9. Financial Projections
10. The Ask / CTA

**Sales / Investment Pitch:**
1. Title / Hook
2. The Opportunity
3. Current Landscape
4. Our Approach
5. Key Differentiators
6. Case Studies / Results
7. Pricing / ROI
8. Next Steps

**Professional / Corporate:**
1. Title
2. Agenda
3. Context / Background
4–N. Content sections
N+1. Key Takeaways
N+2. Next Steps / Q&A

**Marketing / Campaign:**
1. Title / Campaign Name
2. Objective
3. Target Audience
4. Market Insights
5. Strategy / Approach
6. Creative Direction
7. Channel Plan
8. Timeline
9. Budget / Resources
10. KPIs / Success Metrics

**Business Model Canvas:**
1. Title
2. Value Proposition
3. Customer Segments
4. Channels
5. Customer Relationships
6. Revenue Streams
7. Key Resources
8. Key Activities
9. Key Partners
10. Cost Structure
11. Summary / Next Steps

**Product Launch:**
1. Title / Product Name
2. The Problem
3. Introducing [Product]
4. Key Features (2-3 slides)
5. How It Works
6. Pricing / Plans
7. Launch Timeline
8. Go-To-Market
9. CTA / Get Started

**Educational / Workshop:**
1. Title
2. Learning Objectives
3. Context / Why This Matters
4–N. Teaching sections with key concepts
N+1. Summary / Recap
N+2. Resources / Further Reading

**Portfolio / Case Study:**
1. Title / Project Name
2. The Challenge
3. Approach / Process
4–6. Solution Details (with visuals)
7. Results / Impact
8. Testimonials
9. Contact / CTA

**Market Analysis / Research Report:**
1. Title / Research Question
2. Methodology
3. Executive Summary
4. Key Finding 1
5. Key Finding 2
6. Key Finding 3
7. Market Landscape / Competitive Map
8. Implications / Recommendations
9. Appendix / Sources

**Plan de Negocios:**
1. Title / Company Name
2. Executive Summary
3. Problem / Opportunity
4. Solution / Value Proposition
5. Target Market & Customer Segments
6. Business Model & Revenue Streams
7. Competitive Landscape
8. Go-To-Market Strategy
9. Operations Plan
10. Team & Organization
11. Financial Projections (3-5 years)
12. Funding Requirements / Use of Funds
13. Milestones & Roadmap
14. Risks & Mitigation
15. Summary / Next Steps

**Reporte Financiero:**
1. Title / Report Period
2. Executive Summary
3. Revenue Overview (charts, YoY comparison)
4. Cost Structure & Margins
5. Profit & Loss Summary
6. Cash Flow Analysis
7. Balance Sheet Highlights
8. Key Financial Ratios
9. Budget vs. Actual
10. Projections & Forecast
11. Risks & Considerations
12. Recommendations / Action Items

**Reporte Operativo:**
1. Title / Report Period
2. Executive Summary
3. Key Performance Indicators (KPI dashboard)
4. Operations Overview
5. Team Performance & Headcount
6. Process Metrics & Efficiency
7. Quality & Customer Satisfaction
8. Incidents / Issues & Resolution
9. Infrastructure & Resources
10. Goals vs. Achievements
11. Improvement Initiatives
12. Next Period Priorities

**Reporte de Ventas:**
1. Title / Report Period
2. Executive Summary
3. Revenue by Product / Segment
4. Sales Pipeline & Funnel
5. Conversion Rates & Metrics
6. Top Accounts / Clients
7. New Business vs. Renewals
8. Regional / Channel Breakdown
9. Sales Team Performance
10. Competitor Activity
11. Forecast & Targets
12. Action Plan / Next Steps

**Planeacion de Proyectos:**
1. Title / Project Name
2. Project Overview & Objectives
3. Scope Definition
4. Stakeholders & Team
5. Timeline & Milestones (Gantt-style)
6. Work Breakdown Structure
7. Resource Allocation
8. Budget Overview
9. Risk Assessment & Mitigation
10. Dependencies & Constraints
11. Communication Plan
12. Success Criteria & KPIs
13. Next Steps / Kick-off

### Outline Presentation

For each slide in the outline, specify:

| # | Title | Key Content | Visual Element | Speaker Notes |
|---|-------|-------------|----------------|---------------|
| 1 | [Title] | [2-4 bullet points] | [image/chart/diagram/full-bleed bg] | [Brief talking points] |
| 2 | ... | ... | ... | ... |

**Single mode** — present one outline and ask for confirmation:
> "Here's the slide-by-slide outline for your [N]-slide [type] presentation. Want to reorder, add, remove, or change any slides before I generate the content and design?"

**Multi-material mode** — present all outlines grouped by material type in one message:
> "Here are the outlines for all [N] materials in this project:"
>
> **1. Pitch Deck** (10 slides)
> [outline table]
>
> **2. Plan de Negocios** (15 slides)
> [outline table]
>
> **3. Reporte Financiero** (12 slides)
> [outline table]
>
> "Want to reorder, add, remove, or change any slides in any of these before I start generating?"

The user confirms the full project structure at once before any content generation begins.

---

## Step 6: Content & Image Generation

**Multi-material mode:** Repeat this step for each selected material type, reusing the shared research data. Images can be shared across materials where appropriate (e.g., the same hero image or brand visual).

### 6A: Slide Content

For each slide, generate:
- **Headline**: large, bold, fits the style — one line that communicates the slide's message
- **Body**: bullet points or short text blocks — concise, presentation-friendly, no walls of text
- **Data callouts**: large numbers or stats when applicable ("47% increase", "$2.3M revenue", "10x faster")
- **Speaker notes**: what to say when presenting this slide — not shown on screen, stored in hidden `<div>`

**Writing rules:**
- One idea per slide. If a point needs elaboration, split across slides.
- Slide text should be scannable in under 5 seconds. The detail goes in speaker notes.
- Use the audience's language level and terminology.
- Ground content in research data where available. Real numbers are more compelling than vague claims.
- Write in the presentation language (auto-detected or user-specified).

### 6B: Image Generation

Check for `infsh` CLI first:

```bash
which infsh
```

**If available**, generate images using FLUX model:

```bash
infsh app run falai/flux-dev-lora --input '{
  "prompt": "[aesthetic-matched prompt]"
}'
```

**If not available**, use Social Toolkit MCP `HiggsfieldImageTool`.

**If both fail**, create CSS-only atmospheric effects (gradients, noise textures, geometric patterns, layered transparencies) and include the image prompts as comments in the HTML for manual generation later.

**Images to generate (3-6 per presentation, not one per slide):**

| Image | Purpose | Aspect |
|-------|---------|--------|
| Title / hero background | Sets the visual tone | 16:9 |
| Section divider backgrounds (1-2) | Visual break between major sections | 16:9 |
| Content visuals (1-3) | Supporting imagery for key content slides | 16:9 |

**Prompt construction pattern:**

```
"[presentation style aesthetic] presentation visual,
[slide context — e.g., 'technology innovation concept'],
[color palette from design preferences],
professional [photography/illustration/abstract] style,
16:9 widescreen composition,
no text overlay, no logos, clean and presentation-ready"
```

**Download all generated images** to the `images/` directory inside the `/tmp/presentation-<topic-slug>/` project folder. Tell the user: *"Generating presentation images — this may take a moment..."*

---

## Step 7: HTML Slide Deck Generation

Generate self-contained HTML files with all CSS and JS inline. The only external dependencies are Google Fonts via `<link>` tag. Images referenced as relative paths (`images/*.jpg`).

**Multi-material mode:** Generate each material as its own slide deck in a subdirectory, then generate an index page at the project root.

### File Structure

All presentation files are generated in a namespaced folder under `/tmp/` to keep the user's project directory clean and support multiple runs without collisions.

**Directory convention:**
```
/tmp/presentation-<topic-slug>/
```

Where `<topic-slug>` is a kebab-case version of the topic (e.g., `/tmp/presentation-startup-pitch/`, `/tmp/presentation-q1-sales-report/`). If the skill is run multiple times for the same topic, append a counter: `/tmp/presentation-startup-pitch-2/`.

**Single mode:**
```
/tmp/presentation-<topic-slug>/
  index.html          ← the slide deck
  images/
    hero-bg.jpg
    section-1.jpg
    content-1.jpg
    ...
  wrangler.jsonc      ← Cloudflare Workers deployment config
```

**Multi-material mode:**
```
/tmp/presentation-<topic-slug>/
  index.html              ← fancy brochure/book index linking to all materials
  images/
    index-hero.jpg         ← hero image for the index page
  pitch-deck/
    index.html             ← individual slide deck
    images/
  plan-de-negocios/
    index.html
    images/
  reporte-financiero/
    index.html
    images/
  ... (one subdirectory per selected material)
  wrangler.jsonc           ← deploys the entire project
```

### Index Page (Multi-material mode only)

The root `index.html` is NOT a slide deck — it's a visually rich, responsive landing page that acts as a **book cover / brochure** linking to all materials.

**Design:**
- Shares the same typography, color palette, and aesthetic as the slide decks
- Self-contained HTML with inline CSS, Google Fonts via `<link>` tag
- AI-generated hero image matching the project's visual identity

**Page sections:**

**1. Hero**
- Project / company name (large display type)
- Tagline or project description (1 line)
- Date or period
- Optional AI-generated background image

**2. Materials Grid**

CSS Grid of cards, one per deliverable. Each card:

```
┌─────────────────────────────────────┐
│  [Icon / visual]                    │
│  Material Title                     │
│  Brief description (1-2 lines)      │
│  Slide count badge                  │
│  → View Presentation                │
└─────────────────────────────────────┘
```

- Cards link to `./pitch-deck/index.html`, `./plan-de-negocios/index.html`, etc.
- Hover effects (subtle scale, shadow, or color shift)
- Responsive: 3 columns on desktop, 2 on tablet, 1 on mobile
- Keyboard-navigable (tab between cards, Enter to open)

**3. Footer**
- Generation date, project name, attribution

**Icon mapping per material type:**

| Material | Icon suggestion |
|----------|---------------|
| Pitch Deck | Rocket / presentation |
| Plan de Negocios | Building / briefcase |
| Reporte Financiero | Chart / dollar sign |
| Reporte Operativo | Gear / dashboard |
| Reporte de Ventas | Cart / trending up |
| Planeacion de Proyectos | Calendar / roadmap |
| Sales / Investment Pitch | Handshake / money |
| Marketing / Campaign | Megaphone / target |
| Business Model Canvas | Grid / puzzle |
| Product Launch | Sparkle / box |
| Educational / Workshop | Book / lightbulb |
| Portfolio / Case Study | Folder / gallery |
| Market Analysis | Search / magnifier |
| Professional / Corporate | Building / tie |

Use inline SVG icons — no external icon libraries. Keep them simple, monochrome, matching the accent color.

### Typography Selection

Select 2 Google Fonts based on the style choice. Never use Inter, Roboto, Arial, or system fonts as the display font.

| Style | Display Font Options | Body Font Options |
|-------|---------------------|-------------------|
| Minimalist Executive | Outfit, Sora, Plus Jakarta Sans | Source Serif 4, Lora, Newsreader |
| Bold & Dynamic | Bebas Neue, Anton, Barlow Condensed | DM Sans, Manrope, Space Grotesk |
| Editorial / Magazine | Playfair Display, Fraunces, Cormorant | Source Sans 3, Libre Franklin, Crimson Pro |
| Data-Driven / Analytical | Space Grotesk, IBM Plex Mono, JetBrains Mono | IBM Plex Sans, Atkinson Hyperlegible |
| Creative / Storytelling | Syne, Josefin Sans, Unbounded | DM Sans, General Sans, Outfit |

Never reuse the same font combination across different generations. Vary intentionally.

### CSS Variables

```css
:root {
  /* Theme — derived from design preferences (ALWAYS light backgrounds) */
  --bg-primary: #[light hex — white/off-white/cream];
  --bg-secondary: #[light hex — light gray/warm white];
  --text-primary: #[dark hex — near-black/dark gray];
  --text-secondary: #[medium hex — gray];
  --accent: #[hex — bold color for emphasis];
  --accent-secondary: #[hex];

  /* Typography */
  --font-display: '[Display Font]', sans-serif;
  --font-body: '[Body Font]', sans-serif;

  /* Slide dimensions */
  --slide-width: 100vw;
  --slide-height: 100vh;

  /* Transitions */
  --transition-duration: 0.6s;
  --transition-easing: cubic-bezier(0.4, 0, 0.2, 1);
}
```

### Slide Transitions by Style

| Style | Transition |
|-------|-----------|
| Minimalist Executive | Fade (opacity 0→1, 0.6s ease) |
| Bold & Dynamic | Slide from right (translateX 100%→0) |
| Editorial / Magazine | Crossfade with slight scale (opacity + scale 0.95→1) |
| Data-Driven / Analytical | Instant cut with subtle fade (0.3s) |
| Creative / Storytelling | Cinematic push (translateX with parallax on background) |

### Slide Layout Classes

Each slide uses a layout class that determines its visual structure:

| Layout | Class | Description |
|--------|-------|-------------|
| Title | `.slide--title` | Centered content, large display font, optional background image |
| Content | `.slide--content` | Left-aligned headline + bullets or text |
| Two Column | `.slide--two-col` | 50/50 split — text on one side, image/visual on the other |
| Full Image | `.slide--image` | Full-bleed background image with text overlay |
| Data / Stats | `.slide--data` | Large number callouts, chart areas, metric grids |
| Quote | `.slide--quote` | Centered large quote with attribution |
| Section Divider | `.slide--divider` | Bold section title, visual break between sections |
| Closing / CTA | `.slide--closing` | Final slide with CTA, contact info, next steps |

### HTML Structure

```html
<!DOCTYPE html>
<html lang="[language]">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Presentation Title]</title>
  <link href="https://fonts.googleapis.com/css2?family=[Display+Font]:wght@400;700;900&family=[Body+Font]:wght@300;400;600&display=swap" rel="stylesheet">
  <style>
    /* All CSS inline — reset, variables, slide layouts, transitions, responsive */
  </style>
</head>
<body>
  <div class="presentation" id="presentation">
    <div class="progress-bar" id="progress"></div>
    <div class="slide-counter" id="counter">1 / N</div>
    <div class="controls">
      <button class="control-btn" id="prev" aria-label="Previous slide">&larr;</button>
      <button class="control-btn" id="next" aria-label="Next slide">&rarr;</button>
    </div>

    <div class="slide slide--title active" data-slide="1" aria-roledescription="slide" aria-label="Slide 1 of N">
      <div class="slide-content">
        <!-- Slide content here -->
      </div>
      <div class="slide-notes" hidden>
        <!-- Speaker notes -->
      </div>
    </div>

    <div class="slide slide--content" data-slide="2" aria-roledescription="slide" aria-label="Slide 2 of N">
      <!-- ... -->
    </div>

    <!-- Repeat for all slides -->
  </div>

  <script>
    /* All JS inline — navigation, keyboard, touch, progress, fullscreen, notes */
  </script>
</body>
</html>
```

### JavaScript Navigation System

The inline JS must handle:

1. **Keyboard navigation**: ArrowLeft/ArrowRight to navigate, Escape for overview grid, F for fullscreen, N to toggle speaker notes
2. **Click/tap**: Click on slide area to advance
3. **Touch/swipe**: `touchstart`, `touchmove`, `touchend` listeners for swipe detection on mobile
4. **Progress bar**: Visual indicator at top of viewport showing current position (width = currentSlide / totalSlides * 100%)
5. **Slide counter**: "3 / 12" display in corner
6. **URL hash**: Update `#slide-3` on navigation for direct linking — read hash on load to jump to specific slide
7. **Overview mode**: Escape key toggles a grid view of all slides (miniature thumbnails) — click any to jump
8. **Fullscreen**: F key triggers `document.documentElement.requestFullscreen()`
9. **Speaker notes**: N key toggles a notes panel at the bottom of the screen (visible only to presenter)

### Responsive Design

Slides should work on:
- **Desktop** (primary): full-screen 16:9, all navigation features
- **Tablet**: landscape works like desktop, portrait reduces font sizes
- **Mobile** (<768px): fall back to vertical scroll mode where slides stack naturally — swipe navigation still works horizontally, but vertical scroll is the default

```css
@media (max-width: 768px) {
  .slide {
    min-height: 100vh;
    height: auto;
  }
  /* Stack slides vertically, adjust typography */
}

@media (prefers-reduced-motion: reduce) {
  .slide { transition: none !important; }
}
```

### Accessibility

- All navigation is keyboard-accessible
- `aria-roledescription="slide"` on each slide div
- `aria-label="Slide N of M"` for screen readers
- `prefers-reduced-motion` media query disables all transitions
- High contrast text on all backgrounds (minimum WCAG AA)
- `alt` text on all images
- Focus indicators on interactive elements
- Semantic heading hierarchy within slides

### Open in Browser

After generating the HTML file and saving images:

```bash
open /tmp/presentation-<topic-slug>/index.html
```

Tell the user the full local file path (under `/tmp/`).

---

## Step 8: Deployment

### 8A: Subdomain Selection

Before deploying, ask the user what subdomain they'd like:

> "What subdomain would you like for this presentation? (e.g., `my-pitch.workers.dev`). If you don't have a preference, I'll pick one for you."

**If the user provides a subdomain:** use it as-is (kebab-case, lowercase).

**If the user skips or says "whatever" / "you pick":** auto-generate one:
1. Pick a single descriptive word from the presentation topic (e.g., "pitch", "sales", "finanzas", "growth", "launch")
2. Append a random 6-character alphanumeric string to avoid collisions
3. Result: `pitch-a3x9k2`, `growth-m7b2p1`, `finanzas-q8r4t6`

```bash
# Generate random suffix
SUFFIX=$(LC_ALL=C tr -dc 'a-z0-9' < /dev/urandom | head -c 6)
SUBDOMAIN="<word>-${SUFFIX}"
```

### 8B: Deploy to Cloudflare Workers

Include a `wrangler.jsonc` in the output directory (root level for both single and multi-material):

```jsonc
{
  "name": "<subdomain>",
  "compatibility_date": "2025-04-01",
  "assets": {
    "directory": "./"
  }
}
```

Deploy from the `/tmp/` project directory:
```bash
cd /tmp/presentation-<topic-slug> && wrangler deploy
```

This deploys the entire directory tree as a static site to Cloudflare Workers.

**Single mode** — one URL for the presentation.

**Multi-material mode** — one URL with paths for each material:
- `https://<subdomain>.workers.dev/` → index page (brochure)
- `https://<subdomain>.workers.dev/pitch-deck/` → pitch deck
- `https://<subdomain>.workers.dev/plan-de-negocios/` → business plan
- `https://<subdomain>.workers.dev/reporte-financiero/` → financial report

Tell the user the deployed URL(s) once complete.

---

## Step 9: Storage

Ask the user where to save. Do NOT assume — always ask.

> "Where should I save this presentation?"
> - **Notion** — create a structured page with slide content, speaker notes, and the deployed URL
> - **Local only** — the HTML file is already saved, no additional storage needed

### Notion Storage (if selected)

First check for existing presentations:
```
notion-search → "[presentation topic]" (query_type: "internal", page_size: 5)
```

If a matching page is found, ask: *"I found an existing presentation about [topic] in Notion. Should I update it or create a new page?"*

**Single mode** — create one page with `notion-create-pages`:

**Page title:** `Presentation: [Topic] — [Date]`

**Page structure:**

```
# Presentation Overview
[Topic, audience, goal, type, style, slide count]

## Slide Outline
[Table: # | Title | Key Points | Visual | Notes]

---

# Slide Content

## Slide 1: [Title]
[Full headline and body content]
**Speaker Notes:** [notes]

## Slide 2: [Title]
[Full content]
**Speaker Notes:** [notes]

[Repeat for all slides]

---

# Deliverables
- HTML file: [local path]
- Deployed URL: [Cloudflare Workers URL]
- Images: [list of generated images]

# Image Generation Prompts
[Code blocks with each prompt for regeneration]
```

**Multi-material mode** — create one parent page with sub-pages:

**Parent page title:** `Project: [Topic] — [Date]`

**Parent page structure:**
```
# Project Overview
[Topic, audience, goals, style, total materials count]

## Materials
[Table: # | Material | Slides | URL | Status]

## Deployed URL
[Root URL linking to index page]
```

Then create one **sub-page per material** (as children of the parent page), each following the single-mode page structure above. This keeps the Notion workspace organized with the project as a container.

---

## Step 10: Present Deliverables

Wrap up with a clean summary.

**Single mode:**

**What was created:**
- [N]-slide [type] presentation in [style] style
- Interactive HTML slide deck: `/tmp/presentation-<topic-slug>/index.html`
- [N] AI-generated visuals
- Deployed URL: `https://<subdomain>.workers.dev/`
- [Notion page URL — if saved to Notion]

**Multi-material mode:**

**What was created:**
- [N] materials in [style] style:
  - Pitch Deck ([N] slides): `[URL]/pitch-deck/`
  - Plan de Negocios ([N] slides): `[URL]/plan-de-negocios/`
  - Reporte Financiero ([N] slides): `[URL]/reporte-financiero/`
  - [... list all materials]
- Index page (brochure): `[URL]/`
- [N] total AI-generated visuals
- Local project directory: `/tmp/presentation-<topic-slug>/`
- [Notion parent page URL — if saved to Notion]

**Navigation guide (applies to each slide deck):**
- **Arrow keys**: Navigate slides
- **Click / tap**: Next slide
- **Swipe**: Next / previous (touch devices)
- **Escape**: Overview / grid mode
- **F**: Fullscreen
- **N**: Toggle speaker notes
- **Direct link**: Share `[URL]#slide-5` to link to a specific slide

**Suggested next steps:**
- Present directly from the browser (F11 for fullscreen)
- Share the Cloudflare Workers URL with your audience
- For multi-material: share the index page URL — recipients can navigate to each material
- Edit any HTML file to customize slide content
- Come back with `/presentation` to create another deck or project

---

## Behavior Notes

- **Research first, design second.** The quality of the slides depends on understanding the topic and audience. Don't rush to generate HTML before the outline is solid.
- **Be opinionated about structure.** Propose a specific slide order and narrative arc. Don't present empty outlines — fill in real content and let the user adjust.
- **Write for the stage, not the page.** Slide text should be concise, impactful, and scannable. No paragraphs on slides. The detail goes in speaker notes.
- **One idea per slide.** Never cram multiple concepts into one slide. If a point needs elaboration, split it across slides.
- **Visual consistency.** Every slide should feel like it belongs to the same deck. Consistent margins, font sizes, color usage, and transition style throughout.
- **Ground content in research.** If data was gathered from real sources, cite it. Real numbers ("+47% YoY", "$2.3B market") are more compelling than vague claims.
- **The title slide sets expectations.** Make it bold, memorable, and immediately communicative. The audience decides in the first 3 seconds whether to pay attention.
- **Handle multiple languages naturally.** If the topic is in Spanish, write slides in Spanish. Don't translate — write natively.
- **Images should feel intentional.** Every generated image should match the slide's content and the overall aesthetic. No generic stock photo vibes.
- **Design with restraint.** Modern, bold, minimalist, elegant — not cluttered. Whitespace is a design tool. Typography carries the message. Color accents draw focus, not distract.
- **Show research before design, outline before content.** Each phase builds on the previous. Let the user validate each step before moving forward.
- **Multi-material consistency.** When generating multiple materials, maintain visual unity across all decks — same fonts, colors, transition style, and image aesthetic. The index page and all materials should feel like chapters of the same book. Reuse images where they fit (e.g., the same hero visual can appear on the index and the pitch deck title slide).
