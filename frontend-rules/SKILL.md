---
name: frontend-rules
description: Implement, refactor, or review React frontend features with shadcn/ui components, consistent formatting, reusable components and hooks, accessible forms, and behavior tests. Use when following frontend-rules for React pages, layouts, interactions, or API integrations.
---

# FRONTEND-RULES

Build maintainable React interfaces using **shadcn/ui as the only UI component system**. This skill provides engineering conventions; preserve explicit user decisions and existing project architecture. A folder reference such as `[frontend-rules](frontend-rules/)` identifies this package: read this file and the relevant references before implementing the requested feature.

## Inspect the project first

- Read applicable `AGENTS.md`, package manifests, lockfile, formatter/linter configuration, TypeScript configuration, `components.json`, theme styles, nearby pages/components, and test setup.
- Identify installed React/framework versions, routing, rendering boundaries, shadcn primitive family and style, component aliases, forms, and data-fetching conventions. Support the installed versions; do not upgrade the stack incidentally.
- Clarify required behavior, data, permissions, and interaction states when they cannot be inferred. Follow existing defaults for ordinary implementation choices.

## UI component boundary

- Use the project's local shadcn/ui components for buttons, inputs, selects, checkboxes, dialogs, menus, tables, notifications, and other supported controls.
- Create feature-specific components by composing these primitives. Do not introduce another UI kit, copy an arbitrary community registry, or hand-build substitutes for existing shadcn controls.
- Semantic HTML for page structure, headings, paragraphs, lists, images, links, and a native form container is allowed. Use shadcn components for supported controls and preserve correct semantics during composition.
- Reuse existing components before adding them. For missing primitives, follow the official shadcn component source/installation appropriate to the project's configuration. Do not mix primitive families or overwrite customized components blindly.
- shadcn's dependencies, the project's icon package, and non-UI tools such as routing, validation, and data fetching are compatible with this rule. A specialized UI requirement without a suitable shadcn composition needs an explicit user decision; do not silently choose another UI library.

## Core implementation rules

- Follow configured formatting and lint rules. If none exist, use the defaults in [formatting-and-reuse.md](references/formatting-and-reuse.md); do not reformat unrelated files or add tooling merely to satisfy the skill.
- Use focused function components, meaningful props, explicit types where the project uses TypeScript, and composition. Extract repeated domain concepts rather than creating a universal component with many boolean modes.
- Extract hooks for reusable stateful behavior; use ordinary functions for pure transformations. Keep state near its consumers and avoid duplicate derived state.
- Keep rendering pure, props/state immutable, and hooks compliant with React's rules. Do not silence hook linting to force an effect to work.
- Preserve accessibility: labels, keyboard interaction, focus, semantic roles, accessible names, and readable validation errors. Do not remove shadcn accessibility behavior while styling.
- Use shared theme tokens, existing variants, and the project's `cn` utility. Make layouts responsive and give loading, empty, error, success, and disabled states deliberate behavior.
- Keep transport and API error normalization outside presentational components when substantial/reused. Never treat hidden buttons or client-side validation as server authorization.
- Verify changed behavior with existing tools and relevant interaction tests; report checks actually run and any unavailable checks.

## Task-specific references

- For naming, formatting, component boundaries, TypeScript, and reusable code, read [formatting-and-reuse.md](references/formatting-and-reuse.md).
- For shadcn selection, styling, accessibility, forms, and validation, read [shadcn-and-forms.md](references/shadcn-and-forms.md).
- For hooks, effects, state, API integration, performance, testing, and completion, read [state-and-testing.md](references/state-and-testing.md).

Read only references relevant to the task. Keep changes focused on the requested feature; do not migrate the application to a new router, form library, state manager, or rendering model without a requirement.
