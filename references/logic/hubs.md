# Hub Tenancy Model (V6) — Canonical Reference

> **Status:** Authoritative for V6+
> **Supersedes:** the flat, globally-scoped resource model used in V1–V5.
> **Owner modules:** `common/models`, `gateway/api/hubs.py`, `gateway/auth`, all `projects/*`

This document is the **single source of truth** for the V6 Hub architecture. Every other reference
(`syntraflow.md`, `guardroute.md`, `evalops.md`, `workflow_v6.md`, `auth.md`, `frontend.md`) defers to
the definitions here. If a downstream document conflicts with this file, this file wins.

---

## 1. Motivation & Core Decision

Through V5 every resource — collections, agents, workflows, eval suites — lived in a single flat global
namespace. There was exactly one implied workspace, one workflow surface, and a single global RBAC triple
(`admin` / `editor` / `viewer`). This made it impossible to:

* run more than one workflow graph at a time,
* separate a customer-support knowledge base from an internal-engineering knowledge base,
* delegate ownership of a subsystem to a specific group of users,
* evaluate an arbitrary workflow **or** agent from one place.

**Decision (V6):** introduce the **Hub** as the platform's unit of tenancy.

> A **Hub** is a typed, isolated workspace that owns a set of resources and a set of members.

Every domain resource in the platform belongs to exactly one Hub. Every read and every write is scoped
by `hub_id`. There is no "global" resource plane for domain data anymore.

---

## 2. Hub Types

There are exactly **four** hub types. The type is fixed at creation and immutable.

| `hub_type` | Owns | Backing module |
|---|---|---|
| `ingestion` | Collections, datastore bindings, documents, chunks, video segments, ingestion jobs | `projects/syntraflow` |
| `agent` | Agent definitions, agent endpoints, invocation logs | `projects/guardroute` |
| `workflow` | Workflow definitions, workflow versions, workflow runs | `projects/guardroute` |
| `eval` | Eval suites, test cases, eval runs, metric results, flow traces | `projects/evalops` |

A deployment may contain **many hubs of each type**. `Support KB` and `Eng Docs` are two independent
`ingestion` hubs; `Billing Flows` and `Onboarding Flows` are two independent `workflow` hubs, each
containing many workflows.

### Naming collision resolution

V5 shipped a feature called the "MCP Integration Hub". That name now collides with the Hub tenancy
concept. **In V6 it is renamed `MCP Registry`** and remains a *platform-level* service (see §7). The
word "Hub" is reserved exclusively for the tenancy construct defined in this document.

---

## 3. Data Model

### 3.1 `hubs`

```text
hubs
  id               String(36)  PK, uuid4
  slug             String(64)  indexed          # url-safe identifier, e.g. "support-kb"
  name             String(120) not null
  hub_type         String(20)  not null         # ingestion | agent | workflow | eval
  description      Text        nullable
  accent           String(20)  nullable         # design-token key for UI accent colour
  icon             String(40)  nullable         # lucide icon name
  settings_json    JSON        default {}
  owner_id         String(36)  FK users.id ON DELETE RESTRICT
  is_archived      Boolean     default false
  created_at       DateTime
  updated_at       DateTime

  UNIQUE (hub_type, slug)
```

* `slug` is unique **within a hub type**, not globally. `ingestion/support-kb` and `agent/support-kb`
  may coexist.
* Hubs are **archived**, never hard-deleted, unless the hub is empty. Archived hubs are read-only and
  hidden from default listings.
* `owner_id` must always resolve to an active user holding the `owner` hub role for that hub.

### 3.2 `hub_members`

```text
hub_members
  id           String(36) PK
  hub_id       String(36) FK hubs.id ON DELETE CASCADE, indexed
  user_id      String(36) FK users.id ON DELETE CASCADE, indexed
  hub_role     String(20) not null              # owner | maintainer | contributor | viewer
  invited_by   String(36) FK users.id nullable
  created_at   DateTime

  UNIQUE (hub_id, user_id)
```

