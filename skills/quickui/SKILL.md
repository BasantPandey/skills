---
name: quickui
description: Generate 3 quick UI design variations (Clean & Minimal, Bold & Vibrant, Dark & Sophisticated) as self-contained HTML/CSS pages from a natural language description. Opens a single gallery tab with inline iframe previews so the user can compare designs and click to fullscreen. Trigger when user invokes /quickui or asks for quick UI design variations/POC designs.
---

Generate 3 distinct HTML/CSS UI design variations from the user's description. Follow every step below exactly.

## Step 1 — Parse the prompt

Extract from the user's message:
- The **page type** (login, dashboard, landing page, settings, checkout, etc.)
- The **domain/industry** if mentioned (fintech, healthcare, SaaS, e-commerce, etc.)
- Any **specific components** mentioned (table, form, chart, hero, cards, etc.)

## Step 2 — Create output directory

Create a timestamped output folder:
```
./ui-designs/YYYY-MM-DD-HHmmss/
```
Inside it, create:
- `design-1-minimal.html`
- `design-2-vibrant.html`
- `design-3-dark.html`
- `index.html` (gallery)

## Step 3 — Generate 3 design HTML files

Each file must be a **complete, full-page HTML/CSS document** with:
- A `<nav>` / header
- A main content area appropriate to the page type (form, cards, table, hero+sections, etc.)
- A `<footer>`
- CSS entrance animations on page load
- CDN links for Google Fonts and Lucide icons (`https://unpkg.com/lucide@latest/dist/umd/lucide.min.js`)

### Archetype 1 — Clean & Minimal (`design-1-minimal.html`)

**Typography:** Google Font `Inter`. Body 16px, headings font-weight 300–400, generous line-height 1.7. Letter-spacing 0.01em on headings.

**Color & Theme:** Background `#ffffff` or `#f8f9fa`. Primary accent `#6366f1` (indigo). Text `#1a1a1a`. Borders `#e5e7eb`. Muted text `#6b7280`.

**Motion:** CSS keyframe `fadeIn` — `opacity: 0 → 1, transform: translateY(8px) → translateY(0)` over 0.5s ease-out. Stagger child elements with `animation-delay` increments of 0.1s.

**Spatial Composition:** Generous padding (48px+ sections). Max-width 1100px centered. 8-column grid. Cards with 24px padding, `border-radius: 12px`, subtle `box-shadow: 0 1px 3px rgba(0,0,0,0.08)`.

**Backgrounds & Visual Details:** Solid white. Thin 1px borders. No gradients. Small dot or line separator accents.

---

### Archetype 2 — Bold & Vibrant (`design-2-vibrant.html`)

**Typography:** Google Font `Poppins`. Headings font-weight 700–800, tight `letter-spacing: -0.03em`. Body `Plus Jakarta Sans` or fallback to Poppins regular.

**Color & Theme:** Strong gradient header `linear-gradient(135deg, #f97316, #ec4899)`. White content area. Accent `#f97316`. Text `#111827`. Buttons: filled gradient, rounded-full.

**Motion:** CSS keyframe `slideUp` — `opacity: 0 → 1, transform: translateY(24px) → translateY(0), scale(0.97) → scale(1)` over 0.45s cubic-bezier(0.34, 1.56, 0.64, 1). Stagger with 0.08s delays.

**Spatial Composition:** Compact, energetic. 16px base padding. Cards in a tight 3-column grid. Bold section headings. Colorful tag/badge chips. Visible section dividers with color.

**Backgrounds & Visual Details:** Hero section with bold gradient. Cards with colored left-border accent `4px solid`. Icon backgrounds with soft tinted circles. SVG wave divider between sections.

---

### Archetype 3 — Dark & Sophisticated (`design-3-dark.html`)

**Typography:** Google Font `Sora`. Headings font-weight 600, `letter-spacing: -0.02em`. Body `Space Grotesk` or fallback Sora. Light text on dark.

**Color & Theme:** Background `#0f0f13`. Surface `#1a1a24`. Accent `#a78bfa` (violet) with glow. Text `#e2e8f0`. Muted `#64748b`. Borders `rgba(255,255,255,0.08)`.

**Motion:** CSS keyframe `glowFadeIn` — `opacity: 0 → 1, filter: blur(4px) → blur(0)` over 0.6s ease-out. Key elements also get a subtle `box-shadow` glow pulse animation (2s infinite alternate) using the accent color.

**Spatial Composition:** Dramatic spacing. Section padding 80px+. Full-width dark hero. Cards with glassmorphism: `background: rgba(255,255,255,0.04); backdrop-filter: blur(12px); border: 1px solid rgba(255,255,255,0.08)`.

**Backgrounds & Visual Details:** Radial gradient spotlight on hero: `radial-gradient(ellipse 80% 50% at 50% 0%, rgba(167,139,250,0.15), transparent)`. Subtle grid texture overlay on hero. Dark footer with slightly lighter surface.

---

## Step 4 — Generate the gallery index.html

Create `index.html` with this structure:
- Header: "QuickUI — 3 Design Variations" + the user's original prompt in a subtitle
- 3 iframe cards side by side (CSS grid, 1fr 1fr 1fr)
- Each card has: archetype name badge, description line, the iframe (height ~500px, border-radius 12px, overflow hidden), and a "Open Fullscreen" button that opens the design file in a new tab
- A "Design Dimensions" footer note listing: Typography · Color & Theme · Motion · Spatial Composition · Backgrounds
- Dark neutral background (`#111`) for the gallery itself so all 3 iframes are visually comparable
- The iframes use `src="./design-1-minimal.html"` etc. (relative paths)
- "Open Fullscreen" buttons: `<a href="./design-1-minimal.html" target="_blank">`

## Step 5 — Open in browser

After writing all files, open the gallery using the appropriate shell command:

**Windows (PowerShell):**
```powershell
Start-Process (Resolve-Path "./ui-designs/<timestamp>/index.html")
```

**macOS:**
```bash
open ./ui-designs/<timestamp>/index.html
```

**Linux:**
```bash
xdg-open ./ui-designs/<timestamp>/index.html
```

## Step 6 — Report to user

After opening, print a short summary:
```
Generated 3 UI variations for: "<user prompt>"
Saved to: ./ui-designs/<timestamp>/

  1. Clean & Minimal    → design-1-minimal.html
  2. Bold & Vibrant     → design-2-vibrant.html
  3. Dark & Sophisticated → design-3-dark.html

Gallery opened in browser. Click any design to fullscreen.
```
