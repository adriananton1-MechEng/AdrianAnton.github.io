# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Site owner

- **Adrian Florin Anton** — Mechanical Engineer at **Agora Robotics** (Bucharest, since 08/2023), reporting to the Head of Engineering / CTO.
- **PhD candidate** at Politehnica University of Bucharest on *Flexible Mechanisms in Robot–Environment Interaction*. MSc Advanced Mechatronics (valedictorian, GPA 10.00/10). BSc Precision Mechanics & Nanotechnologies.
- Previous work: Segula Technologies (Stellantis automotive, CATIA/ENOVIA, 2022–2023) and INCDMTM (CNC/EDM/SLS/SLM intern, 2021).
- Core stack: Fusion 360, AutoCAD, FDM/SLA, CO₂ laser (80 W), silicone casting, vacuum forming, TIG welding, ANSYS, MATLAB, GD&T.
- Communicates often in Romanian — reply in the same language unless he switches.
- **Auto-commit and push to `main` after every change is pre-authorised** — no need to ask. GitHub Pages auto-deploys in ~30s.

## Featured project — Sweep (Agora Robotics)

Sweep is the commercial autonomous indoor floor-scrubbing robot Adrian owns the mechanical architecture of. Nine hardware generations to date (D1 → D8 + E1 small-series), plus an IPX6-certified docking station. Real deployments at Orange HQ, IKEA, Dedeman Central Warehouse, Stejari Residence. Source PDFs live in `images/projects/robot/` — **content marked in red in those documents is internal and must not be published** (workspace size, lack of formal welding certifications, internal cost numbers).

## Overview

Static HTML5 portfolio website for an engineering professional (mechanical/mechatronics). No build tools, no framework, no dependencies — pure HTML, CSS, and vanilla JS deployed to GitHub Pages.

## Development

**No build step.** To preview locally, serve the root directory with any HTTP server:

```bash
python -m http.server 8000
# or
npx http-server
```

**Deployment**: push to `main` → GitHub Pages auto-deploys.

## File Structure

```
index.html              # Main portfolio (hero, projects grid, skills, hobbies, contact)
cv.html                 # Interactive CV (dark theme + theme toggle)
cv-print.html           # Standalone print/PDF-ready CV (light theme, no toggles, A4)
admin.html              # Single-page admin — edit any page via GitHub Contents API (PAT auth)
project-robot.html      # Sweep — Agora Robotics (D1→E1, 9 hardware generations)
project-microscope.html # Microscope Inspection Table (MSc thesis)
project-cad.html        # CAD Assembly project
project-rover.html      # Rocker-Bogie Rover (BSc thesis)
project-mechanical.html # Mechanical design project
images/                 # hero, cv, projects/{rover,cad,microscope,robot,mechanical}
```

## Admin tooling

`admin.html` provides an in-browser editor for all pages — content edits commit back via the GitHub Contents API using a fine-grained Personal Access Token (Contents: Read and Write on this repo). The token is stored only in the user's browser localStorage. The editor injects into a sandboxed iframe of each page, exposes a right-side control panel for highlight toggles, project-card color variants, duplicate/delete/reorder of pills/tags/cards, and typography controls (font, size, weight, italic/underline/uppercase, colour, padding/margin). Implementation notes: never call `active.focus()` from the iframe on `admin:panel-interact` — it steals focus from panel controls. The user is the only person with the token; treat the admin as a single-user tool.

## Architecture

### Design System

All pages share CSS custom properties defined in `:root`:

```css
--bg: #0a0c0f;        /* dark navy */
--accent: #00d4ff;    /* cyan */
--accent2: #ff6b35;   /* orange */
--text: #e8edf2;
--muted: #5a6a7a;
--mono: 'Share Tech Mono', monospace;
--display: 'Syne', sans-serif;
```

There is no shared stylesheet — each page embeds its full CSS in a `<style>` block. When adding new pages, copy and paste the full CSS from an existing page and adjust only what's needed.

### Navigation Pattern

- `index.html`: fixed nav with anchor links (`#projects`, `#skills`, `#contact`)
- Project/CV pages: nav contains a "back" link to `index.html`
- No client-side router — multi-page by plain HTML links

### Animations

Two JavaScript patterns handle all interactivity:

1. **Fade-in on scroll**: Elements with class `.fade-in` are observed via `IntersectionObserver`; the class `.visible` is added when they enter the viewport (threshold: 0.12). The observer unobserves after triggering (one-shot).

2. **Skill bar animation**: Bars carry `data-width="0.90"` (0–1 float). The observer sets `element.style.width` to `${value * 100}%` on viewport entry, triggering a CSS transition.

### Project Cards

Cards on `index.html` use class `.project-card`. The MSc thesis card uses `.project-card--purple` to override the accent color to `--purple: #a770ff`. The `.featured` modifier styles the first/highlighted card differently.

### Background Grid

The grid texture is a fixed pseudo-element (`body::before`) using a double `linear-gradient`, not CSS Grid layout.

### Responsive Breakpoints

- `900px`: two-column CV layout collapses to one column
- `768px`: navigation, hero font sizes, section padding adjust

## Content Conventions

- All content is hardcoded — no CMS or data files.
- Contact links use `mailto:` and `tel:` hrefs; no form backend.
- SVG icons in project cards are inline and decorative (opacity 0.15–0.25).
- The blinking green status dot uses a CSS `@keyframes` box-shadow pulse.
