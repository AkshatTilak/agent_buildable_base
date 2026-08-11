# Frontend UI & Dashboard Architecture (V6 ✅)

> **Source:** Updated for ContAIned V6
> **Last Updated:** 2026-07-28
> **Tenancy authority:** [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) is canonical. Where this document and `hubs.md` disagree, `hubs.md` wins.

This document details the user interface, component layout, Zustand store slices, ReactFlow canvas, and technical stack of the web client for the ContAIned AI Platform.

From V6 the entire client is organised around **Hubs**. There is no flat, globally-scoped resource plane: every collection, agent, workflow and eval suite is reached through a hub route and rendered inside the shared **Hub Shell**. Section 2 is the authoritative frontend IA; the dashboard sections that follow describe the panels that now live *inside* hub workspaces.

---

## 1. Technical Stack (V2 ✅)

### Framework, Styling & Build Tool
- **Framework:** React 19 + TypeScript (~6.0).
- **Styling:** Vanilla CSS + Tailwind CSS v4 (`@tailwindcss/vite`, `@import "tailwindcss";` in `index.css`).
- **Build Tool:** Vite 8.
- **Location:** `frontend/` at monorepo root.
- **State Management:** `zustand` (modular slices in `frontend/src/store/`: `hubSlice.ts`, `metricsSlice.ts`, `agentSlice.ts`, `ingestionSlice.ts`, `workflowSlice.ts`, `authSlice.ts`, `settingsSlice.ts`, `notificationSlice.ts`, `useStore.ts`). From V6, every hub-scoped slice keys its data by `hubId` — see §2.6.
- **Real-Time Telemetry:** `TelemetryService` (`frontend/src/services/telemetry.ts`) listening to WebSocket (`/api/telemetry/ws`) & SSE fallback.
- **Key Libraries:**
  - `@xyflow/react` — Drag-and-drop ReactFlow visual workflow builder canvas.
  - `zustand` — Modular reactive state management.
  - `recharts` — RAGAS & DeepEval score progression area charts.
  - `@tanstack/react-query` — Data fetching and caching.
  - `react-markdown` + `react-syntax-highlighter` — Chat & prompt rendering.
  - `lucide-react` — Visual iconography.

### API Client Layer
- Centralized REST client in `frontend/src/services/api.ts`.
- **V6:** hub-scoped calls target `/api/hubs/{hub_id}/...` and every such helper takes `hubId` as an explicit first argument (§2.7). Only platform surfaces (`/api/playground`, `/api/mcp`, `/api/models`, `/api/telemetry`, `/api/admin`, `/api/auth`) remain flat.
- Automatic fallback and human-readable error alerts.
- Base URL: Configurable API Gateway endpoint (`http://localhost:8000`).

### Docker Integration
- Add `frontend` service to `docker-compose.yml`.
- Alternative: Serve built static files from gateway via `fastapi.staticfiles`.

---

## 2. V6 Hub Shell

