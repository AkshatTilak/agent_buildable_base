# B6-04: Ingestion Hub — Collections & Datastore Bindings

> **Status:** `[x]`  
> **Owner:** `projects/syntraflow`  
> **Secondary:** `gateway/api`, `common/models`  
> **Complexity:** 🔴 High (6 subtasks)

---

## Objective

Convert SyntraFlow into the **Ingestion Hub**. Each ingestion hub owns many collections *and* the
physical databases that store them: administrators bind a hub to specific Qdrant / Neo4j / Postgres /
OpenSearch instances, and every collection, document, chunk, job and retrieval query resolves through
those bindings. All flat `/api/syntraflow/*` endpoints are replaced by hub-nested equivalents.

---

## Acceptance Criteria

- [ ] `CollectionManager` requires `hub_id` on every operation and derives physical store names as `{hub_slug}__{collection_name}`.
- [ ] Collection names are unique **within** a hub; two hubs may both own a collection called `policies`.
- [ ] `DatastoreBindingManager` supports create / list / update / delete / test-connection for `qdrant`, `neo4j`, `postgres` and `opensearch` bindings.
- [ ] Binding credentials are Fernet-encrypted at rest with `DATASTORE_ENCRYPTION_KEY` and are never returned by any API response; `connection_uri` is masked on read.
- [ ] Exactly one binding per `(hub_id, store_type)` may be `is_default`; the platform default is surfaced as a synthetic read-only binding when no binding exists.
- [ ] A background health check updates `health_status` / `last_health_check` per binding and is exposed on the API.
- [ ] Client resolution is centralised: `resolve_vector_client(hub_id)` / `resolve_graph_client(hub_id)` return a client for the hub's bound store, with connection pooling and a clear error when a binding is unhealthy.
- [ ] The ingestion pipeline writes `hub_id` onto documents, chunks, video segments and jobs, and refuses to run if the target collection belongs to another hub.
- [ ] Retrieval accepts a per-collection strategy config (dense / sparse / hybrid / graph) and filters every query by `hub_id`.
- [ ] All ingestion routes are nested under `/hubs/{hub_id}/...` and guarded by `require_hub(hub_type="ingestion")`.
- [ ] Flat `/api/syntraflow/collections`, `/search`, `/ingest` and `/jobs` routes are **removed**.
- [ ] Existing collections, documents, chunks, segments and jobs are migrated into the `ingestion/default` seed hub with physical stores renamed accordingly.
- [ ] `tests/test_ingestion_hub.py` covers hub scoping, binding encryption, cross-hub rejection, and per-hub name collision.

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S6-04a | Hub-Scoped Collection Manager | [`S6-04a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-04a.md) |
| S6-04b | Datastore Binding Manager & Client Resolution | [`S6-04b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-04b.md) |
| S6-04c | Hub-Scoped Ingestion Pipeline & Jobs | [`S6-04c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-04c.md) |
| S6-04d | Hub-Scoped Retrieval & Strategy Config | [`S6-04d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-04d.md) |
| S6-04e | Ingestion Hub API Routes | [`S6-04e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-04e.md) |
| S6-04f | Ingestion Data Migration, Legacy Removal & Tests | [`S6-04f.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-04f.md) |

---

## References

- [`references/logic/syntraflow.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/syntraflow.md)
- [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §3.4, §3.6, §5
- [`references/deployment/infrastructure.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/deployment/infrastructure.md)
