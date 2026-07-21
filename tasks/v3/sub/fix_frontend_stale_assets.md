# Subtask: Clean Up Stale Frontend Assets & SEO

**Parent Link:** `base/01_bugfixes_breaking_flows.md`

## Actionable Steps
- [x] Delete the entire contents of `App.css` — it contains Vite starter boilerplate (`.hero`, `.counter`, `#center`, `#next-steps`, `#spacer`, `.ticks`) that is completely unused by any component.
- [x] Update `index.html`:
  - Title: `ContAIned — AI Platform Control Plane`
  - Add `<meta name="description" content="ContAIned AI Platform — Autonomous document ingestion, intelligent query orchestration, and automated QA.">`
  - Add `<meta name="theme-color" content="#080809">`
  - Verify favicon path references
- [x] Remove any unused imports in existing components identified during audit.

## Dependencies
- None.

## Definition of Done
- `App.css` is either empty or deleted.
- `index.html` has proper SEO title and meta description.
- No unused CSS rules remain from Vite scaffold.