The V6 client has exactly two entry points into domain data: the **Hub Directory** (`/hubs`) and the
global **Hub Switcher** (`Cmd+K`). Everything else is a nested route inside a hub. Platform surfaces
(Playground, MCP Registry, Models, Infrastructure, System, Settings) and the Admin Console stay outside
the hub tree because they are not hub-scoped — see
[`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §7.

### 2.1 Route Tree

```text
/                                   -> redirect to /hubs

/hubs                               Hub Directory — all four types, grouped, searchable
/hubs/new                           Hub creation wizard
/hubs/not-found                     Unknown / inaccessible hub (never a crash)
/hubs/:hubType/:hubId/*             Hub Shell — persistent frame + per-type tabs

  /hubs/ingestion/:hubId            overview | collections | collections/:collectionId
                                    | datastores | documents | jobs | search
  /hubs/agent/:hubId                overview | agents | agents/:agentId
  /hubs/workflow/:hubId             overview | workflows
                                    | workflows/:wfId/editor | workflows/:wfId/runs
  /hubs/eval/:hubId                 overview | suites | suites/:suiteId | runs
                                    | runs/:runId | traces
  /hubs/:hubType/:hubId/members     shared MembersPanel   (all four types)
  /hubs/:hubType/:hubId/links       shared HubLinksPanel  (all four types)
  /hubs/:hubType/:hubId/settings    hub settings          (all four types)

/admin/users                        User directory
/admin/users/pending                Approval queue
/admin/invites                      Invite manager
/admin/audit                        Audit log viewer

/login                              Password + OAuth sign-in
/register                           Password sign-up (gated by ALLOW_SELF_REGISTRATION)
/auth/callback                      OAuth callback
/auth/invite/:token                 Invite preview + dual OAuth/password acceptance
/auth/pending                       Approval holding screen (polls, "contact admin")
/auth/reset-password/:token         Password reset

/playground                         Model Playground        (all authenticated users)
/mcp                                MCP Registry            (read: all; write: admin)
/models                             Model Registry          (read: all; write: admin)
/infrastructure                     Qdrant / Neo4j embeds   (admin)
/settings                           User + platform settings
/system                             Telemetry & system status
```

**Hard cutover.** The V5 flat routes `/ingestion`, `/workflow`, `/agents` and `/evalops` are deleted —
no aliases, no redirects. `hubType` stays in the URL so the shell can pick its tab set before the hub
fetch resolves; a `hubType` that disagrees with the fetched `hub.hub_type` renders the not-found state.

### 2.2 Per-Hub-Type Tabs

`HubShell` renders one tab bar, driven by a single configuration table in
`frontend/src/components/hubs/hubTabs.ts` mapping `HubType → { label, icon, to }[]`.

| Hub type | Workspace tabs | Common tabs (right-aligned, visually separated) |
|---|---|---|
| `ingestion` | Overview · Collections · Documents · Jobs · Datastores · Search | Members · Links · Settings |
| `agent` | Overview · Agents | Members · Links · Settings |
| `workflow` | Overview · Workflows | Members · Links · Settings |
| `eval` | Overview · Suites · Runs · Traces | Members · Links · Settings |

Tab navigation inside a hub must **not** re-fetch the hub. Nested detail views (a collection, an agent,
a workflow editor) keep the hub tab bar and add their own inner tab bar where needed — the agent detail
workspace, for example, is a five-tab surface (Configuration, Knowledge, Endpoint, Invocations, Test)
whose active tab is a URL segment so it is linkable and survives reload.

### 2.3 Component Tree

```text
frontend/src/
├── routes.ts                        # typed path builders + ROUTE_PATTERNS (§2.4)
├── components/
│   ├── hubs/
│   │   ├── HubShell.tsx             # persistent frame: header, tabs, breadcrumbs, <Outlet/>
│   │   ├── HubContext.tsx           # { hub, hubRole, isArchived, can(), denyReason() }
│   │   ├── hubTabs.ts               # HubType → tab config
│   │   ├── Gated.tsx                # role/archive-aware control wrapper
│   │   ├── HubDirectory.tsx         # /hubs — grouped, searchable, archived toggle
│   │   ├── HubCreateWizard.tsx      # /hubs/new — type, name, slug, accent, members, links
│   │   ├── HubSwitcher.tsx          # Cmd+K cross-hub jump
│   │   ├── MembersPanel.tsx         # shared by all four hub types
│   │   ├── HubLinksPanel.tsx        # shared; enforces the §3.3 direction matrix in the UI
│   │   ├── HubNotFound.tsx
│   │   ├── ingestion/
│   │   │   ├── IngestionOverview.tsx
│   │   │   ├── CollectionsWorkspace.tsx
│   │   │   ├── CollectionDetail.tsx
│   │   │   ├── RetrievalTester.tsx      # props { hubId, collectionId } — no global state
│   │   │   ├── DatastoresWorkspace.tsx  # new in V6 — per-hub physical store bindings
│   │   │   ├── DocumentsWorkspace.tsx
│   │   │   ├── IngestionUploadModal.tsx # V7: Advanced Multi-Step Pipeline Config (OCR, Chunking, Embedding, Summary, KG)
│   │   │   └── JobsWorkspace.tsx
│   │   ├── agent/
│   │   │   ├── AgentOverview.tsx
│   │   │   ├── AgentLibrary.tsx
│   │   │   └── AgentDetail.tsx
│   │   ├── workflow/
│   │   │   ├── WorkflowHubOverview.tsx
│   │   │   ├── WorkflowLibrary.tsx
│   │   │   ├── CreateWorkflowDialog.tsx
│   │   │   ├── WorkflowEditor.tsx
│   │   │   ├── VersionRail.tsx
│   │   │   ├── ValidationStrip.tsx
│   │   │   ├── ConflictDialog.tsx
│   │   │   ├── WorkflowRuns.tsx
│   │   │   ├── RunDetailDrawer.tsx
│   │   │   ├── NodeTraceTimeline.tsx
│   │   │   └── templates/               # seed graphs for the create dialog
│   │   └── eval/
│   │       ├── EvalHubOverview.tsx
│   │       ├── SuiteManager.tsx
│   │       ├── TargetPicker.tsx         # Agent | Workflow, grouped by source hub
│   │       ├── TestCaseEditor.tsx
│   │       ├── RunConfigModal.tsx
│   │       ├── EvalResults.tsx
│   │       ├── MetricTrendChart.tsx
│   │       ├── CaseResultsTable.tsx
│   │       └── TraceReplay.tsx
│   ├── admin/
│   │   ├── UserDirectory.tsx        # /admin/users
│   │   ├── ApprovalQueue.tsx        # /admin/users/pending
│   │   ├── InviteManager.tsx        # /admin/invites — incl. copy-link SMTP fallback
│   │   └── AuditLogViewer.tsx       # /admin/audit — virtualised, before/after diff
│   ├── auth/                        # LoginPage, RegisterPage, InviteAccept,
│   │                                # PendingApproval, ResetPassword
│   ├── layout/                      # Sidebar, Header, Breadcrumbs, CommandPalette
│   └── shared/                      # DataTable, PageHeader, EmptyState, ErrorState, …
├── hooks/useHubPermissions.ts
└── store/hubSlice.ts
```

The ReactFlow editor and its dependency are loaded lazily via route-level code splitting.

### 2.4 Typed Route Builders

All route paths are centralised in `frontend/src/routes.ts` as typed builder functions. **No component,
sidebar entry, breadcrumb or command-palette action may contain a hardcoded path string.**

```ts
export type HubType = "ingestion" | "agent" | "workflow" | "eval";

export const routes = {
  hubs: {
    directory: () => "/hubs",
    create:    () => "/hubs/new",
    shell:   (t: HubType, id: string) => `/hubs/${t}/${id}`,
    members: (t: HubType, id: string) => `/hubs/${t}/${id}/members`,
    links:   (t: HubType, id: string) => `/hubs/${t}/${id}/links`,
  },
  ingestionHub: { collections: (id) => `/hubs/ingestion/${id}/collections`, /* … */ },
  agentHub:     { agent: (id, aid) => `/hubs/agent/${id}/agents/${aid}`,    /* … */ },
  workflowHub:  { editor: (id, wid) => `/hubs/workflow/${id}/workflows/${wid}/editor`, /* … */ },
  evalHub:      { runs: (id) => `/hubs/eval/${id}/runs`, /* … */ },
  admin:        { users: () => "/admin/users", invites: () => "/admin/invites", /* … */ },
  platform:     { playground: () => "/playground", mcp: () => "/mcp", /* … */ },
} as const;
```

A sibling `ROUTE_PATTERNS` object holds the React Router pattern strings
(`"/hubs/:hubType/:hubId/collections/:collectionId"` …) so `<Route path>` declarations can never
diverge from the builders.

### 2.5 `HubContext` & Role Gating

`HubShell` performs the single hub fetch for the route and publishes the result through `HubContext`.
Child views never re-fetch the hub and never re-derive the caller's role.

```ts
export interface HubContextValue {
  hub: Hub;
  hubRole: "owner" | "maintainer" | "contributor" | "viewer";
  isPlatformAdmin: boolean;
  isArchived: boolean;
  can: (action: HubAction) => boolean;
  denyReason: (action: HubAction) => "role" | "archived" | null;
}
```

`frontend/src/hooks/useHubPermissions.ts` encodes the
[`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §4.2 capability
matrix **once**, as a pure, unit-testable `evaluate(role, action, isArchived)` function. Every mutating
control asks this hook rather than comparing role strings inline. The role ladder is ordered
`owner(4) > maintainer(3) > contributor(2) > viewer(1)`, and a platform `admin` short-circuits to
`owner`.

