# B6-03: Admin User Management, Invitations & Approval Gate

> **Status:** `[ ]`  
> **Owner:** `gateway/auth`, `gateway/api/admin_users.py`  
> **Secondary:** `common/models`, `gateway/services/mailer.py`  
> **Complexity:** 🔴 High (7 subtasks)

---

## Objective

Give platform administrators complete control over who can enter the system. Add admin-issued email
invitations with pre-assigned platform roles and hub grants, local email + password authentication
alongside the existing Google/GitHub SSO, multi-provider identity linking, and an **approval gate** that
holds every unsolicited sign-up in a `pending` state until an admin approves it.

---

## Acceptance Criteria

- [ ] `users.is_active` is replaced by `users.status` (`pending` | `active` | `suspended` | `rejected`); only `active` users receive a usable session.
- [ ] `users.provider` / `users.provider_id` are replaced by a `user_identities` table supporting `google`, `github` and `password` on one account, resolved by verified email.
- [ ] A user signing in with Google and later with GitHub using the same verified email lands on **one** account.
- [ ] `user_invites` exists with hashed single-use tokens, TTL, `platform_role`, `hub_grants_json`, and a partial unique index allowing one open invite per email.
- [ ] Invite redemption works via **both** OAuth ("Continue with Google") and password setup; a provider email mismatch is rejected with `409`.
- [ ] Redeeming an invite applies its `hub_grants_json` as real `hub_members` rows and activates the account immediately.
- [ ] Self sign-up with no matching invite creates a `pending` user and returns `ACCOUNT_PENDING_APPROVAL`; the frontend shows a dedicated holding screen, not an error.
- [ ] `AUTO_APPROVE_EMAIL_DOMAINS` bypasses the gate for configured domains.
- [ ] Passwords use Argon2id with the documented parameters, a 12-character minimum, and a common-password denylist.
- [ ] Login is constant-time and non-enumerating; 5 failures lock the account for 15 minutes.
- [ ] `gateway/services/mailer.py` sends via SMTP when configured and degrades to `NullMailer` otherwise; when delivery fails the API still succeeds and returns a one-time `invite_url` for manual copying.
- [ ] Admin API covers list/detail/update/approve/reject/suspend/reinstate/delete users and create/list/resend/revoke invites, all audited.
- [ ] Guardrails hold: an admin cannot demote, suspend or delete themselves; the system refuses to drop to zero active admins; a hub owner cannot be deleted before ownership transfer.
- [ ] Password change, suspension, rejection and role demotion all revoke existing sessions immediately.
- [ ] Rate limits are applied per `user_management.md` §7 and covered by tests.

---

## Linked Subtasks

| ID | Title | File |
|---|---|---|
| S6-03a | User, Identity & Invite Models + Migration | [`S6-03a.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-03a.md) |
| S6-03b | Local Password Authentication | [`S6-03b.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-03b.md) |
| S6-03c | Multi-Provider OAuth Linking & Approval Gate | [`S6-03c.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-03c.md) |
| S6-03d | Invite Issuance & Redemption Service | [`S6-03d.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-03d.md) |
| S6-03e | Mailer Abstraction, SMTP & Templates | [`S6-03e.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-03e.md) |
| S6-03f | Admin Users, Invites & Audit API | [`S6-03f.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-03f.md) |
| S6-03g | Auth Hardening & Test Coverage | [`S6-03g.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/tasks/v6/sub/S6-03g.md) |

---

## References

- [`references/logic/user_management.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/user_management.md) — canonical for this base task
- [`references/logic/auth.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/auth.md)
- [`references/logic/hubs.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/hubs.md) §4
- [`references/logic/security.md`](file:///c:/Akshat/ContAIned/agent_buildable_base/references/logic/security.md)
