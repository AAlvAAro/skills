---
name: frontend-design
description: Create distinctive, production-grade frontend interfaces with high design quality. Use this skill when the user asks to build web components, pages, or applications. Generates creative, polished code that avoids generic AI aesthetics. Auto-detects project context (Rails+Inertia, standalone React, or plain HTML) and generates code that fits.
license: Complete terms in LICENSE.txt
---

This skill guides creation of distinctive, production-grade frontend interfaces that avoid generic "AI slop" aesthetics. Implement real working code with exceptional attention to aesthetic details and creative choices.

The user provides frontend requirements: a component, page, application, or interface to build. They may include context about the purpose, audience, or technical constraints.

## Step 0 — Detect Project Context

**Before generating any code**, detect which mode to use by checking the current working directory:

### Mode 1: Rails + Inertia (Auto-detected)

Activate when ALL of these exist:
- `Gemfile` contains `inertia_rails`
- `package.json` contains `@inertiajs/react`
- `app/frontend/pages/` directory exists

→ Jump to **"Rails + Inertia Mode"** section below.

### Mode 2: Standalone React (Vite SPA)

Activate when:
- The user asks for a standalone app, landing page, or marketing site
- No Rails+Inertia project is detected
- The user does NOT request plain HTML

→ Jump to **"Standalone React Mode"** section below.

### Mode 3: Plain HTML

Activate when:
- The user explicitly asks for "plain HTML", "single file", or "no framework"
- The project is a simple static page with no interactivity beyond CSS hover/scroll

→ Generate a single `index.html` with inline CSS/JS. Follow the Design Thinking and Aesthetics sections only.

---

## Rails + Inertia Mode

This mode generates code that integrates directly into a Rails + Vite + Inertia.js + React project. No merge step needed — code goes directly where it belongs.

### Discovery Phase

Before writing any code, read these files to understand the existing project patterns:

1. **Existing pages** — read 1-2 files in `app/frontend/pages/` to match conventions
2. **Layout system** — check `app/frontend/layouts/` to see available layouts
3. **Components** — check `app/frontend/components/ui/` for existing shadcn/ui components
4. **Types** — read `app/frontend/types/index.ts` for `SharedData`, `Auth`, `User` interfaces
5. **Hooks** — check `app/frontend/hooks/` for available hooks (`use-appearance`, `use-i18n`, etc.)
6. **Routes** — check `config/routes.rb` for routing patterns
7. **Tailwind** — check `tailwind.config.*` or `app/frontend/` CSS files for theme/design tokens
8. **Path aliases** — check `tsconfig.app.json` for `@/*` or `~/*` path aliases

### What to Generate

For each feature/page, generate ALL of these:

#### 1. React Page Component(s)
- **Location**: `app/frontend/pages/<feature>/` (e.g., `app/frontend/pages/store/index.tsx`)
- **Pattern**: TypeScript, functional component, props interface for data from Rails
- **Navigation**: Use `Link` and `router` from `@inertiajs/react` — NEVER use `react-router-dom`
- **Head**: Use `Head` from `@inertiajs/react` for page titles and meta tags
- **Shared data**: Access via `usePage<SharedData>()` for auth, flash, etc.
- **Layout assignment**: Set `PageComponent.layout = (page) => <Layout>{page}</Layout>` or use nested layouts

```tsx
// app/frontend/pages/store/index.tsx
import { Head, Link, usePage } from '@inertiajs/react'
import type { SharedData } from '@/types'
import DashboardLayout from '@/layouts/dashboard-layout'

interface StorePageProps {
  products: Product[]
}

export default function StorePage({ products }: StorePageProps) {
  const { auth } = usePage<SharedData>().props

  return (
    <DashboardLayout>
      <Head title="Store" />
      {/* page content */}
    </DashboardLayout>
  )
}
```