Two gating modes, implemented by the `<Gated action="…">` wrapper:

* **Denied on role grounds** → the control is **not rendered at all**. A `viewer` sees no create, edit
  or delete affordance anywhere in the hub — never a control that fails on click.
* **Denied because the hub is archived** → the control renders `disabled` with the tooltip
  "This hub is archived and is read-only".

### 2.6 Store Layer

`frontend/src/store/hubSlice.ts` (new) holds hub lists by type, the active hub, its membership and its
links. Every other hub-scoped slice keys its data by `hubId` rather than holding a single global
collection:

```ts
collectionsByHub: Record<string, Collection[]>
agentsByHub:      Record<string, Agent[]>
suitesByHub:      Record<string, EvalSuite[]>
```

`workflowSlice.ts` is rewritten. Per
[`workflow_v6.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/workflow_v6.md) §5
the singleton `currentWorkflow`, `nodes` and `edges` fields are **removed** in favour of:

```ts
workflowsByHub: Record<string, WorkflowSummary[]>
editorState: {
  workflowId: string | null
  graph: WorkflowGraph
  versionEtag: string | null
  dirty: boolean
  validation: ValidationResult | null
}
runsByWorkflow: Record<string, WorkflowRun[]>
```

ReactFlow node/edge state is local to the editor component; it is not global store state. Canvas edits
debounce-autosave the draft (2s idle) with `If-Match: <versionEtag>` and surface an explicit
`Saved • v7 draft` / `Unsaved changes` / `Conflict` state in the editor header.

### 2.7 API Client Contract

Every hub-scoped helper in `frontend/src/services/api.ts` takes `hubId` as its **explicit first
argument**. The flat V5 helpers are deleted outright, not wrapped.

```ts
// V5 — removed
getAgents(); createAgent(body); listSuites(); getDocuments();

// V6
listAgents(hubId);                 // GET  /api/hubs/{hubId}/agents
createAgent(hubId, body);          // POST /api/hubs/{hubId}/agents
listCollections(hubId);            // GET  /api/hubs/{hubId}/collections
listSuites(hubId);                 // GET  /api/hubs/{hubId}/eval/suites
saveWorkflowDraft(hubId, wfId, graph, etag);
```

Only platform surfaces (`/api/playground`, `/api/mcp`, `/api/models`, `/api/telemetry`, `/api/admin`,
`/api/auth`) may be called without a `hubId`. Cross-hub references are always sent as **qualified
references** (`{ hub_id, agent_id }`), never a bare id.

### 2.8 Navigation

* **Sidebar** is regrouped into three sections:
  * **Hubs** — the four hub types, each expandable to list that user's hubs, plus "All hubs" and
    "New hub".
  * **Platform** — Playground, MCP Registry, Models, Infrastructure, System, Settings.
  * **Admin** — visible only to platform admins, carrying a live **pending-approval badge** sourced
    from `GET /admin/users/pending` (polled every 60s, invalidated on approval actions).
* **Hub Switcher** (`Cmd+K`) searches every hub the user belongs to and jumps straight to it, surfacing
  recently visited hubs first — no round-trip through the directory.
* **Command Palette** gains hub-aware actions: create hub, create workflow in the current hub, invite a
  user, jump to a collection, open the current hub's members or links.
* **Breadcrumbs** are hub-aware: `Hubs / {Hub Type} / {Hub Name} / {Tab} / {Resource}`, with the hub
  name taken from `HubContext` rather than parsed out of the URL.
* An **archived banner** renders above the tab bar for archived hubs, with an Unarchive action for
  owners.

### Removed in V6

Deleted outright — not deprecated, not aliased
([`hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §9):

