# CLAUDE.md — plant-profile

## Project Overview

**plant-profile** is a static, client-side web application for displaying and managing staghorn fern (Platycerium) growth profiles. It is one half of a two-repo system:

- **`plant-pos`** — the upload/data-entry tool (separate repo, not present here)
- **`plant-profile`** (this repo) — the customer-facing gallery and admin management UI

The site is deployed on Vercel at https://plant-profile.vercel.app and backed by a Supabase (PostgreSQL + Storage) project.

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Language | Vanilla HTML5 + CSS3 + ES6+ JavaScript |
| Build system | None — static files served as-is |
| Backend / DB | Supabase (PostgreSQL REST API + Storage) |
| AI analysis | Google Gemini 1.5 Flash (implemented in `plant-pos`) |
| Video encoding | Web Codecs API + mp4-muxer 5.1.3 (CDN) |
| Charts | Chart.js 4.4.0 (CDN) |
| Deployment | Vercel (with URL rewriting in `vercel.json`) |

There is **no package.json, no Node.js, no bundler, no TypeScript, and no test suite.**

---

## File Structure

```
plant-profile/
├── index.html        # Landing page — 4 navigation cards
├── plants.html       # Plant gallery grid + customer login portal
├── plant.html        # Individual plant detail page (main UI)
├── admin.html        # Backend admin panel (plant status + customer management)
├── shop.html         # Product shop with admin overlay
├── collection.html   # Placeholder ("Coming soon")
├── vercel.json       # URL rewriting: /plant/:rid → /plant.html
└── ROADMAP.md        # Architecture notes and feature backlog (Chinese/Vietnamese)
```

All application logic lives **inline** inside each HTML file (`<style>` and `<script>` blocks). There are no external `.css` or `.js` source files.

---

## How to Run Locally

No build step is needed. Choose any method:

```bash
# Python
python -m http.server 8000

# Node.js
npx serve .

# Then open http://localhost:8000
```

Or open any `.html` file directly in a browser via `file://`.

---

## Supabase Database Schema

All DB access uses the Supabase REST API with an anon key (hardcoded in JS — safe, as Supabase Row Level Security governs access).

### Key Tables

| Table | Purpose |
|-------|---------|
| `rehab` | Core plant records (`rid`, `plant_id`, `plant_name`, `purchase_date`, `notes`, `description`) |
| `plant_web` | Web display layer: `rid` (PK), `web_status`, `web_owner`, `customer_id`, `owner_label`, `updated_at` |
| `photos` | Photo records: `id`, `rid`, `photo_url`, `storage_path`, `taken_at`, `angle`, `session_date`, `weight_g`, `note`, `is_featured`, `zone` (`timeline`/`timelapse`), `approved` |
| `videos` | Videos: `id`, `rid`, `video_url`, `title`, `period_start`, `period_end`, `video_type`, `is_displayed`, `created_at` |
| `ai_analysis` | AI scores: `id`, `rid`, `analysis_date`, `health_score`, `growth_score`, `beauty_score`, `summary` |
| `customers` | Customer accounts: `id`, `name`, `password`, `notes`, `created_at` |
| `customer_plants` | Plant–customer mapping: `id`, `customer_id`, `rid` |
| `products` | Shop catalog: `id`, `name`, `category`, `price`, `description`, `photo_url`, `is_displayed`, `sort_order` |
| `plants` | Species/pricing reference: `id`, `name`, `price` |

### API Helpers

Every file defines two shared helpers at the top of its `<script>`:

```javascript
const SUPABASE_URL = 'https://edvklxnzhuhmijdwyzrl.supabase.co';
const SUPABASE_KEY = '<anon-key>';          // Safe to expose client-side
const ADMIN_PWD    = 'roots';              // Simple admin gate

async function dbGet(table, query = '') {
  const res = await fetch(`${SUPABASE_URL}/rest/v1/${table}${query}`, {
    headers: { apikey: SUPABASE_KEY, Authorization: `Bearer ${SUPABASE_KEY}` }
  });
  return res.json();
}

async function dbPatch(table, id, data) {
  await fetch(`${SUPABASE_URL}/rest/v1/${table}?id=eq.${id}`, {
    method: 'PATCH',
    headers: { apikey: SUPABASE_KEY, Authorization: `Bearer ${SUPABASE_KEY}`,
               'Content-Type': 'application/json', Prefer: 'return=minimal' },
    body: JSON.stringify(data)
  });
}
```

