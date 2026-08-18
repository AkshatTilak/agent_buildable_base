---
version: 2.0.0
updated: 2026-08-18
id: skill_research_web_research
links:
  - ../../../references/references.md
  - ../../../references/resource/
  - ../../../workflows/planning/planning.md
  - ../../../workflows/user/user_input.md
---

# Skill: Web Research

> **Purpose**: A structured research workflow that investigates questions against
> high-trust primary sources and captures findings as cited Markdown files.
> Adapted from mattpocock/skills `research`.

## Trigger Conditions
- Load this skill when the user asks to research a topic, technology, or
  approach.
- Load when `../../../workflows/planning/planning.md` triggers the research
  phase.
- Load when choosing between libraries, tools, or architectures.

## Core Principle

Spin up a **background agent** to do the research, so you keep working while it
reads.

## The Research Agent's Job

1. **Investigate against primary sources** — official docs, source code, specs,
   first-party APIs — not a secondary write-up of them. Follow every claim back
   to the source that owns it.

2. **Write findings to a single Markdown file**, citing each claim's source.

3. **Save it where the repo already keeps such notes**; match the existing
   convention, and if there is none, put it somewhere sensible and say where.

## Process

### 1. Analyze the Question
- Parse the research question into its core components.
- Identify what is already known vs. what needs discovery.
- Check `../../../references/` for existing knowledge before searching
  externally.

### 2. Decompose into Subtopics
- Break the question into **non-overlapping subtopics**.
- Each subtopic must be independently researchable.
- Write a research plan file listing subtopics and the sources to consult for
  each.

### 3. Source Selection
Prioritize sources in this order:
1. **Official documentation**: the library/tool/framework's own docs.
2. **Primary repositories**: source code, issues, release notes.
3. **Reputable technical sources**: established blogs, conference talks,
   standards bodies.
4. **Community knowledge**: Stack Overflow, GitHub discussions, Discord (treat
   as supplementary, not authoritative).

### 4. Execute Research per Subtopic
- For each subtopic, perform **3–5 targeted searches** maximum.
- Extract: key facts, direct quotes, source URLs, and publication dates.
- Save findings to `../../../references/resource/` as structured notes.
- **Bias awareness**: flag vendor bias, sponsored content, outdated information,
  and opinion presented as fact.

### 5. Synthesize
- Combine subtopic findings into a unified research document.
- Structure: Overview → Core Findings → Comparison (if applicable) →
  Recommendations → References.
- Every assertion must cite at least one primary source with a clickable URL.
- Explicitly note confidence levels: confirmed, likely, speculative.

### 6. Present & Record
- Present findings to the user per `../../../workflows/user/user_input.md`.
- Record the final research document in `../../../references/resource/`.
- Update `../../../references/references.md` if the research changes
  architectural understanding.

## Constraints
- **Parametric memory is unverified**: do not rely on what the model "knows"
  without citing a source.
- **No single-source conclusions**: cross-validate across at least two
  independent sources.
- **Date everything**: note when each source was published and when the research
  was conducted.
- **Respect rate limits**: do not flood APIs with excessive requests.

## Expected Outputs
- Research plan with subtopic decomposition.
- Per-subtopic findings files in `../../../references/resource/`.
- Synthesized research document with cited sources.
- Recommendations with confidence levels.

## References
- `../../../references/references.md` — reference management.
- `../../../references/resource/` — external knowledge storage.
- `../../../workflows/planning/planning.md` — planning workflow.
- `../../../workflows/user/user_input.md` — user input protocol.
- Adapted from: mattpocock/skills `research`.