| Kind | Removed |
|---|---|
| Routes | `/ingestion`, `/workflow`, `/agents`, `/evalops` |
| Pages | `AgentHub.tsx`, `WorkflowCanvas.tsx`, `IngestionPanel.tsx`, `EvalPanel.tsx` |
| Pages | `collections/CollectionManager.tsx`, `collections/RetrievalTester.tsx` (with the `tenant_id` field and `tenantFilter`) |
| Pages | `settings/UserManagement.tsx` → `admin/UserDirectory.tsx` |
| Pages | `eval/SuiteManager.tsx`, `eval/TestCaseEditor.tsx`, `eval/RunConfigModal.tsx`, `eval/FlowTraceVisualizer.tsx` |
| Store | `workflowSlice` singleton fields — `currentWorkflow`, `activeWorkflow`, `workflows`, `nodes`, `edges`, `setWorkflows`, `setActiveWorkflow`, and `Workflow.graph_json` |
| API | every non-hub-scoped helper in `services/api.ts` (`getAgents`, `createAgent`, `getWorkflows`, `getDocuments`, `listSuites`, `triggerEvalRun`, `listUsers`, `updateUserRole`, …) |
| Roles | the `editor` and `viewer` **platform** roles and every UI branch on them; `platform_role` is `admin` \| `member`. `viewer` survives only as a **hub** role |
| Copy | the label "MCP Hub" / "MCP Integration Hub" → **MCP Registry**; `MCPHubPage.tsx` → `MCPRegistryPage.tsx` |

