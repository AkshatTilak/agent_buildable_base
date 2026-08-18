---
version: 2.0.0
updated: 2026-08-18
id: skill_backend_domain_modeling
links:
  - ../../../references/logic/
  - ../../../references/structure/
  - ../../../references/db/db.md
  - ../../../CONVENTIONS.md
  - ../../../workflows/quality/recheck_codebase.md
---

# Skill: Domain Modeling

> **Purpose**: Build and enforce a project's ubiquitous language — a shared,
> unambiguous vocabulary for domain concepts. Maintains a glossary of canonical
> terms, rejects ambiguous synonyms, and records architectural decisions when
> trade-offs meet defined thresholds. Adapted from mattpocock/skills
> `domain-modeling` and `codebase-design`.

## Trigger Conditions
- Load this skill when defining or refining domain concepts, entities, or
  business rules.
- Load when naming conflicts or ambiguous terminology are detected (see
  `../../../CONVENTIONS.md` §3).
- Load when making architectural trade-off decisions that affect the domain
  model.

## File Structure

Most repos have a single context:

```
/
├── CONTEXT.md
├── docs/
│   └── adr/
│       ├── 0001-event-sourced-orders.md
│       └── 0002-postgres-for-write-model.md
└── src/
```

If a `CONTEXT-MAP.md` exists at the root, the repo has multiple contexts. The
map points to where each one lives.

Create files lazily — only when you have something to write. If no `CONTEXT.md`
exists, create one when the first term is resolved. If no `docs/adr/` exists,
create it when the first ADR is needed.

## During the Session

### Challenge Against the Glossary

When the user uses a term that conflicts with the existing language in
`CONTEXT.md`, call it out immediately. "Your glossary defines 'cancellation' as
X, but you seem to mean Y — which is it?"

### Sharpen Fuzzy Language

When the user uses vague or overloaded terms, propose a precise canonical term.
"You're saying 'account' — do you mean the Customer or the User? Those are
different things."

### Discuss Concrete Scenarios

When domain relationships are being discussed, stress-test them with specific
scenarios. Invent scenarios that probe edge cases and force the user to be
precise about the boundaries between concepts.

### Cross-Reference with Code

When the user states how something works, check whether the code agrees. If you
find a contradiction, surface it: "Your code cancels entire Orders, but you just
said partial cancellation is possible — which is right?"

### Update CONTEXT.md Inline

When a term is resolved, update `CONTEXT.md` right there. Don't batch these up
— capture them as they happen.

`CONTEXT.md` should be totally devoid of implementation details. Do not treat
`CONTEXT.md` as a spec, a scratch pad, or a repository for implementation
decisions. It is a glossary and nothing else.

### Offer ADRs Sparingly

Only offer to create an ADR when all three are true:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will wonder "why did they
   do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you
   picked one for specific reasons

If any of the three is missing, skip the ADR.

## Glossary Terms (from codebase-design)

Use these terms exactly — don't substitute "component," "service," "API," or
"boundary." Consistent language is the whole point.

**Module** — anything with an interface and an implementation. Deliberately
scale-agnostic: a function, class, package, or tier-spanning slice. _Avoid_:
unit, component, service.

**Interface** — everything a caller must know to use the module correctly: the
type signature, but also invariants, ordering constraints, error modes, required
configuration, and performance characteristics. _Avoid_: API, signature (too
narrow).

**Implementation** — what's inside a module, its body of code. Distinct from
**Adapter**: a thing can be a small adapter with a large implementation (a
Postgres repo) or a large adapter with a small implementation (an in-memory
fake).

**Depth** — leverage at the interface: the amount of behaviour a caller (or
test) can exercise per unit of interface they have to learn. A module is **deep**
when a large amount of behaviour sits behind a small interface, **shallow** when
the interface is nearly as complex as the implementation.

**Seam** _(Michael Feathers)_ — a place where you can alter behaviour without
editing in that place; the *location* at which a module's interface lives.
_Avoid_: boundary (overloaded with DDD's bounded context).

**Adapter** — a concrete thing that satisfies an interface at a seam. Describes
*role* (what slot it fills), not substance (what's inside).

**Leverage** — what callers get from depth: more capability per unit of
interface they learn. One implementation pays back across N call sites and M
tests.

**Locality** — what maintainers get from depth: change, bugs, knowledge, and
verification concentrate in one place rather than spreading across callers. Fix
once, fixed everywhere.

## The Deletion Test

For every abstraction (module, class, service), apply the deletion test:
- If deleting the abstraction causes complexity to **scatter** across callers
  → the abstraction is justified.
- If deleting the abstraction causes complexity to **vanish** → the abstraction
  was unnecessary indirection.

Remove abstractions that fail the deletion test.

## Expected Outputs
- Domain glossary in `CONTEXT.md` with canonical terms and `_Avoid_` lists.
- Domain model documented in `../../../references/structure/`.
- ADRs for significant architectural decisions.
- Cleaned-up terminology across the codebase.

## References
- `../../../references/logic/` — business rules and domain logic.
- `../../../references/structure/` — architecture and topology.
- `../../../references/db/db.md` — database conventions.
- `../../../CONVENTIONS.md` — duplicate-name root-causing rule (§3).
- `../../../workflows/quality/recheck_codebase.md` — drift audit.
- Adapted from: mattpocock/skills `domain-modeling` and `codebase-design`.
