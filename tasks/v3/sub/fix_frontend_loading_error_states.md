# Subtask: Fix Frontend Loading & Error States

**Parent Link:** `base/01_bugfixes_breaking_flows.md`

## Actionable Steps
- [x] Create `frontend/src/components/shared/LoadingSkeleton.tsx` — reusable shimmer skeleton (card, row, chart variants).
- [x] Create `frontend/src/components/shared/ErrorBanner.tsx` — dismissible error banner with retry button and timestamp.
- [x] `SystemMetrics.tsx`: Show skeleton cards while `systemHealth` is null. Show error banner if fetch fails.
- [x] `AgentHub.tsx`: Show skeleton grid while `isLoading` is true. Show meaningful empty state with illustration when `agents.length === 0`.
- [x] `EvalPanel.tsx`: Show skeleton chart while loading. Show error state if eval API is unreachable.
- [x] `WorkflowCanvas.tsx`: Show loading state while workflow data loads.
- [x] `IngestionPanel.tsx`: Show upload progress bar during file upload. Show error toast if ingestion fails.
- [x] Add global error boundary component wrapping `App` to catch React rendering errors.

## Dependencies
- Design system tokens must be implemented first (`sub/design_css_tokens.md`).

## Definition of Done
- Every panel shows a skeleton loader when data is being fetched.
- Every panel shows an error banner when the backend is unreachable.
- No panel ever shows stale/zero default data without indication.
