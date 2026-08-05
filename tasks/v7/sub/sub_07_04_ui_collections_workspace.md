# Subtask 07_04: UI Collections Workspace Datastore Selector Fix

## Objective
Update the frontend Collections Workspace component to dynamically fetch connected datastores and provide visual validation status feedback.

## Tasks
1. `[x]` Refactor `CollectionsWorkspace.tsx` to fetch active datastores directly from Gateway API `/api/v1/hubs/ingestion/datastores`.
2. `[x]` Disable datastore selection options when backend service is offline or unbound.
3. `[x]` Provide visual status feedback badges (e.g., Connected, Degraded, Unreachable) in UI during collection setup.
4. `[x]` Test frontend datastore selector component flow.