### 3.3 `hub_links` — cross-hub consumption grants

Hubs are isolated by default. A hub may only reference resources in another hub if an explicit,
directional **link** exists.

```text
hub_links
  id              String(36) PK
  source_hub_id   String(36) FK hubs.id ON DELETE CASCADE, indexed   # the consumer
  target_hub_id   String(36) FK hubs.id ON DELETE CASCADE, indexed   # the provider
  access_level    String(20) not null    # read | use
  created_by      String(36) FK users.id
  created_at      DateTime

  UNIQUE (source_hub_id, target_hub_id)
```

**Allowed link directions** (enforced in application logic; any other pairing is rejected with `422`):

| Source (consumer) | Target (provider) | Meaning |
|---|---|---|
| `agent` | `ingestion` | Agents may retrieve from the linked hub's collections |
| `workflow` | `agent` | Workflow nodes may instantiate the linked hub's agents |
| `workflow` | `ingestion` | Workflow retrieval nodes may query the linked hub's collections |
| `eval` | `workflow` | Eval suites may target the linked hub's workflows |
| `eval` | `agent` | Eval suites may target the linked hub's agents |

Nothing may link *into* an `eval` hub. Links are **not** transitive: a workflow hub linked to an agent
hub does **not** inherit that agent hub's ingestion links; the workflow hub must declare its own.

A link may only be created by a user holding `owner` or `maintainer` on the **source** hub **and** at
least `contributor` on the **target** hub.

### 3.4 `datastore_bindings` — physical stores per ingestion hub

Satisfies the V6 requirement that an ingestion hub manages "the databases that use/store" its data.

```text
datastore_bindings
  id                     String(36) PK
  hub_id                 String(36) FK hubs.id ON DELETE CASCADE, indexed
  name                   String(120) not null
  store_type             String(20)  not null   # qdrant | neo4j | postgres | opensearch
  connection_uri         String(500) not null
  credentials_encrypted  Text        nullable   # Fernet-encrypted, never returned by the API
  is_default             Boolean     default false
  health_status          String(20)  default "unknown"
  last_health_check      DateTime    nullable
  config_json            JSON        default {}
  created_at / updated_at

  UNIQUE (hub_id, name)
```

* If a hub declares no binding of a given `store_type`, the platform default from
  `common/config/settings.py` is used implicitly (a synthetic "Platform Default" binding is surfaced
  read-only in the UI).
* Exactly one binding per `(hub_id, store_type)` may have `is_default = true`.
* `credentials_encrypted` is encrypted at rest with `DATASTORE_ENCRYPTION_KEY` and is **never**
  serialised into any API response. Responses expose a masked `connection_uri` only.

### 3.5 `audit_log`

```text
audit_log
  id             String(36) PK
  hub_id         String(36) FK hubs.id nullable, indexed   # null = platform-level action
  actor_user_id  String(36) FK users.id nullable, indexed
  action         String(40)  not null     # create | update | delete | archive | invite | approve | ...
  resource_type  String(40)  not null     # hub | agent | workflow | collection | user | ...
  resource_id    String(36)  nullable
  summary        String(255) nullable
  before_json    JSON        nullable
  after_json     JSON        nullable
  ip_address     String(45)  nullable
  created_at     DateTime, indexed
```

Every mutating hub-scoped or admin endpoint writes exactly one audit row.

### 3.6 Resource scoping — `hub_id` columns added

