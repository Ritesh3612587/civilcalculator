# BuildScope AI — Civil Engineering & Construction Platform

BuildScope AI is a modern civil engineering web application built according to the Apple design system (`DESIGN-apple.md`) and technical product blueprint (`BuildScope_AI_Full_Implementation_Documentation.md`).

It includes deterministic engineering calculation engines, itemized cutting schedules, dynamic SVG diagrams, interactive Phase 2–4 workspace simulators (PDF library, grounded AI Q&A with page citations, and drawing takeoffs), complete SEO optimization, and native Cloudflare Pages upload support format.

---

## 🚀 Features & Architecture

### 1. Deterministic Engineering Calculators (Phase 1)
- **Concrete Volume & Material Estimator** (`/tools/concrete-calculator`):
  - Rectangular slab, beam, column, footing, and circular pier geometry.
  - Full unit conversions (mm, cm, m, ft, in).
  - Dry volume multiplier (`1.54` standard civil engineering factor).
  - Nominal mixes: M10, M15, M20, M25, and custom ratio.
  - Outputs: Wet volume ($m^3$, $ft^3$, yards), dry volume, cement in 50 kg bags & kg, sand in tonnes & $cft$, coarse aggregate in tonnes & $cft$, water estimate in liters.
  - Live dynamic SVG member preview with dimension callouts.
  - Export calculation schedule to CSV & print view.
- **Steel Bar Weight Calculator** (`/tools/steel-weight-calculator`):
  - Nominal $d^2 / 162$ formula and precise $d^2 / 162.28$ ($\rho = 7850\text{ kg/m}^3$) solver.
  - Standard diameters (8mm, 10mm, 12mm, 16mm, 20mm, 25mm, 32mm, 40mm).
  - Multi-bar Cutting Schedule Table generator with live cumulative tonnage.
  - Export schedule to CSV.
- **Simply Supported Beam Analysis** (`/tools/beam-calculator`):
  - Solves simply supported beams under point load ($P$ at distance $a$) and full-span uniform distributed load ($w$).
  - Analytical reactions $R_A, R_B$, maximum shear $V_{\max}$, and maximum bending moment $M_{\max}$.
  - Verification check badge: static equilibrium $\sum F_y = 0$ within numerical tolerance.
  - Live dynamic SVG Shear Force Diagram (SFD) & Bending Moment Diagram (BMD).

### 2. Interactive Workspace Simulator (Phases 2–4)
- **Project Document Library** (`/app`): Drag-and-drop PDF upload simulator tracking state transitions (`uploading` → `processing` → `ready`), SHA-256 checksums, and page counts.
- **Grounded AI Document Intelligence** (`/app`): Questions answered with mandatory source citations (e.g., *Structural Specifications.pdf, page 14* for M30 column mix) and refusal to invent unreferenced clauses ("Not found in uploaded documents").
- **Drawing Takeoff Viewer** (`/app`): Interactive HTML5 drawing canvas with scale calibration (e.g. 5m wall = 250 units = 0.02 m/unit), polygon area takeoff, linear wall tracing, count markers, and CSV schedule export.

### 3. Apple Design System (`DESIGN-apple.md`)
- Typography: SF Pro Display / SF Pro Text typography hierarchy with negative letter-spacing.
- Color palette: Action blue (`#0066cc`), frosted glass navigation chrome (`backdrop-filter: blur(20px)`), edge-to-edge product cards, and dark tile contrast sections.
- Pill buttons (`border-radius: 9999px`), segmented controls, tabular numbers for engineering metrics.
- Complete responsive behavior on desktop, tablet, and mobile.
- Dedicated print stylesheet (`@media print`) for clean calculation reports.

### 4. Complete SEO & Meta
- **Meta Tags**: Optimized `<title>`, `<meta name="description">`, keywords, canonical links on every page.
- **Social Sharing**: OpenGraph (`og:title`, `og:image`, `og:url`) and Twitter Summary Cards.
- **Structured Data (JSON-LD)**: Schema.org markup for `SoftwareApplication`, `BreadcrumbList`, `Organization`, and `FAQPage`.
- **Search Engine Discovery**: Comprehensive `sitemap.xml` with priority weightings and `robots.txt`.
- **PWA Manifest**: `manifest.json` with theme color `#0066cc` and SVG vector icons.

---

## ☁️ Cloudflare Upload & Deployment Format

This repository is formatted for instant deployment to **Cloudflare Pages**:

### Files included for Cloudflare Pages:
1. `_headers`: Defines enterprise HTTP security headers (`Strict-Transport-Security`, `X-Frame-Options: DENY`, `X-Content-Type-Options: nosniff`, `Referrer-Policy`, and CSP) plus 1-year immutable caching for `/css/*`, `/js/*`, and `/assets/*`.
2. `_redirects`: Configures clean URLs, 301 redirects from `.html` extensions to directory slugs, and 200 rewrites.
3. `_routes.json`: Defines Cloudflare routing scopes.
4. `wrangler.toml`: Official Cloudflare deployment configuration file.
5. Directory & File structure: All routes exist both as subdirectories with `index.html` (e.g. `/tools/concrete-calculator/index.html`) and as flat files (e.g. `concrete-calculator.html`) to guarantee 100% zero-404 compatibility across any deployment method.

### How to Deploy:

#### Option A: Direct Drag-and-Drop (No CLI Needed)
1. Go to the [Cloudflare Pages Dashboard](https://dash.cloudflare.com/?to=/:account/pages).
2. Click **Create a project** → **Direct Upload**.
3. Name your project (e.g., `buildscope-ai`).
4. Drag and drop this folder (`c:\Users\Asus\Desktop\civil tools`) into the upload zone.
5. Click **Deploy site**.

#### Option B: Deploy via Wrangler CLI
Run the following in your terminal:
```bash
npx wrangler pages deploy . --project-name=buildscope-ai
```

#### Option C: Local Development Preview
Test the site locally on port 3000:
```bash
npx serve . -l 3000
```
or with Wrangler:
```bash
npx wrangler pages dev .
```

---

## ⚖️ Engineering Governance
See [`/engineering-disclaimer`](engineering-disclaimer/index.html) for our Appendix A limitation of liability notice. All calculations require independent verification by a qualified professional engineer before being used in safety-critical construction.
