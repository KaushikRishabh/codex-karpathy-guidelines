# DESIGN.md

## Product intent
Build a clean, trustworthy, fast-feeling product UI. The interface should feel intentional, modern, and human — not flashy, ornamental, or overly "AI-generated."

## Design priorities
1. Clarity over novelty
2. Fast comprehension over visual drama
3. One obvious next action per screen
4. Strong mobile usability
5. Consistency across flows
6. Complete state handling: loading, empty, error, success

## Visual direction
- Overall feel: clean, calm, crisp, product-focused
- Avoid "luxury dashboard" aesthetics unless explicitly requested
- Prefer strong hierarchy, spacing, and copy over decorative effects

## Anti-patterns
- Do not add gradients unless the brand already uses them
- Avoid glassmorphism and translucent floating cards by default
- Avoid giant corner radii everywhere
- Avoid heavy shadows on every surface
- Avoid eyebrow labels, decorative subtitles, and filler marketing copy unless needed
- Avoid multiple competing CTAs on one screen
- Avoid hiding key actions inside menus when the main flow needs them visible

## Layout rules
- Keep page structure simple: header, primary content, secondary content
- Use spacing to create hierarchy instead of extra borders and shadows
- Prefer fewer containers with better rhythm over nested card-on-card layouts
- Mobile layouts should prioritize vertical flow and large tap targets
- Desktop layouts should preserve the same mental model as mobile

## Typography
- Use a small number of text sizes
- Reserve the largest size for the main page heading only
- Body copy should stay readable and compact
- Supporting text should be quieter but still legible
- Avoid too many font weights in one screen

## Spacing scale
Use a small spacing system and stay consistent:
- 4
- 8
- 12
- 16
- 24
- 32
- 48

Default choices:
- component internal spacing: 12–16
- section spacing: 24–32
- page spacing: 24 mobile, 32–48 desktop

## Radius and shadows
- Default radius: subtle to medium
- Use larger radii only for intentional hero elements or media
- Use shadows sparingly
- Prefer border + contrast separation over large soft shadows

## Color usage
- Use a restrained palette
- One primary accent should do most of the action work
- Error, warning, success colors should be functional, not decorative
- Avoid using multiple saturated accent colors in the same view

## Buttons and CTAs
- One primary CTA per screen or section
- Secondary actions should look secondary
- Button text should describe the action clearly
- Avoid vague CTA labels like "Continue" when something more specific is possible

## Forms
- Keep labels clear and close to inputs
- Show validation near the field
- Preserve enough contrast and spacing for fast scanning
- Required vs optional fields should be obvious
- Avoid multi-column forms on mobile unless there is a clear gain

## State design
Every meaningful flow should consider:
- Loading
- Empty
- Error
- Success

Questions to answer during UI work:
- What does the user see while waiting?
- What if there is no data yet?
- What if the action fails?
- What confirms success?

## Responsive behavior
- Mobile first
- No horizontal scrolling for primary flows
- Controls should remain reachable with thumbs
- Dense desktop layouts must not become cramped mobile layouts

## Implementation rule for Codex
When working on UI:
1. Read this file first
2. Keep the user's main flow obvious
3. Avoid speculative redesigns
4. Make the smallest design-consistent change that solves the task
5. Prefer consistency with this file over one-off styling experiments