| Table | `hub_id` | Notes |
|---|---|---|
| `syntraflow_collections` | **NOT NULL** | replaces the V5 soft `tenant_id`, which is **dropped** |
| `syntraflow_documents` | **NOT NULL** | denormalised from collection for query performance |
| `syntraflow_chunks` | **NOT NULL** | denormalised |
| `syntraflow_video_segments` | **NOT NULL** | denormalised |
| `syntraflow_jobs` | **NOT NULL** | |
| `agent_definitions` | **NOT NULL** | |
| `agent_invocation_log` | **NOT NULL** | |
| `workflows` | **NOT NULL** | |
| `workflow_versions` *(new)* | via `workflow_id` | see [`workflow_v6.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/workflow_v6.md) |
| `workflow_runs` *(new)* | **NOT NULL** | |
| `eval_test_suites` | **NOT NULL** | |
| `eval_test_cases` | via `suite_id` | inherits |
| `eval_run_history` | **NOT NULL** | |
| `eval_metric_results` | via `run_id` | inherits |
| `eval_flow_traces` | **NOT NULL** | |
| `api_keys` | nullable | `null` = platform-wide key; set = hub-scoped key |
| `mcp_servers` | nullable | `null` = platform registry entry; set = hub-private server |
| `playground_sessions` | nullable | playground is user-scoped, hub is an optional context tag |

### 3.7 Uniqueness changes

* `agent_definitions.endpoint_slug`: `UNIQUE` → `UNIQUE (hub_id, endpoint_slug)`.
* `syntraflow_collections.name`: `UNIQUE` → `UNIQUE (hub_id, name)`.
  The **physical** Qdrant collection name becomes `{hub_slug}__{collection_name}` to preserve global
  uniqueness in the vector store while allowing friendly per-hub names.
* `workflows.name`: new `UNIQUE (hub_id, name)`.
* `eval_test_suites.name`: new `UNIQUE (hub_id, name)`.

---

## 4. Roles & Permissions

V6 splits authorisation into two independent axes.

### 4.1 Platform role (`users.platform_role`)

Replaces the V5 `users.role` column.

| Role | Meaning |
|---|---|
| `admin` | Superuser. Implicit `owner` on every hub. Manages users, invites, approvals, platform settings, MCP Registry, infrastructure. |
| `member` | Standard user. Sees only hubs they are a member of. May create new hubs if `ALLOW_MEMBER_HUB_CREATION` is enabled. |

**V5 → V6 migration mapping:** `admin → admin`; `editor → member`; `viewer → member`.

### 4.2 Hub role (`hub_members.hub_role`)

| Capability | `owner` | `maintainer` | `contributor` | `viewer` |
|---|:--:|:--:|:--:|:--:|
| View hub & its resources | ✅ | ✅ | ✅ | ✅ |
| Run read-only operations (search, preview, dry-run) | ✅ | ✅ | ✅ | ✅ |
| Create / edit resources | ✅ | ✅ | ✅ | ❌ |
| Execute mutating operations (ingest, invoke, run workflow, run eval) | ✅ | ✅ | ✅ | ❌ |
| Delete resources | ✅ | ✅ | ❌ | ❌ |
| Manage datastore bindings | ✅ | ✅ | ❌ | ❌ |
| Manage members & their roles | ✅ | ✅ | ❌ | ❌ |
| Create / revoke hub links | ✅ | ✅ | ❌ | ❌ |
| Rename / re-slug the hub | ✅ | ✅ | ❌ | ❌ |
| Archive / unarchive the hub | ✅ | ❌ | ❌ | ❌ |
| Transfer ownership | ✅ | ❌ | ❌ | ❌ |
| Hard-delete an empty hub | ✅ | ❌ | ❌ | ❌ |

Hub roles are ordered: `owner (4) > maintainer (3) > contributor (2) > viewer (1)`. A check for
`contributor` is satisfied by `maintainer` and `owner`.

A platform `admin` short-circuits every hub check and is treated as `owner`.

---

## 5. Request Scoping Contract

### 5.1 Route shape

All hub-scoped resources live under a nested prefix. **This is mandatory** — no hub-scoped resource may
be reachable from a flat top-level route.

```text
/hubs                                     # list / create hubs
/hubs/{hub_id}                            # get / update / archive
/hubs/{hub_id}/members                    # membership CRUD
/hubs/{hub_id}/links                      # cross-hub grant CRUD

/hubs/{hub_id}/datastores                 # ingestion hubs only
/hubs/{hub_id}/collections                # ingestion hubs only
/hubs/{hub_id}/documents                  # ingestion hubs only
/hubs/{hub_id}/jobs                       # ingestion hubs only
/hubs/{hub_id}/search                     # ingestion hubs only

/hubs/{hub_id}/agents                     # agent hubs only
/hubs/{hub_id}/agents/{agent_id}/invoke

/hubs/{hub_id}/workflows                  # workflow hubs only
/hubs/{hub_id}/workflows/{wf_id}/versions
/hubs/{hub_id}/workflows/{wf_id}/runs

/hubs/{hub_id}/eval/suites                # eval hubs only
/hubs/{hub_id}/eval/runs
/hubs/{hub_id}/eval/traces
```

### 5.2 The `HubContext` dependency

A single FastAPI dependency resolves and validates hub access for every nested route:

```python
# gateway/auth/hub_context.py
@dataclass
class HubContext:
    hub: Hub
    user: User
    hub_role: str          # effective role, "owner" for platform admins
    is_platform_admin: bool

def require_hub(*, hub_type: str | None = None, min_role: str = "viewer") -> Callable:
    """FastAPI dependency factory. Resolves {hub_id} from the path, asserts the hub exists,
    is not archived, matches `hub_type`, and that the caller's effective role >= `min_role`."""
```

**Failure semantics (deliberate, to prevent enumeration):**

| Condition | Response |
|---|---|
| Hub does not exist | `404 Hub not found` |
| Hub exists but caller is not a member and is not a platform admin | `404 Hub not found` — **never** `403` |
| Caller is a member but role is insufficient | `403 Insufficient hub role` |
| Hub type mismatch for the route | `404 Hub not found` |
| Hub archived and request is mutating | `409 Hub is archived` |

### 5.3 Mandatory query scoping

Every repository/service function that touches a hub-scoped table **must** accept `hub_id` as a
required argument and include it in the `WHERE` clause. A resource id alone is never sufficient.

```python
# CORRECT
stmt = select(AgentDefinition).where(
    AgentDefinition.id == agent_id,
    AgentDefinition.hub_id == ctx.hub.id,
)

# FORBIDDEN — IDOR vulnerability
stmt = select(AgentDefinition).where(AgentDefinition.id == agent_id)
```

A guard test (`tests/test_hub_isolation.py`) asserts that no hub-scoped model is queried by primary key
alone anywhere in `gateway/` or `projects/`.

### 5.4 Cross-hub resolution

When a resource in hub *A* references a resource in hub *B* (an agent's collection binding, a workflow
node's agent reference, an eval suite's target), resolution goes through a shared helper:

```python
# common/services/hub_resolver.py
async def resolve_linked(session, source_hub_id, target_resource_type, target_resource_id) -> Any:
    """Loads the target resource, then asserts a hub_link exists from source_hub_id to the
    target's hub_id with a sufficient access_level. Raises HubLinkError (403) otherwise."""
```

Link validity is re-checked **at execution time**, not only at save time. Revoking a link immediately
breaks dependent executions with a descriptive error rather than silently leaking data.

---

## 6. Bootstrap & Data Migration

The V6 migration is a **hard cutover**. No back-compat aliases are retained.

1. Create `hubs`, `hub_members`, `hub_links`, `datastore_bindings`, `audit_log`,
   `user_invites`, `workflow_versions`, `workflow_runs`.
2. Add `platform_role` to `users`, backfill from `role`, drop `role`.
3. Create four seed hubs owned by the first `admin` user (or a synthetic `system@contained.local`
   admin if none exists):
   * `ingestion/default` — "Default Ingestion Hub"
   * `agent/default` — "Default Agent Hub"
   * `workflow/default` — "Default Workflow Hub"
   * `eval/default` — "Default Eval Hub"
4. Add `hub_id` to every table in §3.6 as nullable, backfill all existing rows to the matching seed
   hub, then `ALTER ... SET NOT NULL`.
5. Seed `hub_links` between the four default hubs along every allowed direction in §3.3 so existing
   agent→collection, workflow→agent and eval→agent references keep working.
6. Add every existing active user to all four seed hubs: old `admin` → `owner`,
   old `editor` → `contributor`, old `viewer` → `viewer`.
7. Rename physical Qdrant collections to `default__{name}` and update
   `syntraflow_collections.physical_name`.
8. Drop `syntraflow_collections.tenant_id`.
9. Rebuild unique constraints per §3.7.

The migration must be reversible (`downgrade()` implemented) and must be idempotent when re-run against
an already-migrated database.

---

## 7. What stays platform-level

These are **not** hub-scoped and remain under `/admin` or `/settings`:

| Surface | Route | Access |
|---|---|---|
| User management, invites, approvals | `/admin/users`, `/admin/invites` | platform `admin` |
| Audit log viewer | `/admin/audit` | platform `admin` |
| Model Registry | `/models` | read: all; write: platform `admin` |
| MCP Registry (renamed from "MCP Hub") | `/mcp` | read: all; write: platform `admin` |
| Infrastructure dashboards (Qdrant / Neo4j embeds) | `/infrastructure` | platform `admin` |
| Model Playground | `/playground` | all authenticated users |
| Platform API keys | `/settings/api-keys` | platform `admin` |
| System telemetry | `/telemetry` | all authenticated users |

---

## 8. Frontend Information Architecture

See [`references/structure/frontend.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/frontend.md)
§ "V6 Hub Shell" for the component contract. Summary of the route tree:

