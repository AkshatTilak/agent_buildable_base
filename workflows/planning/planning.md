---
version: 1.2.0
updated: 2026-08-27
id: planning
links:
  - ../../design/design.md
  - ../../features/features.md
  - ../../tasks/tasks.md
  - ../user/user_input.md
  - ../../skills/research/architecture_research/SKILL.md
  - ../../skills/research/web_research/SKILL.md
---

# Planning — Research, Options & Recommendations

> **Purpose**: Before any implementation, plan. Research the options, present
> recommendations, and let the user choose. Planning is as important as coding.

## 1. When To Plan

- Before starting a new feature, workflow, or significant task.
- Before choosing a library, tool, or architecture.
- Before any change that affects backward compatibility.

## 2. Research First & Open-Source Reference Ingestion

Before proposing any architecture, run the research sequence below. Use
`../../skills/research/architecture_research/SKILL.md` for design/HLD tasks and
`../../skills/research/web_research/SKILL.md` for library/API tasks.

### Required Starting Sources (Architecture/Design)

| Source | URL | When to Use |
|--------|-----|-------------|
| Awesome Topics Hub | `https://github.com/topics/awesome` | Find domain-specific curated lists |
| Awesome Scalability | `https://github.com/binhnguyennus/awesome-scalability` | Scalability, reliability, perf patterns |
| Awesome Python | `https://github.com/vinta/awesome-python` | Python library selection |
| GitHub Trending (weekly) | `https://github.com/trending?since=weekly` | Community momentum signals |
| GitHub Search (stars) | `https://github.com/search?type=repositories&sort=stars` | Direct prior art |

These are **starting points**, not the only sources. Always cross-validate with
official docs and primary repos. See `architecture_research/SKILL.md` for the
full research protocol.

### Additional Research Channels

- **Web research**: consult official documentation, RFCs, and reputable sources.
- **Playwright / browser / crawl**: verify behavior against real pages/APIs where relevant.
- **Workspace search**: check `references/` and existing code for prior art before proposing anything new.

## 3. Present Options & Template Inquiry

When designing or proposing a system or substantial feature, present the user with clear architectural choices:
1. **Build upon an open-source template (Fork & Update)**: Adopt an existing open-source template or boilerplate as the base and adapt it.
2. **Create from scratch informed by templates (Reference-Informed Scratch Build)**: Analyze reference architectures, extract requirements/design patterns into SRS/DDS, and author the implementation cleanly from scratch.
3. **Custom Pure Architecture**: Build bespoke architecture tailored strictly to custom specifications.

Always outline trade-offs and provide a concrete recommendation with rationale.

## 4. Backward Compatibility Is A Choice

- Prefer backward-compatible changes, but **ask the user** whether to preserve
  backward compatibility or break it. Never assume silently.

## 5. Output

- Record the chosen option and rationale in the relevant `design/` or `features/` file.
- Link the decision to the tasks that implement it.
