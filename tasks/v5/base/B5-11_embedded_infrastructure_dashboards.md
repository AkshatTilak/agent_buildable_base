# B5-11: Embedded Infrastructure Dashboards & UI Proxies

> **Status:** `[x]`  
> **Owner:** `gateway`  
> **Secondary:** `frontend`  
> **Complexity:** 🟡 Medium (3 subtasks)

---

## Objective

Expose native infrastructure dashboards (Qdrant vector UI and Neo4j Browser) via authenticated API Gateway reverse proxies and embed them directly inside the React frontend.

---

## Acceptance Criteria

- [x] API Gateway implements reverse proxy endpoints (`/qdrant/*` → `http://localhost:6333`, `/neo4j/*` → `http://localhost:7474`)
- [x] RBAC middleware enforces admin/editor authentication before proxying requests
- [x] React frontend renders embedded iframe tabs for Qdrant and Neo4j dashboards in the Infrastructure view


---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S5-11a | Gateway Reverse Proxy for Infrastructure UIs | [`S5-11a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-11a.md) |
| S5-11b | RBAC Authorization & Middleware for Proxy Routes | [`S5-11b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-11b.md) |
| S5-11c | Frontend: Embedded Infrastructure Views | [`S5-11c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v5/sub/S5-11c.md) |