Removal is tracked under **B6-11 (Legacy Decommission)** and enforced by
`scripts/verify_v6_cutover.py`.

---

## 3. Design Tokens & Aesthetics (Pending)
- **Dark Mode:** Graphite backgrounds (`#0F0F11` to `#18181B`) with neon accents (emerald/indigo/amber).
- **Typography:** Google Fonts — `Inter` or `Outfit`.
- **Glassmorphism:** `backdrop-filter: blur(12px)`, thin borders (`1px solid rgba(255,255,255,0.05)`).
- **Micro-Animations:** Cubic-bezier transitions (`0.4, 0, 0.2, 1`).
- **Responsive:** Optimized for desktop/tablet (min 1024px). No mobile requirement.

---

## 4. Pluggable Dashboard Architecture (Pending)
- **Status Mapping:** GET `/health` → read `active_projects` array.
- **Dynamic Navigation:** Disable routes/menus for inactive modules.
- **Fallback Placeholders:** Show status card with enable instructions if user navigates to disabled module.

---

## 5. Global System Status Panel (Pending)

### Connection Grid
| Service | Port |
|---|---|
| Gateway | 8000 |
| Inference Server | 8010 |
| PostgreSQL | 5432 |
| Qdrant | 6333 |
| Neo4j | 7687 |
| Redis | 6379 |
| Kafka Broker | 9092 |

### VRAM Monitor
- Visual gauge: VRAM used / budget.
- Live list of loaded model slots (from `/health`).
- Cold-start vs warm latency metrics.

