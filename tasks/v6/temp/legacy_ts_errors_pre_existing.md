# Pre-existing TS Build Errors in Legacy V5 Components

**Discovered during:** S6-08a (Router Restructure)  
**Date:** 2026-07-30  
**Severity:** Low — build fails but only due to legacy code scheduled for B6-11 removal.

## Errors

| File | Error | Code |
|---|---|---|
| `frontend/src/components/PropertyDrawer.tsx:16` | `Property 'activeWorkflow' does not exist on type 'StoreState'` | TS2339 |
| `frontend/src/components/PropertyDrawer.tsx:19` | `Parameter 'n' implicitly has an 'any' type` | TS7006 |
| `frontend/src/components/WorkflowCanvas.tsx:269` | `Cannot find name 'setActiveWorkflow'. Did you mean 'setSavedWorkflows'?` | TS2552 |
| `frontend/src/services/api.ts:176` | `Cannot find name 'str'` | TS2304 |

## Root Cause

These files were part of the flat V5 frontend. The store slice for `activeWorkflow`/`setActiveWorkflow` was removed or renamed during a prior session. They are not imported in the new V6 App.tsx.

## Resolution

All four errors will be resolved when B6-11 removes these legacy components outright. No action needed before then. The V6 route tree in App.tsx does **not** import any of these files so they have no runtime impact.
