# React UI patterns

Use these patterns when making React UI changes.

## Preferred patterns
- keep state near where it is used
- preserve existing component boundaries where possible
- add explicit UI states instead of hiding them in conditionals
- prefer simple layout composition over clever abstractions
- prefer CSS modules / Tailwind / existing styling pattern already used by the repo

## Good UI fix examples
- add loading, empty, error, success states to one screen
- improve CTA hierarchy in one component
- tighten form layout and validation messaging
- make one page responsive without changing unrelated routes
- reduce noise in a card/list/table layout

## Avoid
- rewriting a page into a new architecture for a visual tweak
- creating a mini design system for one component
- extracting hooks or providers before they are justified
- mixing major copy rewrite, layout rewrite, and styling rewrite in one task

## Verification
After a React UI change, try to verify:
- the screen still renders
- the main action works
- edge states still render
- mobile layout is not broken
