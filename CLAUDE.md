# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Site owner

- **Adrian Florin Anton** — Mechanical Engineer at **Agora Robotics** (Bucharest, since 08/2023), reporting to the Head of Engineering / CTO.
- **PhD candidate** at Politehnica University of Bucharest on *Flexible Mechanisms in Robot–Environment Interaction*. MSc Advanced Mechatronics (valedictorian, GPA 10.00/10). BSc Precision Mechanics & Nanotechnologies.
- Previous work: Segula Technologies (Stellantis automotive, CATIA/ENOVIA, 2022–2023) and INCDMTM (CNC/EDM/SLS/SLM intern, 2021).
- Core stack: Fusion 360, AutoCAD, FDM/SLA, CO₂ laser (80 W), silicone casting, vacuum forming, TIG welding, ANSYS, MATLAB, GD&T.
- Communicates often in Romanian — reply in the same language unless he switches.
- **Auto-commit and push to `main` after every change is pre-authorised** — no need to ask. GitHub Pages auto-deploys in ~30s.

## Maintenance policy for this file

This file is a living document. Two standing rules:

1. **Auto-document recurring workflows.** Any multi-step procedure performed more than once (DNG extraction, adding a hobby sub-project, fixing a stale role/footer, etc.) should be captured as a copy-pasteable template under *Recurring workflows* below — without waiting to be asked. Future Claude sessions read this file first; the work should not have to be re-derived.
2. **Periodically audit for staleness.** When starting a fresh `/clear`-ed session, or after several substantive working sessions, re-read this file against the current working tree. Update file-structure listings, refresh version-specific facts (current Sweep generation, LinkedIn handle, etc.), prune notes that no longer apply, and consolidate workflows that have evolved.

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
project-vacuumforming.html # Custom large-format vacuum forming machine (900×1200 mm, 6 heat zones — CAD-designed + built)
project-rover.html      # Rocker-Bogie Rover (BSc thesis)
project-hobbies.html    # Hobby gallery (3D-print art, demo stands, custom prints)
images/                 # hero, cv, projects/{rover,vacuumforming,microscope,robot,hobby}
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

Cards on `index.html` use class `.project-card` plus an optional colour-variant modifier:

| Variant class                | Accent     | Used for                            |
|------------------------------|------------|-------------------------------------|
| (default — cyan)             | `#00d4ff`  | Rocker-Bogie Rover (BSc thesis)     |
| `.project-card--purple`      | `#a770ff`  | Microscope Inspection Table (MSc)   |
| `.project-card--orange`      | `#ff6b35`  | SolidWorks Assembly                 |
| `.project-card--lime`        | `#a8ff78`  | Personal Works (hobby gallery)      |
| `.project-card--amber`       | `#f59e0b`  | Sweep — Agora Robotics              |

The `.featured` modifier draws a full-height accent bar down the left edge (used on the Agora card to draw the eye first). Each variant also has a matching `.tag--<colour>`, `.card-num--<colour>`, `.card-cta--<colour>` and `.card-img-placeholder--<colour>` helper class — keep these in sync if a new variant is ever added.

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

## Recurring workflows

Templates for procedures that come up more than once. Add new ones here when patterns emerge; don't let them stay tribal knowledge.

### 1. Pre-push hygiene (admin commits via API)

Edits made through `admin.html` commit directly to GitHub via the Contents API. That means after even a brief gap, `origin/main` may be ahead of the local working copy. Before every local `git push`, run:

```bash
git -C "<repo>" pull --rebase origin main
```

If git refuses with *"cannot pull with rebase: You have unstaged changes"*, stage your work first (`git add <files>`), then commit, then `git pull --rebase`, then `git push`. The expected workflow each iteration:

```bash
git add <files>
git commit -m "..."
git pull --rebase origin main   # optional but safe; needed if remote is ahead
git push origin main
```

### 2. Image-path & filename conventions (hobby galleries)

The hobby gallery (`project-hobbies.html`) uses photo number as ordering:

- **`1.jpg` is always the card cover.** Number remaining photos `2.jpg`, `3.jpg`, … in the desired carousel order.
- **URL-encode spaces** in `src` attributes (`%20`). Commas and parentheses in folder names can stay literal — modern browsers handle them.
- **RAW files (`.DNG`, `.NEF`, `.CR2`) are not directly referenced** from `<img>`. Convert them with the DNG recipe below and keep the RAW on disk as the source of truth.
- Per-page image folders live under `images/projects/<page>/`. Hobby sub-projects nest one level deeper: `images/projects/hobby/<category>/<subproject>/`.

### 3. Adding a sub-project to the hobby picker

`project-hobbies.html` supports two card modes:

