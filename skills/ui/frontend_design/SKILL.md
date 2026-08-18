---
version: 2.0.0
updated: 2026-08-18
id: skill_ui_frontend_design
links:
  - ../creation/SKILL.md
  - ../../../design/ux/
  - ../../../design/design.md
  - ../../../CONVENTIONS.md
  - ../../../STACK.md
---

# Skill: Frontend Design

> **Purpose**: Generate production-quality, visually distinctive frontend
> designs that avoid generic AI aesthetics. Enforces deliberate design decisions
> — typography, color, spacing, motion, and layout — rather than defaulting to
> low-effort patterns. Adapted from anthropic/skills `frontend-design`.

## Trigger Conditions
- Load this skill when designing or building user-facing interfaces.
- Load alongside `../creation/SKILL.md` when the task involves visual design.
- Load when the user asks for a "beautiful", "modern", or "polished" UI.

## Core Philosophy

Approach this as the design lead at a small studio known for giving every
client a visual identity that could not be mistaken for anyone else's. This
client has already rejected proposals that felt templated, and is paying for a
distinctive point of view: make deliberate, opinionated choices about palette,
typography, and layout that are specific to this brief, and take one real
aesthetic risk you can justify.

## Ground It in the Subject

If the brief does not pin down what the product or subject is, pin it yourself
before designing: name one concrete subject, its audience, and the page's single
job, and state your choice. The subject's own world, its materials, instruments,
artifacts, and vernacular, is where distinctive choices come from. Build with
the brief's real content and subject matter throughout.

## Design Principles

**The hero is a thesis.** Open with the most characteristic thing in the
subject's world, in whatever form makes sense for it: a headline, an image, an
animation, a live demo, an interactive moment. Be deliberate with your choice: a
big number with a small label, supporting stats, and a gradient accent is the
template answer, only use if that's truly the best option.

**Typography carries the personality of the page.** Pair the display and body
faces deliberately, not the same families you would reach for on any other
project, and set a clear type scale with intentional weights, widths, and
spacing. Make the type treatment itself a memorable part of the design, not a
neutral delivery vehicle for the content.

**Structure is information.** Structural devices, numbering, eyebrows, dividers,
labels, should encode something true about the content, not decorate it. Many
generic designs use numbered markers (01 / 02 / 03), but that's only appropriate
if the content actually is a sequence. Question if choices like numbered markers
actually make sense before incorporating them.

**Leverage motion deliberately.** Think about where and if animation can serve
the subject: a page-load sequence, a scroll-triggered reveal, hover
micro-interactions, ambient atmosphere. An orchestrated moment usually lands
harder than scattered effects; choose what the direction calls for. However,
sometimes less is more, and extra animation contributes to the feeling that the
design is AI-generated.

**Match complexity to the vision.** Maximalist directions need elaborate
execution; minimal directions need precision in spacing, type, and detail.
Elegance is executing the chosen vision well.

## Process: Brainstorm, Explore, Plan, Critique, Build, Critique Again

**For calibration:** AI-generated design right now clusters around three looks:
(1) a warm cream background (near #F4F1EA) with a high-contrast serif display
and a terracotta accent; (2) a near-black background with a single bright
acid-green or vermilion accent; (3) a broadsheet-style layout with hairline
rules, zero border-radius, and dense newspaper-like columns. All three are
legitimate for some briefs, but they are defaults rather than choices, and they
appear regardless of subject. Where the brief pins down a visual direction,
follow it exactly — the brief's own words always win. Where it leaves an axis
free, don't spend that freedom on one of these defaults.

**Work in two passes:**

1. **Brainstorm a short design plan** based on the human's design brief: create
   a compact token system with color, type, layout, and signature.
   - Color: describe the palette as 4–6 named hex values.
   - Type: the typefaces for 2+ roles (a characterful display face that's used
     with restraint, a complementary body face, and a utility face for captions
     or data if needed).
   - Layout: a layout concept, using one-sentence prose descriptions and ASCII
     wireframes to ideate and compare.
   - Signature: the single unique element this page will be remembered by that
     embodies the brief in an appropriate way.

2. **Review that plan against the brief before building:** if any part of it
   reads like the generic default you would produce for any similar page rather
   than a choice made for this specific brief — revise that part, say what you
   changed and why. Only after you've confirmed the relative uniqueness of your
   design plan should you start to write the code, following the revised plan
   exactly and deriving every color and type decision from it.

## Restraint and Self-Critique

Spend your boldness in one place. Let the signature element be the one memorable
thing, keep everything around it quiet and disciplined, and cut any decoration
that does not serve the brief. Not taking a risk can be a risk itself! Build to
a quality floor without announcing it: responsive down to mobile, visible
keyboard focus, reduced motion respected. Critique your own work as you build,
taking screenshots if your environment supports it – a picture is worth 1000
tokens. Consider Chanel's advice: before leaving the house, take a look in the
mirror and remove one accessory.

## State Design

Per `../../../CONVENTIONS.md` §2, design all three states for every component:
- **Empty / zero state**: meaningful empty states with guidance, not blank
  screens.
- **Populated state**: the primary view with real content.
- **Errored state**: clear error messaging with recovery paths.

Design loading, skeleton, and transition states between these three.

## Implementation

- Build the design following `../../../STACK.md` for the project's frontend
  stack.
- Use CSS variables or design tokens for colors, spacing, and typography — never
  hardcode values in components.
- Document the design system in `../../../design/ux/` for downstream
  consistency.
- Be careful of structuring your CSS selector specificities. It's easy to
  generate CSS classes that cancel each other out.

## Verification

- Verify all three states are implemented and visually correct.
- Verify responsiveness at all target breakpoints.
- Verify accessibility: contrast, keyboard navigation, screen reader
  compatibility.
- Run both test tracks per `../../../references/tests/tests.md`.

## Expected Outputs
- Design system documented in `../../../design/ux/` (typography, colors,
  spacing, motion).
- UI implementation with all states covered.
- Responsive across target breakpoints.
- Accessible (WCAG AA minimum).

## References
- `../creation/SKILL.md` — UI/UX creation workflow.
- `../../../design/ux/` — UX design specifications.
- `../../../design/design.md` — design philosophy.
- `../../../CONVENTIONS.md` — zero-state convention.
- `../../../STACK.md` — frontend stack and tooling.
- Adapted from: anthropic/skills `frontend-design`.
