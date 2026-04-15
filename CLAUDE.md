# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

This repo is a **design iteration sandbox** for the TIQ World public landing page. Each revision is a single, self-contained HTML file using Tailwind CDN — no build step, no framework. Open any file directly in a browser to preview.

## Naming Convention

Files are named `landing-v<N>.html` (e.g. `landing-v1.html`, `landing-v2.html`). Never overwrite an existing revision — always create a new one so versions can be compared side by side.

## Source of Truth

The canonical React implementation lives at:

```
F:\IIT-RCOM\tiqworld-github\consumer-app\src\features\landing\pages\
  LandingPage.tsx       ← V1: multi-stage vertical funnel (6 stages)
  LandingPageV2.tsx     ← V2: user-centric, 7 sections (current production)
```

Always read these files before starting a revision to stay aligned with copy, section order, and content.

## Design System

**Fonts** (load via Google Fonts CDN):
- `Space Grotesk` 400/500/600/700 — body and headings
- `JetBrains Mono` 400/500/600/700 — labels, stats, badges, mono copy

**Brand color tokens** (HSL source values from `consumer-app/src/index.css`):

| Token | HSL |
|---|---|
| `copper-600` | `hsl(25, 85%, 58%)` |
| `copper-500` | `hsl(25, 90%, 62%)` |
| `copper-700` | `hsl(25, 80%, 52%)` |
| `teal-500`   | `hsl(180, 75%, 45%)` |
| `teal-600`   | `hsl(180, 80%, 38%)` |
| `slate-950`  | `hsl(217, 24%, 8%)` |
| `slate-900`  | `hsl(217, 20%, 12%)` |
| `slate-850`  | `hsl(217, 18%, 16%)` |
| `slate-800`  | `hsl(217, 16%, 20%)` |
| `slate-700`  | `hsl(217, 14%, 28%)` |
| `slate-600`  | `hsl(217, 12%, 42%)` |
| `slate-400`  | `hsl(217, 10%, 62%)` |
| `slate-300`  | `hsl(217,  8%, 76%)` |
| `slate-50`   | `hsl( 30,  8%, 96%)` |

Copper is the primary CTA/accent. Teal is the secondary accent. The slate palette is custom (cooler/darker than Tailwind's default slate).

**Typography rules:**
- `h1`: uppercase, `letter-spacing: -0.04em`, `font-weight: 700`
- `h2`: uppercase, `letter-spacing: -0.03em`, `font-weight: 700`
- Labels / eyebrows: `JetBrains Mono`, `10px`, `letter-spacing: 0.14em`, uppercase
- Stat values: `JetBrains Mono`, `font-weight: 700`, copper or teal color

## HTML + Tailwind CDN Pattern

```html
<script src="https://cdn.tailwindcss.com"></script>
<script>
  tailwind.config = {
    theme: {
      extend: {
        colors: {
          copper: { 400:'#f5a66b', 500:'#f09247', 600:'#e07838', 700:'#c86528' },
          teal:   { 400:'#38d4d4', 500:'#20c9c9', 600:'#14aeae', 700:'#0d9090' },
        },
        fontFamily: {
          sans: ['Space Grotesk','system-ui','sans-serif'],
          mono: ['JetBrains Mono','monospace'],
        },
      }
    }
  }
</script>
```

`tailwind.config` must go in a separate `<script>` tag **after** the CDN `<script>`. The CDN's JIT engine re-processes the DOM when the config is set.

For brand-slate colors (which differ from Tailwind's built-in slate), define them as CSS custom properties in `:root` and write explicit utility classes rather than relying on Tailwind class generation. Opacity variants like `rgba(224,120,56,0.1)` must be written as literal values — the CDN cannot generate opacity modifiers for custom CSS-variable colors.

## Scroll Animations

Use `IntersectionObserver` with CSS transitions. Standard pattern used across revisions:

```css
.reveal { opacity:0; transform:translateY(24px); transition:opacity .7s ease-out,transform .7s ease-out; }
.reveal.in { opacity:1; transform:none; }
```

```js
const io = new IntersectionObserver(
  entries => entries.forEach(e => { if (e.isIntersecting) { e.target.classList.add('in'); io.unobserve(e.target); } }),
  { threshold: 0.12, rootMargin: '0px 0px -40px 0px' }
);
document.querySelectorAll('.reveal').forEach(el => io.observe(el));
```

Hero elements use CSS `@keyframes fade-up` with `animation-delay` staggering on `:nth-child()` — not IntersectionObserver — so they fire immediately on load.

## Section Structure (V2 reference)

1. **Hero** — fullscreen, blueprint grid bg, copper glow blob, star particles, dual CTAs, stat strip, backed-by strip
2. **Dual Audience** — two-panel grid: copper (professionals) / teal (enterprises), each with feature points + CTA button
3. **Social Proof Dashboard** — 6 stat tiles (2×3 grid), context annotation strips, partner logo placeholders, Google reviews placeholder
4. **AI Tools** — 3 cards: TIQ Wiki (teal), AI Report Studio (copper, "FEATURED"), AI Assessment Engine (copper)
5. **Manifesto** — fullscreen dark, dual glow blobs, star particles, "Human Intelligence amplified by AI" headline, 3 principle cards
6. **Ecosystem** — 6-card grid: Verified Professionals, Certified Training, Marketplace, Contract Opportunities, AI Report Studio, Career Ladder
7. **Trust + Footer** — institutional logos (IIT Madras, CNDE, IITM Incubation), cert badges, stats row, final CTA band, 3-column footer nav

## Assets

Logo files are in `F:\IIT-RCOM\tiqworld-github\consumer-app\public\logos\`. Since the HTML files run standalone (not served by Vite), reference logos either as absolute paths for local testing or use text/SVG placeholders for portable demos.