---

## Page-by-Page Guide

### `plant.html` (1700+ lines — core page)
- Reads `?rid=` from the URL query string to identify the plant
- Loads photos, weight data, AI scores, and videos from Supabase
- Displays a large photo viewer with two-angle support (front/back)
- Renders a growth timeline and Chart.js weight graph
- Provides an admin panel (password-gated) for featured photo/video management
- Generates MP4 timelapse videos client-side using Web Codecs + mp4-muxer
- Supports customer login to view ownership info

### `plants.html` (470+ lines)
- Gallery grid of all plants with `web_status = 'active'`
- Filters by status (for sale, sold, not for sale)
- Customer login modal: password-matches against `customers` table, stores session in `localStorage`
- Logged-in customers see their owned plants highlighted

### `admin.html` (740+ lines)
- Password-gated (same `ADMIN_PWD`)
- Manage `plant_web` status per plant (active/inactive/hidden)
- Add/edit customers, assign plants to customers
- Bulk-save via Supabase PATCH

### `shop.html` (410+ lines)
- Customer-facing product catalog grouped by category
- Admin overlay to add/edit/reorder products
- Facebook link for contact

### `index.html` (90 lines)
- Static hero with four navigation cards

---

## Key Conventions

### Code Style
- No external build tooling; all JS/CSS is inline in `<script>` and `<style>` tags
- ES6+ async/await for all API calls — no callbacks or `.then()` chains
- CSS custom properties (`--cream`, `--green`, `--gold`, `--dark-green`, etc.) define the color palette; **do not introduce hardcoded colors**
- Prefer editing the smallest section of code needed — avoid touching unrelated functions
- No comments unless the reason (not the what) is non-obvious

### Supabase Queries
- Use the `dbGet` / `dbPatch` helpers that already exist in each file
- Filter syntax follows PostgREST: `?col=eq.value&order=col.asc`
- For inserts, use a raw `fetch` with `method: 'POST'` mirroring the existing PATCH pattern
- Always set `approved=eq.true` when querying `photos` for customer-facing display

### Routing
- The `plant.html` page is the only route with a dynamic segment (`/plant/:rid`)
- `vercel.json` rewrites `/plant/:rid` → `/plant.html`; the page reads `rid` from `new URLSearchParams(location.search).get('rid')`
- All other pages are addressed directly by filename

### Authentication
- Admin: compare input against `ADMIN_PWD = 'roots'` — no server-side auth
- Customer: fetch matching row from `customers` table by name+password, store `{id, name}` in `localStorage` under key `customerSession`
- No JWT, no sessions beyond localStorage

### Internationalization
- The app targets three locales: English, Traditional Chinese (繁體中文), Vietnamese
- Labels are defined as JS objects or CSS variables per file; follow the same pattern when adding new text

### Video / Media
- Photos are stored in Supabase Storage bucket `plant-media`
- Timelapse videos are encoded client-side (Web Codecs H.264) and can be uploaded back to Supabase Storage
- `is_featured` flag on `photos` and `is_displayed` flag on `videos` control what appears on the plant detail page

---

## Deployment

```bash
# Push to main → Vercel auto-deploys (no build step)
git push origin main
```

`vercel.json`:
```json
{
  "rewrites": [{ "source": "/plant/:rid", "destination": "/plant.html" }]
}
```

No environment variables are configured in Vercel — all config is hardcoded in the HTML files.

---

## Development Workflow

1. Edit the relevant `.html` file directly
2. Test in a local browser (`python -m http.server 8000` or similar)
3. Verify Supabase reads/writes work against the live DB (no staging DB exists)
4. Commit and push to trigger Vercel deployment

There are **no linters, formatters, or pre-commit hooks** to run.

---

## Feature Roadmap (from ROADMAP.md)

- AI prompt refinement for Platycerium health/beauty scoring
- Multi-photo trend analysis → growth prediction graphs
- Timeline video UI improvements (player controls)
- Weight measurement protocol (3-point: post-water, morning, pre-water)
- Expanded customer portal features
