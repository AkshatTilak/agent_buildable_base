# Subtask: ST-11 — B2C Family Search Portal (Next.js)

**Parent Link:** [BT-04: Client Applications & Shared Mobile Shell](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/base/BT-04_client_surfaces.md)

## Objective
Build the Next.js public B2C portal (`apps/web-family/`) providing SEO-heavy discovery, interactive PostGIS map interfaces, natural-language semantic search, side-by-side facility comparisons, and tour booking request forms.

---

## Technical Actionable Steps

- [ ] **Step 1: Bootstrap Next.js App Shell (`apps/web-family/`)**
  - Next.js 14+ App Router setup, React 18, Vanilla CSS design system tokens.
  - Setup Google Fonts (Inter / Outfit), SEO metadata framework, title tags, and OpenGraph schemas.

- [ ] **Step 2: Build Hybrid Search & Filter Interface**
  - Search bar with natural language AI input + location autocomplete.
  - Interactive map integration (Mapbox / Leaflet) synchronized with PostGIS spatial results.
  - Filter drawer: Care types, pricing slider, amenities, staffing ratio min, CMS rating min.

- [ ] **Step 3: Build Facility Detail & Comparison Pages**
  - Dynamic page `/facility/[slug]` displaying ratings, staff ratios, CMS inspection history, photos, and tour request drawer.
  - Multi-facility comparison page `/compare?ids=1,2,3` invoking `ai-agent-service` comparative summaries.

- [ ] **Step 4: Build Lead & Tour Request Forms**
  - Interactive tour scheduling modal connecting to `leads-service`.

---

## Dependencies
- Completion of `ST-03_taxonomy_and_common_packages.md` and `ST-07_search_hybrid_engine.md`.

## Complexity Rating
**High**

## Definition of Done
- `web-family` builds without TypeScript or SSR errors.
- Search page renders interactive map pins and filters facilities smoothly.
- Facility detail pages load SEO meta tags and launch tour booking modals.
