---
name: ui-design-guardrails
description: Use for UI, UX, styling, layout, component, or screen-flow work. Apply when the task changes visual design, copy hierarchy, states, interaction flow, responsive behavior, or component styling. Use together with DESIGN.md.
---

# UI Design Guardrails

This skill improves design consistency for UI tasks without relying on paid image or design models.

## Primary goal
Help Codex make UI changes that are:
- clear
- restrained
- consistent
- mobile-friendly
- aligned with the product's main flow
- less "AI-generated" in look and feel

## Required source of truth
Always consult `DESIGN.md` for project-level visual and UX rules before changing UI.

## Core rules

### 1. Clarity first
- Make the main action obvious
- Avoid competing primary actions
- Preserve clear information hierarchy
- Use copy, spacing, and layout to explain the screen before adding visual decoration

### 2. Respect existing patterns
- Reuse existing components, layout rhythms, and spacing patterns
- Avoid introducing a second visual language in one app
- Do not redesign unrelated areas while fixing one screen

### 3. Handle all meaningful states
For screens and flows, consider:
- loading
- empty
- error
- success

If the task affects a flow and one of these states is missing or broken, address it if it is in scope.

### 4. Avoid common AI-UI anti-patterns
- gratuitous gradients
- glassmorphism by default
- oversized corner radii everywhere
- giant shadows on every card
- nested card-on-card layouts
- decorative eyebrow text above every heading
- multiple accent colors fighting for attention
- vague CTA labels

### 5. Keep responsiveness intentional
- mobile first
- preserve the same mental model across screen sizes
- avoid dense desktop patterns collapsing into cramped mobile layouts
- keep tap targets comfortable

### 6. Minimalism over novelty
- prefer a cleaner layout over more decorative UI
- prefer better spacing and hierarchy over more components
- prefer stable readable defaults over inventive styling

## Design workflow
For non-trivial UI tasks:
1. identify the main user action on the screen
2. identify what content is primary, secondary, and supporting
3. check the screen against `DESIGN.md`
4. make the smallest layout/styling/content changes that improve the flow
5. verify mobile and desktop behavior if relevant
6. verify loading, empty, error, and success states if relevant

## Implementation guidance for React/JS/TS
- prefer local UI fixes over broad component rewrites
- avoid premature extraction of design systems or hooks
- keep visual changes traceable to the task
- if adding new tokens or reusable styles, do so only when the task clearly benefits

## Reference files
- `references/ANTI-SLOP-RULES.md`
- `references/DESIGN-REVIEW-CHECKLIST.md`
- `references/TASTE-RULES.md`
- `references/REACT-UI-PATTERNS.md`