- **Flat carousel** — `data-images='["url1","url2",…]'` on the `.gallery-card`.
- **Picker → carousel** — `data-projects='[ {…}, {…} ]'` on the `.gallery-card` plus `data-tag`, `data-title`, `data-desc` for the picker header.

To add a new sub-project to an existing picker card, append an object to its `data-projects` JSON array:

```json
{
  "title": "Short project name",
  "desc": "What it is and how it was built — 1–2 sentences.",
  "cover": "images/projects/hobby/<cat>/<sub>/1.jpg",
  "images": [
    "images/projects/hobby/<cat>/<sub>/1.jpg",
    "images/projects/hobby/<cat>/<sub>/2.jpg"
  ]
}
```

Empty `"images": []` renders the tile as "Photos coming soon" (non-clickable placeholder). Cover defaults to the first image if `"cover"` is empty.

To **convert a flat-carousel card into a picker**, swap `data-images` for `data-projects` and add the `data-tag` / `data-title` / `data-desc` attributes — the JS detects which mode to use automatically.

### 4. Converting DNG / RAW photos to web-ready JPG

Browsers can't render `.DNG` (Adobe RAW) directly. Most DNG files contain one or more embedded JPEG previews (small thumbnail + near-full-resolution preview). Extracting the largest embedded JPEG is the fastest no-dependency path. Run this from the repo root (works in Git Bash on Windows with the system Python — no extra packages):

```bash
python -c "
import sys
dng_path = r'<ABSOLUTE\\PATH\\TO\\file.DNG>'
out_path = r'<ABSOLUTE\\PATH\\TO\\file.jpg>'
with open(dng_path, 'rb') as f:
    data = f.read()
print('DNG size:', len(data), 'bytes')

jpegs = []
i = 0
while i < len(data) - 1:
    if data[i] == 0xFF and data[i+1] == 0xD8:
        start = i
        j = i + 2
        while j < len(data) - 1:
            if data[j] == 0xFF and data[j+1] == 0xD9:
                jpegs.append((start, j + 2, j + 2 - start))
                i = j + 2
                break
            j += 1
        else:
            break
    else:
        i += 1

if not jpegs:
    print('No JPEG preview found in DNG.')
    sys.exit(1)

print('Found', len(jpegs), 'JPEG segment(s). Sizes:', [s for _,_,s in jpegs])
largest = max(jpegs, key=lambda x: x[2])
start, end, size = largest
with open(out_path, 'wb') as f:
    f.write(data[start:end])
print('Extracted largest JPEG (', size, 'bytes ) to:', out_path)
"
```

Conventions:

- **Keep the original DNG** on disk next to the extracted JPG — the RAW stays as source of truth for any future re-export (Lightroom / darktable / Photoshop). Only the `.jpg` is referenced in HTML.
- **Name the output the same as the DNG** but with `.jpg` (e.g. `3.DNG` → `3.jpg`). This keeps the numbered-ordering convention intact.
- The script picks the **largest embedded JPEG**, which is the near-full-res preview. The smaller match is the thumbnail.
- The extracted JPG is the camera's bundled preview — fine for the web, **not RAW-processed**. If exposure/colour is poor, re-export from Lightroom and overwrite the `.jpg`.

### 5. Normalising small things across pages

When something inconsistent shows up across more than one page (footer copyright, role title, LinkedIn handle, page title format, theme attributes), fix it everywhere in one pass:

- Grep first: `grep -n "<the bad string>" *.html`
- Apply targeted `Edit` per file (don't `Write` whole files just to change a line).
- Use the same canonical replacement everywhere so the next audit doesn't find drift.

### 6. Hobby card image cover convention

Every `.gallery-card` cover on `project-hobbies.html` follows the same rule: the cover thumbnail = the image numbered **1** in that card's folder. When adding a new card, name the chosen cover photo `1.jpg` (or `vaza 1.JPG`, `cam 1.jpg`, whatever pattern matches the folder's other files) and reference it both in the card's `<img src>` and as the cover of the first sub-project.

### 7. Keep admin.html in sync when project pages change

`admin.html` hard-codes the list of editable pages in a `PAGES` array (~line 519). **Every commit that renames, adds, or removes a `project-*.html` file (or any other top-level HTML page) must also touch `admin.html` in the same commit** so the admin sidebar stays usable. Other admin touchpoints to check when relevant:

- `ELEMENT_PATTERNS` (~line 531) — pattern → control bindings. Update if a new card variant or structural class is introduced (e.g. a new `.project-card--<colour>` modifier needs adding to the colour swatches around line 569, alongside the matching helper classes in `index.html` CSS).
- `SKIP_CLASSES` (~line 686) — classes whose direct text content is non-editable (wrappers, grids, layout shells). Add new structural classes here if they shouldn't be treated as editable text.

Audit step after any structural HTML change: `grep -n "project-" admin.html` to confirm every project page is listed and no stale name remains.
