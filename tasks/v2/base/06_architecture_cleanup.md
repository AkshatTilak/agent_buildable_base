# Base Task: Architecture Cleanup & Decoupling

## Objective
Refactor existing backend microservices, clean up circular dependencies, and enforce rigid API boundaries between the Gateway, Inference Server, and Common Library.

## Business/System Value
The codebase currently has structural flaws where logic that belongs in `common` is duplicated, or the `gateway` directly depends on `inference` internals. A clean architecture makes the system maintainable, scalable, and easier for agents to modify in future versions without breaking downstream systems.

## Complexity Rating
Medium (Primarily code motion, refactoring imports, and standardizing error handling).

## Subtask Registry
* `[x] sub/arch_common_library_refactor.md` — Consolidate all shared models, DB clients, and utilities strictly into the `common` package.
* `[x] sub/arch_dependency_resolution.md` — Eliminate circular imports between `gateway` and `inference`.
* `[x] sub/arch_error_handling_standard.md` — Implement a unified error handling and logging format across all services.
* `[x] sub/arch_api_contracts.md` — Formally define OpenAPI specs for communication between Gateway and Inference Server, removing direct Python call dependencies.
