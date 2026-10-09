# Architecture, SOLID, and clean code

## Layer placement

The default request path is route → middleware → request/controller → optional action → model/database → resource. Authorize before a protected operation. This describes responsibilities, not a requirement for a class at every step.

Controllers translate HTTP into a use case. Simple Eloquent CRUD can remain there; extract workflows with meaningful rules, multiple writes, or reuse. Use invokable controllers for a single operation when consistent with the project. Avoid arbitrary method or line-count limits.

Requests describe incoming data. Models express persisted data and entity-local behavior. Resources describe outgoing JSON; they must not update records or orchestrate workflows. Middleware handles shared request concerns, such as authentication or tenant resolution, rather than feature-specific operations.

Actions such as `PlaceOrder` own a use case. Services such as `ShippingRateCalculator` own a cohesive capability. Inject collaborators through constructors and keep them resolvable through the project's container setup. Keep required business steps explicit; avoid hiding them in model observers. Observers can still handle suitable lifecycle concerns.

Suggested locations, only when compatible with the project:

```text
app/Http/Controllers/Api/OrderController.php
app/Http/Requests/Orders/StoreOrderRequest.php
app/Http/Resources/OrderResource.php
app/Models/Order.php
app/Policies/OrderPolicy.php
app/Actions/Orders/PlaceOrder.php             # if a workflow warrants it
app/Contracts/PaymentGateway.php             # if a boundary warrants it
app/Integrations/Payments/ProviderGateway.php # boundary implementation
tests/Feature/Orders/StoreOrderTest.php
tests/Unit/Orders/OrderTotalTest.php
```

## Apply SOLID to real decisions

| Principle | Laravel application | Avoid |
| --- | --- | --- |
| Single responsibility | Request validates, policy authorizes, action places an order, resource serializes | Controller combining validation, payment transport, inventory, and JSON formatting |
| Open/closed | Add a shipping strategy behind a stable contract when multiple real strategies exist | Speculative plugin systems for a straightforward calculation |
| Liskov substitution | All gateway implementations honor the same inputs, results, and failure semantics | Implementation silently changing currency or failing for supported operations |
| Interface segregation | Separate capabilities callers need, such as capture and refund | Huge interfaces forcing unsupported or empty methods |
| Dependency inversion | Business workflow depends on a payment contract, bound to a provider at composition | Vendor SDK hard-coded inside the business decision |

SOLID does not require an interface per class. Concrete injection is sufficient without a meaningful abstraction. Laravel facades and Eloquent are valid tools; use explicit collaborators when business dependencies or testing seams need clarity. Do not replace framework conventions just to approximate another framework's architecture.

## Clean-code decisions

- Name domain intent: `ApproveInvoice`, `calculateSubtotal`, `canCancel`. Avoid vague `Manager`, `Helper`, or `processData` names unless their role is unambiguous.
- Keep methods at one useful abstraction level. Extract cohesive concepts rather than wrappers that obscure flow.
- Use guard clauses. Avoid boolean switches combining unrelated operations; use distinct methods or meaningful value types.
- Type parameters/returns accurately. Follow project PHP style and formatter. Use enums only when installed PHP and existing models support them.
- Represent money using appropriate decimals/value objects or integer minor units with a currency, rather than binary floating-point totals. Choose quantity precision deliberately.
- Distinguish missing, null, empty, zero, and false. PATCH changes only present allowed fields; preserve explicit null when permitted.
- Avoid mutable global state, hidden request access, and logging that changes behavior. Read environment-backed values through project configuration outside config files.
- Reuse a concept when its shared rule is real. Similar-looking code may express different business rules; delay abstraction until its contract is clear.
- Catch exceptions only to recover, translate at a boundary, or add useful context. Preserve causes and route unexpected failures through existing reporting.
- Refactor incrementally, preserving behavior unless changes are requested. Avoid unrelated renaming, directory migrations, and response changes.

## Choosing an abstraction

Use Eloquent directly for single-table CRUD. Consider a query object for a repeated complex report, an adapter/contract for a provider, an action for a multi-step operation, and a DTO when boundary data needs stable typing or invariants. Do not convert every validated array into a class by default.

For each new layer, explain its responsibility, caller, and removed complexity. Reconsider layers that only forward identical arguments.
