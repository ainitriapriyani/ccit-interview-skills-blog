# AGENTS.md — CCIT Interview Skills Hub

## Project Architecture

A pure static site — no build step, no framework, no runtime dependencies. All pages are standalone HTML files that share a CSS/JS layer.

```
/
├── index.html               # Home page
├── about.html               # About page
├── team.html                # Team page
├── learning-materials.html  # Learning Materials (all 5 topics)
├── css/
│   ├── style.css            # Global styles, layout, components, all pages
│   └── learning.css         # Extra styles specific to learning-materials.html
└── js/
    └── main.js              # All JavaScript: dark mode, navbar, search, animations
```

## Key Directories & Files

### `css/style.css`
The single source of truth for design. Organized into labeled sections:
- CSS custom properties (`:root` and `[data-theme="dark"]`)
- Reset and base styles
- Typography scale
- Layout utilities (`.container`, `.section`, `.section-header`)
- Navigation (`.navbar`, `.dropdown`, `.mobile-nav`)
- Buttons (`.btn`, `.btn-primary`, `.btn-outline`, `.btn-ghost`)
- Cards (`.card`, `.card-glass`)
- Hero section
- Feature/topic cards
- Search bar
- Learning layout (sidebar + content)
- About, Team page-specific styles
- Footer
- Scroll animations (`.reveal`)
- Responsive breakpoints

### `css/learning.css`
Contains styles for components used only in `learning-materials.html`:
- `.type-card` — interview type cards
- `.key-points` — structured info box
- `.stage-card` — stage timeline cards
- `.example-answer` — quoted sample answers
- `.stats-banner` — bottom statistics grid
- `.sw-card` — strength/weakness comparison cards

### `js/main.js`
IIFE-wrapped, no dependencies. Exports nothing — it self-initializes on `DOMContentLoaded`. Key functions:
- `initTheme()` / `applyTheme()` — reads from localStorage, sets `data-theme` attribute
- `initNavbar()` — adds `.scrolled` class for shadow on scroll
- `setActiveNavLink()` — highlights current page link
- `initMobileNav()` — hamburger toggle with animated spans
- `initBackTop()` — shows/hides button, scrolls on click
- `initReveal()` — IntersectionObserver for `.reveal` elements
- `initSidebarHighlight()` — tracks visible `.learning-section` and updates `.sidebar-link.active`
- `updateProgress()` — calculates reading progress percentage, updates `.progress-fill` width
- `initSearch()` — client-side search against `SEARCH_DATA` array with keyword + title + snippet matching and inline term highlighting
- `initCounters()` — animates `[data-count]` numbers on scroll into view with easing

## Coding Conventions

- **CSS custom properties** for all colors, shadows, radii, transitions. Never hard-code hex values in component rules.
- **BEM-inspired naming** without strict BEM: `.card`, `.feature-card`, `.feature-icon`. Modifiers via separate classes (`.btn-primary`, `.reveal-delay-1`).
- **Reveal animations**: Add `.reveal` to any element. Add `.reveal-delay-N` (1–5) for staggered entrance. The IntersectionObserver handles the rest.
- **Dark mode**: Controlled via `data-theme="dark"` on `<html>`. All dark mode overrides live in the `[data-theme="dark"]` block in `style.css` at the top. Never scatter dark-mode styles through the file.
- **No inline styles for layout**: Inline styles are used only for one-off overrides on specific page elements that don't justify a new class (e.g., `max-width: 700px` on a single wrapper).

## Non-Obvious Decisions

- **Search data is hardcoded in `main.js`**: Because there is no backend, all searchable content is an array of objects (`SEARCH_DATA`). This is intentional — the site is fully static. If content expands significantly, consider extracting this to a JSON file loaded via `fetch`.
- **Sidebar progress is scroll-position based, not click-based**: The `.sidebar-link.active` state and the progress bar are driven by `IntersectionObserver` on `.learning-section` elements, not by which link was clicked. This means progress updates automatically as the user scrolls.
- **Dark mode defaults to system preference**: On first visit, `initTheme()` checks `prefers-color-scheme`. On subsequent visits, the localStorage value takes precedence.
- **Four separate HTML files, not a SPA**: Navigation between pages uses standard anchor tags to separate HTML files. This keeps the site purely static without a router.
- **Google Fonts loaded in every HTML file**: Each page has its own `<link>` for Google Fonts, ensuring fonts work when pages are opened directly without navigating from the home page.
- **Dropdown uses CSS hover, not JavaScript**: The dropdown menu in the navbar is triggered by `:hover` via CSS, not JS event listeners. This keeps it lightweight and eliminates edge cases on mobile (where hover does not apply — the mobile nav handles those links separately).
