# Subtask: Ingestion Advanced Settings Tabbed Interface Redesign

**Parent Link:** `base/06_ingestion_documents_ux.md`

## Actionable Steps
- [ ] In `IngestionPanel.tsx`, refactor the monolithic advanced parameters block into a tabbed layout:
  - **Tab 1: Chunking Strategy** (Strategy selector: Hybrid/Recursive/Semantic, Chunk Size slider, Chunk Overlap slider)
  - **Tab 2: Pre-Processing** (Layout OCR toggle, OCR provider selector, Noise reduction toggle)
  - **Tab 3: Post-Processing** (Entity extraction toggle, Summary generation toggle, Graph entity linking)
- [ ] Style tabs according to the V3 design system: active tab highlight border, soft accent background.
- [ ] Save strategy preferences to `ingestionSlice` so selections persist across component remounts.
- [ ] Add tooltips/info icons explaining each strategy and parameter option.

## Dependencies
- `sub/design_css_tokens.md`, `sub/state_new_zustand_slices.md`.

## Definition of Done
- Advanced parameters organized cleanly into 3 tabs.
- Parameter changes correctly reflect in payload sent to `/api/syntraflow/ingest`.
- Strategy selections persist in `ingestionSlice`.
