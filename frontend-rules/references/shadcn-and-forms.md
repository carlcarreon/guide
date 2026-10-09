# shadcn/ui, styling, and forms

## Use the local component system

shadcn/ui provides component source that lives in the application. Inspect local components and `components.json` before assuming an import path or API. Reuse installed primitives and add official components using the project-compatible workflow when needed. [Introduction](https://ui.shadcn.com/docs).

Use Button for actions, Input/Textarea for supported text controls, and the appropriate installed Select, Checkbox, Switch, Dialog, AlertDialog, DropdownMenu, Table, Tooltip, or feedback component. Build domain-specific widgets by composing these. Do not implement a clickable `div`, raw styled button, or custom focus-trapping modal as a substitute.

Structural HTML, text, a native `<form>`, and semantic links are allowed. Links navigate; buttons perform actions. Use the local component's supported composition mechanism for router links; avoid nested anchors/buttons and do not assume Radix `asChild` applies to Base UI components. Use the primitive family already selected by the project.

Do not introduce Material UI, Ant Design, Chakra, Mantine, or another competing UI kit. Non-UI libraries for validation, transport, routing, or state are allowed when the project needs them. Dependencies used by official shadcn components are compatible with this rule. If a specialized widget cannot be composed appropriately, explain the gap and obtain the user's decision before introducing an alternative UI system.

Adding a component must preserve local customization. Inspect generated changes, dependencies, aliases, CSS, and registry source. Do not overwrite all components, run a new initialization over an existing setup, or upgrade everything to obtain one missing control.

## Styling

Use existing semantic tokens such as background, foreground, muted, border, and destructive, and supported size/variant props. Follow the configured theme and dark-mode strategy; avoid feature-specific hard-coded palettes that bypass them. Use the project's `cn` utility for conditional class composition. [Theming](https://ui.shadcn.com/docs/theming).

Keep spacing, typography, radius, and page width consistent with nearby screens. Prefer responsive layout utilities and content-driven sizing over arbitrary fixed dimensions. Ensure long labels, empty content, and narrow viewports work. Do not remove focus indicators or meaningful disabled/error styling. Respect reduced-motion preferences for added animation.

Avoid changing a global primitive to achieve one screen's appearance. Prefer a supported variant or a feature wrapper. Modify a shared primitive deliberately only when the requested design-system behavior belongs to every caller.

## Forms

Use the project's existing form/validation approach. React Hook Form and a schema validator are a reasonable option when already installed or when a new form stack is actually in scope; they are not mandatory for every input. Use the project's compatible shadcn Field components or existing Form integration rather than assuming a particular generation exists. [Official React Hook Form guidance](https://ui.shadcn.com/docs/forms/react-hook-form), [Field guidance](https://ui.shadcn.com/docs/components/base/field).

- Define field names and input shapes explicitly. Choose defaults deliberately; avoid controlled/uncontrolled switching. Use the control's actual API for text, selection, and checked values.
- Keep UI input values distinct from API payload types when conversion is needed. Empty numeric input must not silently become zero. Parse numbers, dates, and monetary values under the API contract rather than blindly casting them.
- Use a real form submission path, explicit submit/cancel button types, and appropriate input type, autocomplete, and input mode. Preserve Enter-to-submit behavior where appropriate.
- Associate labels with controls. Connect descriptions/errors through stable IDs and supported `aria-describedby`/`aria-invalid` behavior. Include required instructions and field-level error messages rather than relying only on a toast.
- Validate for feedback and display server validation as field errors when possible. Unknown/global errors belong in an accessible form-level message. Frontend validation does not replace server validation or authorization.
- Preserve user input on failures. Reset only after a confirmed success or an intentional cancel/reset. Define how edit forms behave when another record loads or remote data changes; avoid resetting on every render/refetch.
- Prevent duplicate submissions while pending. Show useful pending, failure, and success states. Retry only under the API's safe/idempotent semantics.
- Apply the project's unsaved-change handling when relevant. Do not introduce a navigation blocker for unrelated simple controls.

## Accessibility and interaction states

Preserve shadcn keyboard/focus behavior and dialog semantics. Give dialogs meaningful titles/descriptions, icon-only actions accessible names, and table headings meaningful labels. Confirm destructive actions with appropriate AlertDialog behavior when warranted. Tooltips are supplementary information, not replacements for labels.

Plan loading, empty, filtered-empty, error/retry, success, and permission-denied states according to the feature. Announce important status changes where needed. Avoid displaying fake success before an operation is confirmed unless an intentional optimistic flow also handles rollback.

Check keyboard operation, dialog focus restoration, error discoverability, responsive layouts, and the project's supported themes. A component library alone does not make composed screens accessible.