#### 2. Reusable Components
- **Location**: `app/frontend/components/<component-name>.tsx`
- **Use existing shadcn/ui** components from `app/frontend/components/ui/` — do NOT recreate buttons, dialogs, cards, etc.
- **Only create new components** for feature-specific UI that doesn't exist yet
- **Import pattern**: `import { Button } from '@/components/ui/button'`

#### 3. Rails Controller
- **Location**: `app/controllers/<feature>_controller.rb`
- **Inherit from**: `InertiaController` (or `ApplicationController` if that's the pattern)
- **Render pattern**: `render inertia: 'feature/index', props: { data: @data }`
- **Share data**: Use `inertia_share` for data needed across actions

```ruby
# app/controllers/store_controller.rb
class StoreController < InertiaController
  def index
    @products = Product.all
    render inertia: 'store/index', props: {
      products: @products.as_json(only: [:id, :name, :price, :image_url])
    }
  end
end
```

#### 4. Routes
- **Location**: Add to `config/routes.rb`
- **Pattern**: Match existing route style (resourceful or custom)

```ruby
# Add to config/routes.rb
resources :store, only: [:index, :show]
```

#### 5. TypeScript Types (if needed)
- **Location**: Add to `app/frontend/types/index.ts` or create a feature-specific type file
- **Pattern**: Export interfaces for props passed from Rails

### Styling in Rails+Inertia Mode

- **Use Tailwind CSS** — this is the project's styling system. Do NOT use CSS Modules or inline `<style>` tags.
- **Use existing design tokens** — check the Tailwind config for colors, spacing, fonts already defined
- **Use shadcn/ui components** for standard UI elements (buttons, inputs, cards, dialogs, dropdowns, etc.)
- **Custom styles**: Only add custom CSS in `app/frontend/` when Tailwind utilities are insufficient (complex animations, pseudo-elements). Use Tailwind's `@apply` sparingly.
- **Dark mode**: Use `dark:` Tailwind variant — the project likely already has theme switching via `use-appearance` hook
- **Icons**: Use `lucide-react` — it's already installed

### Inertia-Specific Patterns

```tsx
// Navigation — use Inertia Link, not <a> tags
import { Link, router } from '@inertiajs/react'
<Link href="/store" className="...">Store</Link>

// Programmatic navigation
router.visit('/store')
router.post('/store', { data })

// Forms — use Inertia useForm
import { useForm } from '@inertiajs/react'
const { data, setData, post, processing, errors } = useForm({ name: '' })

// Page title
import { Head } from '@inertiajs/react'
<Head title="Page Title" />

// Access shared data (auth, flash, etc.)
import { usePage } from '@inertiajs/react'
const { auth, flash } = usePage<SharedData>().props

// i18n (if the project uses it)
import { useI18n } from '@/hooks/use-i18n'
const { t } = useI18n()
```

### Public/Marketing Pages in Rails+Inertia

For landing pages or public-facing pages that don't need the app layout:
- Still create as Inertia pages (for SSR and consistency)
- Use a minimal layout or no layout (`page.default.layout = null`)
- Can be more creative with Tailwind — use arbitrary values, custom gradients, animations
- Import Google Fonts in the page component or add to `index.html`
- Place images in `public/` directory

---

## Standalone React Mode

For projects outside Rails — standalone landing pages, marketing sites, or apps deployable to Vercel/Netlify/Amplify.

### Project Setup

1. **Scaffold with Vite + React**:
   ```bash
   npm create vite@latest <project-name> -- --template react-ts
   cd <project-name>
   npm install
   ```

2. **Install common dependencies as needed**:
   ```bash
   npm install motion                        # Animation
   npm install react-router-dom              # Routing (if multi-page)
   npm install lucide-react                  # Icons
   npm install react-intersection-observer   # Scroll animations
   ```

3. **Project structure**:
   ```
   src/
   ├── components/       # Reusable UI components
   ├── sections/         # Page sections (for landing pages)
   ├── assets/           # Images, fonts, SVGs
   ├── styles/
   │   └── variables.css # CSS custom properties / theme
   ├── App.tsx
   ├── main.tsx
   └── index.css
   ```

4. **Styling**: CSS Modules (`.module.css`) for component styles. Theme variables in `variables.css`.

### Deployment Ready

- **Build**: `npm run build` → `dist/`
- Include `vercel.json`:
  ```json
  { "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }] }
  ```
- Include `netlify.toml`:
  ```toml
  [[redirects]]
    from = "/*"
    to = "/index.html"
    status = 200
  ```
- Use `VITE_` prefixed env vars, document in `.env.example`
- AI-generated images → `public/` directory

### React Guidelines (Standalone)

- Functional components + hooks only
- `motion` library for animations
- `useInView` from `react-intersection-observer` for scroll reveals
- CSS custom properties for theming
- Semantic HTML (`<header>`, `<main>`, `<section>`, `<footer>`)
- `loading="lazy"` on images

---

## Design Thinking (All Modes)

Before coding, understand the context and commit to a BOLD aesthetic direction:
- **Purpose**: What problem does this interface solve? Who uses it?
- **Tone**: Pick an extreme: brutally minimal, maximalist chaos, retro-futuristic, organic/natural, luxury/refined, playful/toy-like, editorial/magazine, brutalist/raw, art deco/geometric, soft/pastel, industrial/utilitarian, etc. There are so many flavors to choose from. Use these for inspiration but design one that is true to the aesthetic direction.
- **Constraints**: Technical requirements (framework, performance, accessibility).
- **Differentiation**: What makes this UNFORGETTABLE? What's the one thing someone will remember?

**CRITICAL**: Choose a clear conceptual direction and execute it with precision. Bold maximalism and refined minimalism both work - the key is intentionality, not intensity.

Then implement working code that is:
- Production-grade and functional
- Visually striking and memorable
- Cohesive with a clear aesthetic point-of-view
- Meticulously refined in every detail

## Frontend Aesthetics Guidelines (All Modes)

Focus on:
- **Typography**: Choose fonts that are beautiful, unique, and interesting. Avoid generic fonts like Arial and Inter; opt instead for distinctive choices that elevate the frontend's aesthetics; unexpected, characterful font choices. Pair a distinctive display font with a refined body font.
- **Color & Theme**: Commit to a cohesive aesthetic. Use CSS variables or Tailwind config for consistency. Dominant colors with sharp accents outperform timid, evenly-distributed palettes.
- **Motion**: Use animations for effects and micro-interactions. In React, prefer `motion` library. Focus on high-impact moments: one well-orchestrated page load with staggered reveals creates more delight than scattered micro-interactions. Use scroll-triggering and hover states that surprise.
- **Spatial Composition**: Unexpected layouts. Asymmetry. Overlap. Diagonal flow. Grid-breaking elements. Generous negative space OR controlled density.
- **Backgrounds & Visual Details**: Create atmosphere and depth rather than defaulting to solid colors. Add contextual effects and textures that match the overall aesthetic. Apply creative forms like gradient meshes, noise textures, geometric patterns, layered transparencies, dramatic shadows, decorative borders, custom cursors, and grain overlays.

NEVER use generic AI-generated aesthetics like overused font families (Inter, Roboto, Arial, system fonts), cliched color schemes (particularly purple gradients on white backgrounds), predictable layouts and component patterns, and cookie-cutter design that lacks context-specific character.

Interpret creatively and make unexpected choices that feel genuinely designed for the context. No design should be the same. Vary between light and dark themes, different fonts, different aesthetics. NEVER converge on common choices (Space Grotesk, for example) across generations.

**IMPORTANT**: Match implementation complexity to the aesthetic vision. Maximalist designs need elaborate code with extensive animations and effects. Minimalist or refined designs need restraint, precision, and careful attention to spacing, typography, and subtle details. Elegance comes from executing the vision well.

Remember: Claude is capable of extraordinary creative work. Don't hold back, show what can truly be created when thinking outside the box and committing fully to a distinctive vision.