```text
/                          -> redirect to /hubs
/hubs                      Hub Directory (all four types, grouped, searchable)
/hubs/new                  Hub creation wizard
/hubs/:hubType/:hubId      Hub Shell (persistent sub-nav) with per-type tabs
/admin/*                   Admin Console (users, invites, approvals, audit)
/playground, /mcp, /infrastructure, /settings, /system   Platform surfaces
```

The sidebar is restructured into three groups: **Hubs** (the four types, each expandable to show that
user's hubs), **Platform** (Playground, MCP Registry, Infrastructure, System), **Admin** (visible only
to platform admins). A global **Hub Switcher** (`Cmd+K` searchable) lets a user jump between any hub
they belong to without going back to the directory.

---

## 9. Explicitly removed in V6

The following V5 surfaces are **deleted**, not deprecated:

* Flat routes `GET/POST /agents`, `/agents/{id}`, `/workflows`, `/api/syntraflow/collections`,
  `/api/evalops/*` suite and run endpoints — replaced by their `/hubs/{hub_id}/...` equivalents.
* `users.role` column and the `editor` / `viewer` platform roles.
* `syntraflow_collections.tenant_id`.
* The singleton workflow assumption in `frontend/src/store/workflowSlice.ts`
  (`currentWorkflow` as the only workflow).
* `require_role()` in `gateway/auth/dependencies.py` for hub-scoped endpoints — retained only for
  platform-level endpoints, narrowed to `admin` / `member`.
* The label "MCP Hub" everywhere in code, routes, and UI copy → "MCP Registry".

Removal work is tracked under **B6-11 (Legacy Decommission)**.

---

## 10. Related References

* [`references/logic/user_management.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/user_management.md) — invites, approval gate, local auth
* [`references/logic/auth.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/auth.md) — authentication flows
* [`references/logic/workflow_v6.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/workflow_v6.md) — multi-workflow management
* [`references/logic/syntraflow.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/syntraflow.md) — ingestion hub internals
* [`references/logic/guardroute.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/guardroute.md) — agent & workflow hub internals
* [`references/logic/evalops.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/evalops.md) — eval hub internals
* [`references/logic/security.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/security.md) — tenancy isolation & IDOR rules
* [`references/structure/frontend.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/structure/frontend.md) — hub shell IA
