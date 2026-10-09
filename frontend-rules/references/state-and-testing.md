# React behavior, data, and verification

## Render and hooks

Keep render pure and props/state immutable. Render components through JSX rather than calling their functions. Call ordinary hooks at the top level of components/custom hooks before early returns; do not call them in conditions, loops, or callbacks. Honor the installed React API's documented exceptions, such as `use`, rather than generalizing them to other hooks. [React rules](https://react.dev/reference/rules).

State updates should create new values; use functional updates when relying on previous state. Avoid mutating arrays, objects, or hook results. Keep side effects outside render and clean up subscriptions/timers when required.

Use stable keys from data identity for dynamic lists. Do not generate random keys during render or use indexes where reordering/insertion would attach state to the wrong item. [Rendering lists](https://react.dev/learn/rendering-lists).

## State and effects

- Keep local UI state near consumers. Lift shared state only to the nearest appropriate owner; use URL state for filters/pagination when routing and sharing behavior warrant it.
- Calculate derived values during render when practical. Avoid copying props or fetched data into state without a deliberate editable-draft requirement.
- Use effects to synchronize with external systems, not to handle a submit action or keep redundant calculated state in sync. Handle event-driven operations in event handlers.
- Supply accurate effect dependencies and cleanup. Do not suppress exhaustive-deps, use empty arrays to hide stale closures, or create chains of effects for an ordinary state transition.
- Use the project's router loaders, framework data facilities, or query library when already established. If fetching in an effect is necessary, prevent stale responses from overwriting newer state and handle cancellation appropriately.

[Effect guidance](https://react.dev/learn/you-might-not-need-an-effect).

## Data boundaries and framework integration

Keep server state in the established cache/data layer where present; avoid duplicating it in unrelated global state. Normalize transport responses and errors in a reusable API boundary when warranted. Separate backend field errors from network/auth failures. Update or invalidate the relevant cache after mutations and preserve filters/pagination deliberately.

Follow the application's router and rendering model. In Next.js, preserve server/client boundaries and keep secrets/server-only modules off the client. In Laravel with Inertia, use the installed page, routing, and form helpers rather than layering a second client router or duplicating an existing form state system. In Vite client applications, preserve the existing router/auth transport. These are integration choices, not reasons to migrate frameworks.

Treat public client environment values as public. Do not embed private keys or trust client permission flags as authorization. Render untrusted text normally; use raw HTML only with a justified trusted/sanitized contract. Avoid storing sensitive auth tokens in a new client storage scheme without an explicit auth design requirement.

## Performance choices

Optimize demonstrated or clearly relevant problems. Do not add `memo`, `useMemo`, or `useCallback` everywhere. Measure expensive computations/rendering before introducing complexity; account for the project's React Compiler setup if present. Keep interaction state localized and paginate or use the established virtualization strategy for genuinely large datasets. A missing virtualization component does not permit adding a competing UI kit.

## Meaningful verification

Use existing test tooling, such as React Testing Library, Vitest/Jest, or Playwright where installed. Avoid introducing a test framework incidentally. Test user-visible behavior through roles and accessible labels, not internal state or every utility invocation.

| Change/risk | Useful verification |
| --- | --- |
| Form submission | Valid payload, pending state, invalid input, server errors, preserved input |
| Mutations | Confirmation, failure handling, duplicate-submit prevention, updated data |
| Permission-dependent UI | Correct affordances while server enforcement remains required |
| Async reads | Loading, empty, error/retry, and stale response handling |
| Shared component/hook | Public contract and meaningful interaction boundaries |
| Modal/menu | Keyboard use, accessible name, focus behavior |
| Responsive layout | Narrow viewport, long text, supported themes |

Run configured formatting checks, lint, typecheck if applicable, relevant tests, and a build when integration/boundary changes warrant it. Inspect package scripts and use the lockfile's package manager; do not assume script names or invoke an uninstalled formatter through `npx` just to validate.

Manually inspect relevant interaction, loading, error, accessibility, and responsive behavior when a runnable preview is available. Explain unavailable checks rather than claiming they passed. Review the diff for unrelated formatting, duplicated concepts, raw substitutes for shadcn controls, and accidental dependency changes.
