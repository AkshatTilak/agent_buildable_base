# Subtask 14_04: Members Panel Null-Safety Fix

## Objective
Fix the Members panel runtime crash (blank render) caused by null `email` / `display_name` / `avatar_url` fields in the members API response.

## Root Cause
- `MembersPanel.tsx` `filteredMembers` calls `m.email.toLowerCase()` (line ~91) unconditionally. The members API returns `email: null`, `display_name: null`, `avatar_url: null` for the hub owner, so `m.email.toLowerCase()` throws `TypeError: Cannot read properties of null` and the component fails to render.

## Tasks
1. `[x]` **Null-safe filtering** — guard the search filter with `(m.email ?? "").toLowerCase()` and `(m.display_name ?? "").toLowerCase()` so null fields never throw during `filteredMembers` computation.
2. `[x]` **Null-safe rendering** — in the member row, render a fallback identity when `display_name` and `email` are both null (e.g. "Hub member" or the first 8 chars of `user_id`). Guard the avatar initial: `(m.display_name || m.email || "?").charAt(0).toUpperCase()`.
3. `[x]` **Optional chaining sweep** — scan `MembersPanel.tsx` for any other `m.email`, `m.display_name`, or `m.avatar_url` access not yet guarded and apply optional chaining / nullish coalescing.

> **Architecture note:** `HubMemberRead` schema (`common/schemas/hubs.py`) already declares `email: Optional[str] = None` and `display_name: Optional[str] = None`, so this is a frontend-only fix. No backend schema changes are needed.

## Definition of Done
- The Members panel renders without crashing even when `email` / `display_name` / `avatar_url` are null.
- Members with no identity still show a sensible fallback label and avatar initial.
- The search filter with null-email members does not throw — null members are included in results when the search query is empty.

## Test Cases

### Backend (`tests/test_workflow_canvas_interactivity.py`)
| # | Test | Expected |
|---|------|----------|
| B1 | `test_members_list_null_email_user` | Insert member with `email=None`, `display_name=None`; `GET /{hub_id}/members` | HTTP 200; member in array with `email: null`, `display_name: null`; no 500 |
| B2 | `test_members_list_mixed_null_and_real_users` | Hub with 1 null-email member + 1 normal member | HTTP 200; exactly 2 members returned |

### Frontend Manual
| # | Scenario | Steps | Expected |
|---|----------|-------|----------|
| F1 | Panel renders with null-email owner | Navigate to Members panel when hub owner has `email: null` | Panel renders; no blank screen / TypeError |
| F2 | Null-email member avatar initial | Null email + null display_name member | Avatar shows "?" initial |
| F3 | Search with null-email members | Type in search box while members include null-email users | No crash; null-email members included in unfiltered list, excluded only if search string matches something |
| F4 | Fallback label shown | Null-email + null display_name member | Row shows "Hub member" or short `user_id` fallback — not empty/blank |

