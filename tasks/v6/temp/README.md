# V6 Temp — Ad-Hoc & Out-of-Scope Findings

This directory is the holding zone for issues discovered **during** V6 execution that fall outside the
subtask currently being worked on.

## Protocol

1. While executing a subtask, if you find a defect, risk or design conflict that is **not** in your
   subtask's scope, do **not** fix it inline.
2. Create a descriptive `.md` file here (e.g. `qdrant_rename_downtime.md`).
3. Record: what you found, where (file + symbol), why it is out of scope, the blast radius, and a
   suggested owner (`B6-xx` or a future version).
4. Return to your primary assignment.

## Escalate immediately instead of logging here if

* The finding contradicts [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) — the canonical spec must be corrected first.
* It is a live tenancy-isolation or authentication defect.
* It blocks the migration chain from running.