### Model Registry Panel
- Active model per role (OCR, ASR, Embedding, Classifier, Completion).
- Local vs cloud indicator per role.
- VRAM budget impact visualization.
- Model benchmark scores/docs links.
- Toggle model selection via API (if `AUTH_ENABLED`).

### Admin Quick-Console Links
- Qdrant: `http://localhost:6333/dashboard`
- Neo4j: `http://localhost:7474`
- pgAdmin: `http://localhost:5050`
- Kafka UI: `http://localhost:8080`
- Jaeger: `http://localhost:16686`

---

## 6. SyntraFlow Dashboard — Ingestion Hub Workspace (Pending)

> **V6:** these panels are no longer a standalone `/ingestion` page. They render inside the Ingestion Hub workspace under `/hubs/ingestion/:hubId/*` (§2.3) and are scoped to that hub's collections and datastore bindings.

- **Uploader:** Multi-modal drag-and-drop. Upload speeds, file categories, duplicate detection.
- **Ingestion Job Tracker:** Active/recent jobs with status, progress bar, job logs.
- **Layout OCR Split Panel:** Left: PDF renderer. Right: Extracted Markdown. Shows OCR model used.
- **Video Segment Timeline:** HTML5 player + transcription segments + keyframe descriptions + event tags.
- **RAG Sandbox:** Query box, strategy selector (Vector/Graph/Hybrid), side-by-side results comparison.

---

## 7. GuardRoute Dashboard — Agent & Workflow Hub Workspaces (Pending)

> **V6:** the flow map lives in the Workflow Hub editor (`/hubs/workflow/:hubId/workflows/:wfId/editor`) and the chat/invoke client lives in the Agent Hub detail **Test** tab (`/hubs/agent/:hubId/agents/:agentId`). There is no single global graph.

- **LangGraph Flowchart:** React Flow interactive flow map with real-time node highlighting and latency annotations.
- **Subagent Node Logs:** Side-drawer with raw JSON, latency, model, token count.
- **Streaming Chat Client:** Markdown + syntax highlighting. SSE streaming. Meta-header with complexity, agents, latency, model, tokens.

---

## 8. EvalOps Dashboard — Eval Hub Workspace (Pending)

> **V6:** these surfaces render under `/hubs/eval/:hubId/*`. A suite targets either an agent or a workflow in a **linked** hub; see [`references/logic/evalops.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/evalops.md).

- **RAGAS Metric Charts:** Timeline graphs for Context Recall, Faithfulness, Semantic Similarity.
- **Safety Audit Logs:** Blocked injections, toxicity ratings, PII leakage alerts.
- **Diagnostic Trace Waterfall:** OpenTelemetry span visualization with bottleneck highlighting.
- **Model Comparison Dashboard:** Benchmark results from `bench_models.py`.

---

## 9. Error Handling & Loading States (Pending)
- **Skeleton Loaders:** Animated placeholders during data load.
- **Error Banners:** Dismissible with message, retry button, timestamp, suggestion.
- **Upload Failures:** Specific errors for size limit, unsupported format, duplicate, pipeline failure.
- **LLM Provider Outage:** Show provider status and fallback model used.
- **Empty States:** Meaningful illustrations for empty dashboards.

---

## 10. Admin Services (docker-compose)

### Admin Profile (`--profile admin`)
```yaml
pgadmin:
  image: dpage/pgadmin4
  ports: ["5050:80"]
  environment:
    PGADMIN_DEFAULT_EMAIL: admin@local.dev
    PGADMIN_DEFAULT_PASSWORD: admin
  profiles: ["admin"]

kafka-ui:
  image: provectuslabs/kafka-ui:latest
  ports: ["8080:8080"]
  environment:
    KAFKA_CLUSTERS_0_NAME: local
    KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka:9092
  profiles: ["admin"]
```

### Observability Profile (`--profile observability`)
```yaml
jaeger:
  image: jaegertracing/all-in-one:latest
  ports:
    - "16686:16686"
    - "4317:4317"
    - "4318:4318"
  profiles: ["observability"]
```
