# Formatting and reusable React code

## Formatting policy

React does not prescribe quote style, semicolons, indentation, or a universal folder structure. This skill's defaults apply only when the project has no established convention.

Use the configured Prettier or equivalent formatter for layout, and ESLint for configured code-quality and React rules. Respect `.editorconfig`, overrides, ignore files, and import-sort tooling. Avoid formatter/linter configurations that fight each other. [Prettier configuration](https://prettier.io/docs/configuration).

Fallback style:

| Concern | Default |
| --- | --- |
| Indentation | 2 spaces |
| Quotes | Double quotes in JS/TS and JSX |
| Semicolons | Use them |
| Trailing commas | Wherever supported by the target tooling |
| Print width | 100, with formatter-managed wrapping |
| Components/types | PascalCase: `ProductForm`, `ProductFormProps` |
| Functions/variables | camelCase: `saveProduct`, `isPending` |
| Hooks | `use` prefix for actual hooks: `useProducts` |
| Component files | kebab-case: `product-form.tsx` |
| Other files | kebab-case: `use-products.ts`, `product-schema.ts` |
| JSX files | `.tsx` for TypeScript, existing `.jsx`/JS convention otherwise |
| Exports | Named exports for application components; framework-required default exports remain valid |

These defaults are not a reason to rename established PascalCase files or migrate JavaScript to TypeScript. Preserve generated shadcn source conventions and framework file names. Apply defaults to new work only.

If creating a formatter configuration is in scope, an example is:

```json
{
  "tabWidth": 2,
  "singleQuote": false,
  "jsxSingleQuote": false,
  "semi": true,
  "trailingComma": "all",
  "printWidth": 100
}
```

## Component responsibilities

| Layer | Responsibility |
| --- | --- |
| Route/page | Route data, permissions context, page composition |
| Feature component | A meaningful domain interaction such as `ProductForm` |
| Shared composition | A reused concept such as `EmptyState` built from shadcn primitives |
| `components/ui` | Local shadcn primitives and deliberate design-system changes |
| Hook | Cohesive stateful behavior or subscription |
| API module | Request construction and normalized responses/errors |
| Utility/schema/type | Pure transformation, validation definition, or boundary shape |

Do not put feature-specific business behavior in shared shadcn primitives. Keep a component near its feature until it is genuinely shared. Existing project placement wins; avoid a directory migration just to enforce this example:

```text
src/components/ui/button.tsx
src/components/shared/empty-state.tsx
src/features/products/components/product-form.tsx
src/features/products/hooks/use-products.ts
src/features/products/api/products.ts
src/features/products/schemas/product-schema.ts
```

## Reuse and clean code

- Prefer composition through children or focused slots over components with many mode flags. Extract a domain concept when it repeats or clarifies substantial behavior, not every small markup fragment.
- Keep components focused; avoid arbitrary line-count limits. Split when responsibility, reuse, or readability improves.
- Make prop contracts explicit: data flows in, callbacks report intent. Use `onSave`, `onCancel`, and meaningful arguments rather than a child reaching into its parent's state.
- Model mutually exclusive states with discriminated unions when TypeScript helps prevent impossible combinations. Avoid `any`, unjustified non-null assertions, and casts that disguise mismatched API data.
- Derive wrapper props from the underlying component where appropriate. Preserve supported refs/composition behavior according to the installed React and shadcn implementation; do not assume one ref API works for every version.
- Use `import type` according to project configuration. Remove dead code/imports. Let configured tooling order imports; do not introduce manual churn.
- Use ordinary functions for formatting, filtering, and calculation. Extract custom hooks to share stateful logic, not as arbitrary wrappers around pure functions. A hook shares logic; separate hook calls do not automatically share state. [Custom hooks](https://react.dev/learn/reusing-logic-with-custom-hooks).
- Apply single responsibility through focused components/functions; isolate replaceable external dependencies at real boundaries. Do not create an interface, context, or adapter for every function in the name of SOLID.
- Avoid broad global contexts for a local interaction. Avoid blanket barrel exports that cause cycles or hide server/client boundaries.
- Comments explain a constraint or intent. Name functions and values clearly enough that comments need not narrate JSX.

## Example: a shared shadcn composition

This illustrates a meaningful reused button state. Use only if the application repeats this behavior; adapt imports to configured aliases.

```tsx
import type { ComponentProps } from "react";
import { Button } from "@/components/ui/button";

type SubmitButtonProps = ComponentProps<typeof Button> & {
  isPending: boolean;
  pendingLabel?: string;
};

export function SubmitButton({
  isPending,
  pendingLabel = "Saving…",
  disabled,
  children,
  type = "submit",
  ...props
}: SubmitButtonProps) {
  return (
    <Button
      {...props}
      type={type}
      disabled={disabled || isPending}
      aria-busy={isPending}
    >
      {isPending ? pendingLabel : children}
    </Button>
  );
}
```

Preserve a visible form-level pending/success/error message when users need status announcements; a changed button label alone may not be announced by assistive technology.
