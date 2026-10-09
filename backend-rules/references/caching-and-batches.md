# Caching and job batching

## Cache when it helps

Cache repeated expensive reads, aggregates, or provider results when the feature can tolerate a defined freshness window. Fix unnecessary queries first. Identify the saved work and acceptable staleness; do not cache every CRUD endpoint or authoritative balance/inventory checks automatically.

- Keep cache ownership near the query/service that produces the value, rather than scattering keys across controllers.
- Include tenant, relevant permission/user scope, normalized filters, pagination, locale, and schema version in keys as applicable. Authorize before returning protected cached data.
- Use a deliberate TTL and define invalidation for creates, updates, deletes, and related changes. Invalidate after committed writes; consider concurrent readers repopulating old values when strict freshness matters.
- Store stable data shapes and avoid caching secrets or context-dependent model state indiscriminately. A cache miss must still yield correct behavior. Distinguish missing entries from legitimate empty results.
- Use Laravel's cache API, such as `Cache::remember`, for normal read-through caching. Confirm the installed store supports chosen features; tags and locks are not universally interchangeable. Use bounded shared locks for costly concurrent fills when justified. Avoid application-wide cache flushing to update one feature.

[Cache documentation](https://laravel.com/framework/docs/13.x/cache). Check the installed Laravel version and store before choosing advanced APIs.

## Batch related background work

Use batches for imports, bulk updates, or report partitions needing shared progress and completion. Independent jobs remain appropriate for unrelated work; chains suit ordered dependencies. A batch is not an all-or-nothing transaction across its jobs.

- Use `Bus::batch` and `Batchable` jobs with the configured batch repository and workers. Batched jobs share a connection/queue. Worker concurrency determines actual parallel execution.
- Split large datasets into bounded chunks using stable identifiers; avoid loading every record or embedding huge payloads. Preserve the actor/tenant context and validate access for dispatch, status, cancellation, and retries.
- Make each chunk safe to retry. Record business progress durably where recovery requires it. Do not rely on `ShouldBeUnique` for deduplication inside a batch.
- Make jobs check cancellation before continuing. Define fail-fast versus partial completion; use `allowFailures()` only when partial results have a clear contract. Cancellation does not undo completed effects.
- Keep completion/failure callbacks small, serializable, and idempotent; do not capture `$this`. Never report total success merely because all jobs stopped running.
- Persist an operation-to-batch association when clients need status. Return an authorized status reference for accepted work, handle missing/pruned metadata, and define retention/pruning. Plan after-commit dispatch when jobs depend on newly written state.

[Batching documentation](https://laravel.com/framework/docs/13.x/queues#job-batching). Verify setup and callback semantics against the target version; do not introduce batching for a single ordinary job.

## Verify behavior

Test cache hits/misses, expiry, mutation invalidation, and separation between tenants or permission scopes. Exercise concurrency using a suitable shared store when locking is part of correctness.

For batches, verify dispatch authorization and intended chunks with the existing bus test helpers. Test job behavior separately: duplicate execution, failures, cancellation, and resulting state. Faked dispatch alone does not prove worker execution, callbacks, or concurrency; use appropriate integration checks when these matter. Ensure completed mutations invalidate affected caches under the same freshness contract.
