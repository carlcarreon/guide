---
name: backend-rules
description: Implement, refactor, or review Laravel backend features using clear layer responsibilities, SOLID principles, secure APIs, and behavior tests. Use for Laravel routes, controllers, requests, resources, models, policies, actions, jobs, and database changes.
---

# BACKEND-RULES

Produce maintainable Laravel code with explicit business rules and a stable HTTP contract. These are this skill's preferred engineering conventions, not requirements imposed by Laravel. Follow explicit user decisions and established project conventions; improve only the requested feature.

## Start with the project

- Read applicable `AGENTS.md`, `composer.json`, relevant entries in `composer.lock`, routes, nearby implementations, and test configuration. Identify installed PHP/Laravel versions, authentication, database, API conventions, and architecture.
- Use APIs supported by those installed versions. Check matching official Laravel documentation when uncertain; do not upgrade dependencies or copy newer skeleton configuration for an unrelated feature.
- Define input, actor, authorization, business invariants, output, and failures. Clarify only missing decisions that affect correctness; use established defaults elsewhere.

## Choose the smallest sufficient design

| Component | Responsibility | When to use |
| --- | --- | --- |
| Route | Map methods and URIs; apply middleware | HTTP entry points |
| Controller | Coordinate input, authorization, operation, response | HTTP entry points |
| Form Request | Validate/normalize input; optionally delegate authorization | Substantial or reusable HTTP input rules |
| API Resource | Define public JSON fields and relationships | Structured model-based JSON APIs |
| Model | Persistence, relationships, casts, scopes, local domain behavior | Eloquent-backed data |
| Policy / Gate | Decide what an actor may do | Protected operations |
| Action / Service | Execute a cohesive business operation | Workflows, reuse, transactions, or complexity |
| Query object | Encapsulate a substantial read query | Complex/reused filtering or reporting |
| Job | Execute background work | Work benefiting from asynchronous execution |
| Job batch | Track a related set of queued operations | Bulk work needing shared progress, completion, or cancellation |
| Cache | Reuse costly results under a freshness contract | Repeated expensive reads or computations |
| Event / Listener | Announce a fact; handle independent reactions | Decoupled reactions to completed operations |
| Migration | Define schema and database constraints | Schema changes |
| Factory / Seeder | Build test fixtures / deliberate seed data | Tests or reproducible local setup |
| DTO / Value object | Give boundary data a meaningful typed shape | When ambiguity or invariants justify it |
| Interface / Adapter | Isolate a real external/interchangeable dependency | Provider boundaries or real variation |
| Repository | Offer domain-specific persistence operations | When a demonstrated persistence boundary warrants it |

Do not generate every layer for every endpoint. Simple CRUD may use a controller, request, resource, policy, and Eloquent directly. Extract substantial workflows into an action or cohesive service. Prefer the existing Actions or Services convention; do not duplicate the same operation across both. Avoid generic base repositories, pass-through services, and interfaces for every class.

## Essential rules

- Declare each controller-action route explicitly (`index`, `create`, `store`, `show`, `edit`, `update`, `destroy` as needed). Do not use `Route::resource`, `Route::apiResource`, or their bulk variants. Organize feature routes with `prefix(...)` and `group(...)`, a route-name prefix, and shared middleware where applicable. This user-selected routing convention takes priority over existing resource-registration shorthand for routes being added or changed.
- Keep HTTP concerns at the boundary. Actions must work from controllers, commands, or jobs without reading the global request or returning HTTP responses. Pass actor and data explicitly.
- Validate input and authorize independently. Explicitly assign writable fields; validated input alone does not establish permission to change a field. Derive ownership/tenant identifiers from trusted context.
- Scope reads and writes to permitted records. Route model binding resolves a record; it does not grant access.
- Keep public response fields explicit. Preserve established envelopes, pagination, status codes, and error formats.
- Protect invariants with database constraints and appropriate transactions/concurrency controls. Pre-write checks alone do not prevent races.
- Keep transactions short. Separate irreversible external effects from retryable database work and make retryable operations idempotent where needed.
- Load relationships deliberately, bound list endpoints, and avoid hidden queries during serialization.
- Use caching when repeated work and acceptable staleness justify it. Define scoped keys, TTL, invalidation, and miss behavior; preserve authorization and tenant isolation.
- Use Laravel job batching for related bulk work when shared progress/completion is useful. Keep jobs bounded and idempotent, and define cancellation and partial-failure behavior. Use chains when execution order is required.
- Prefer meaningful names, focused methods, guard clauses, explicit dependencies, and accurate types. Comment on intent or constraints rather than narrating code.
- Test observable behavior proportionally to the change, especially authorization, invalid input, business failures, and state changes. Use installed tooling; do not add packages merely to satisfy this skill.

## Read task-specific guidance

- For layer placement, naming, SOLID, clean code, or refactoring, read [architecture.md](references/architecture.md).
- For controllers, requests, resources, routes, policies, security, or API behavior, read [http-and-security.md](references/http-and-security.md).
- For models, migrations, transactions, jobs, performance, tests, or completion checks, read [data-and-testing.md](references/data-and-testing.md).
- For caching, cache invalidation, bulk background work, or job batching, read [caching-and-batches.md](references/caching-and-batches.md).

Read relevant references before implementing. Do not create unrelated modules from the component table.

## Finish the task

Run relevant tests and configured formatting/static analysis appropriate to the change. Inspect the diff for accidental API changes, schema risks, unnecessary abstractions, and unauthorized access paths. Report changes, passing checks, and checks that could not run. For review requests, present concrete findings with locations and consequences; do not rewrite the application unless asked.
