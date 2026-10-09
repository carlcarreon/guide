# Persistence, background work, and verification

## Models and schema

Keep relationships, casts, scopes, and entity-local behavior on models. Use query objects for substantial reads. Explicitly assign writable fields and respect mass-assignment settings; do not globally unguard models to implement a feature.

Use migrations for schema changes. Match foreign-key types, nullability, defaults, uniqueness, and indexes to queries/invariants. Make nullability deliberate. Avoid cascading deletion of historical data without a domain requirement. Preserve applied migrations and add a new migration when expected. Explain backfills and deployment order for populated columns.

Bound pagination and order deterministically. Eager-load required relationships/counts. Avoid serialization-loop queries and loading whole tables for database aggregates. Inspect query plans for demonstrated performance issues; caching is not a default cure.

For justified caching or related bulk background operations, read [caching-and-batches.md](caching-and-batches.md) for freshness, invalidation, isolation, and batch lifecycle rules.

## Transactions and concurrency

Wrap related writes that must succeed together in a transaction owned by the cohesive operation. Laravel supports transaction callbacks and deadlock retries; keep callbacks safe to rerun when retries are enabled. [Database documentation](https://laravel.com/framework/docs/13.x/database).

Protect contested state through atomic conditional writes, locking, or optimistic concurrency as appropriate. Constraints are the final guard for uniqueness/relationships. Validation and pre-write existence checks do not close races. When locking is justified, lock inside the transaction in a consistent order.

Do not hold a transaction during slow provider requests. Coordinate database state and external effects through explicit operation states, idempotency, reconciliation, or compensation as needed. A local transaction cannot make an external payment atomic.

## Jobs, events, and side effects

Queue work when asynchronous execution benefits the feature. Design for retries, duplicates, missing records, and stale state. Pass stable identifiers or deliberate immutable data; reload/recheck required state. Choose bounded retries, timeouts, backoff, and failure handling for the provider.

Dispatch database-dependent jobs after commit where needed; rollback must not send work assuming committed state. After-commit dispatch alone does not guarantee delivery across a crash; consider a transactional outbox when that reliability is required. [Queue documentation](https://laravel.com/framework/docs/13.x/queues).

Use events for meaningful completed facts and independent reactions. Keep mandatory synchronous invariants in the main operation. Check the queue driver: a synchronous driver does not provide background processing. Fake external transport in tests while checking actual operation state changes.

## Tests that establish behavior

Use the project's Pest/PHPUnit conventions, database reset strategy, factories, and helpers. HTTP tests exercise responses and behavior. [HTTP test documentation](https://laravel.com/framework/docs/13.x/http-tests).

Choose tests according to introduced risks:

| Risk | Useful evidence |
| --- | --- |
| Wrong actor accesses data | Unauthenticated, forbidden, cross-owner/tenant cases as applicable |
| Invalid input changes state | Contract error and unchanged state for invalid/boundary inputs |
| Privileged fields are writable | Crafted owner/tenant/role input cannot change protected values |
| Incorrect operation | Intended response and persisted relationships/state |
| Broken update semantics | Omitted fields remain unchanged; allowed nulls work |
| Partial writes | Rollback leaves no partial committed state |
| Duplicate side effects | Retry/duplicate delivery keeps expected operation count/result |
| Leaked or broken output | Sensitive fields absent; pagination/relationship contract preserved |
| Incorrect pure business rule | Focused unit tests of meaningful rule boundaries |

Avoid mocking each Eloquent call or asserting helper invocation counts. Assert observable outcomes; fake providers at external boundaries. Verify important concurrency behavior using the production database engine when SQLite would mislead.

## Completion checks

Run affected tests first and required project checks. Common commands when available:

```bash
php artisan test --filter=StoreOrderTest
vendor/bin/pint --dirty
composer run test
# Use the configured PHPStan/Larastan command if installed.
```

Inspect Composer scripts; do not assume a `test` script exists. Formatting can modify files, so inspect the diff. Broaden tests for shared behavior or required project rules. Do not install tooling or change CI incidentally.

Check changed endpoints for input handling, authorization, response contract, query bounds, invariants, and tests. Report actual checks/results and distinguish unavailable checks from passes. Describe deployment/migration implications when changes have them.
