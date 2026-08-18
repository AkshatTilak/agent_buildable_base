---
version: 2.0.0
updated: 2026-08-18
id: skill_qa_e2e
links:
  - ../frontend/SKILL.md
  - ../../../references/tests/tests.md
  - ../../../design/ux/
  - ../../../workflows/execution/work_verification.md
  - ../../../CONVENTIONS.md
---

# Skill: QA — End-to-End (E2E) Browser Testing

> **Purpose**: Live browser-driven verification of web applications using
> Playwright. Complements `../frontend/SKILL.md` (static QA checks) with
> runtime interaction testing. Adapted from anthropic/skills `webapp-testing`.

## Trigger Conditions
- Load this skill when verifying a web application's runtime behavior.
- Load when `../frontend/SKILL.md` static checks pass and live interaction
  testing is needed.
- Load when a task requires E2E or integration testing of UI flows.

## Decision Tree: Choosing Your Approach

```
User task → Is it static HTML?
    ├─ Yes → Read HTML file directly to identify selectors
    │         ├─ Success → Write Playwright script using selectors
    │         └─ Fails/Incomplete → Treat as dynamic (below)
    │
    └─ No (dynamic webapp) → Is the server already running?
        ├─ No → Run: python scripts/with_server.py --help
        │        Then use the helper + write simplified Playwright script
        │
        └─ Yes → Reconnaissance-then-action:
            1. Navigate and wait for networkidle
            2. Take screenshot or inspect DOM
            3. Identify selectors from rendered state
            4. Execute actions with discovered selectors
```

## Reconnaissance-Then-Action Pattern

1. **Inspect rendered DOM**:
   ```python
   page.screenshot(path='/tmp/inspect.png', full_page=True)
   content = page.content()
   page.locator('button').all()
   ```

2. **Identify selectors** from inspection results

3. **Execute actions** using discovered selectors

## Common Pitfall

❌ **Don't** inspect the DOM before waiting for `networkidle` on dynamic apps
✅ **Do** wait for `page.wait_for_load_state('networkidle')` before inspection

## State Verification

Verify all three states per `../../../CONVENTIONS.md` §2:
- **Empty / zero state**: what the page looks like with no data
- **Populated state**: what the page looks like with real content
- **Errored state**: what the page looks like when something fails

## Best Practices

- **Use bundled scripts as black boxes** — scripts in `scripts/` handle common
  workflows reliably without cluttering the context window. Use `--help` to see
  usage, then invoke directly.
- Use `sync_playwright()` for synchronous scripts
- Always close the browser when done
- Use descriptive selectors: `text=`, `role=`, CSS selectors, or IDs
- Add appropriate waits: `page.wait_for_selector()` or `page.wait_for_timeout()`

## Constraints
- **No hardcoded credentials**: use environment variables or test fixtures.
- **No testing against production**: use staging, local, or ephemeral
  environments.
- **Clean up test data**: tests should not leave persistent side effects.
- **Follow `../../../workflows/execution/work_verification.md`**: both test
  tracks must pass before sign-off.

## Expected Outputs
- E2E test results covering all user flows and states.
- Accessibility report.
- Visual regression report (if applicable).
- Issues filed for any failures.

## References
- `../frontend/SKILL.md` — static frontend QA.
- `../../../references/tests/tests.md` — two-track testing strategy.
- `../../../design/ux/` — UX design specifications.
- `../../../workflows/execution/work_verification.md` — verification protocol.
- `../../../CONVENTIONS.md` — zero-state convention.
- Adapted from: anthropic/skills `webapp-testing`.
