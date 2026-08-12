# Subtask 14_01: Dropdown Loading States in the Workflow Editor

## Objective
Add per-dropdown loading, empty, and error states to the workflow editor's property drawer so users know when agent / collection / eval-suite / DB-credential options are still loading or failed to load.

## Root Cause
- `WorkflowEditor.tsx` `fetchLinkedHubResources()` fetches all resource types but exposes no per-resource loading or error state. The `<select>` dropdowns render with only the placeholder option while requests are in flight, and silently stay empty if a request fails.

## Tasks
1. `[x]` **Track per-resource loading/error** — add state for `agentsLoading`, `collectionsLoading`, `evalSuitesLoading`, `credentialsLoading` (and matching `agentsError`, etc. strings) in `WorkflowEditor.tsx`. Set them around each fetch call in `fetchLinkedHubResources()`, independently per resource type.
2. `[x]` **Render loading in dropdowns** — in the property drawer, when a resource list is loading, render a spinner row (`<Loader2 className="animate-spin" /> Loading agents…`) in place of the `<select>` element — do not render an empty select that misleads the user.
3. `[x]` **Render empty state** — when the resource list is empty (loaded successfully but zero items), show "No linked {resource type} available" in muted text — this must be visually distinct from the loading spinner.
4. `[x]` **Render error state with retry** — when a fetch errors, show a red error message plus a "Retry" button that re-calls `fetchLinkedHubResources()` for only that resource type.

> **Architecture note:** The three states (loading / empty / error) must be unambiguous. A user must never see an empty `<select>` with just a placeholder option and wonder whether it's loading, failed, or genuinely empty.

## Definition of Done
- Every property-drawer dropdown shows a loading spinner while its API response is pending.
- Empty and error states are clearly distinguishable from each other and from the loaded list.
- The error state includes a Retry button that re-fetches that resource type only.

## Test Cases

### Frontend Manual
| # | Scenario | Steps | Expected |
|---|----------|-------|----------|
| F1 | Loading spinner visible | Throttle network → open agent node property drawer | Spinner shown; not an empty select |
| F2 | Success state → options populate | Normal network → open property drawer after data loads | Dropdown populated with agent names |
| F3 | Empty state distinguishable from loading | Linked agent hub has 0 agents; network succeeds | "No linked agents available" text shown — no spinner |
| F4 | Error state shows retry | Simulate API 500 for agent list → open property drawer | Red error message + "Retry" button visible |
| F5 | Retry clears error state | Click Retry → network succeeds | Spinner shown → agents loaded; error cleared |
| F6 | Per-resource independence | Agents fail but collections succeed | Agent dropdown shows error; collections dropdown shows data — not both broken |

