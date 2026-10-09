# HTTP boundaries and security

These are preferred project rules. Preserve established behavior unless the task explicitly changes it.

## Routes and controllers

Use appropriate HTTP methods and resource-oriented routes; express distinct domain operations explicitly. Preserve naming/versioning conventions. Apply existing authentication and targeted rate limits when warranted. Do not introduce auth packages without a requirement.

**Required routing convention:** declare every action explicitly. Do not use `Route::resource()`, `Route::apiResource()`, `Route::resources()`, or `Route::apiResources()`. Use `prefix(...)` and `group(...)` to organize feature routes, with `name('feature.')` for route names and shared middleware where applicable. Apply this convention to routes being added or changed without rewriting unrelated routes. API Resources for JSON serialization remain part of the architecture.

Example for authenticated web routes in `routes/web.php`:

```php
use App\Http\Controllers\ProductController;
use Illuminate\Support\Facades\Route;

Route::prefix('products')
    ->name('products.')
    ->middleware('auth')
    ->group(function () {
        Route::get('/', [ProductController::class, 'index'])->name('index');
        Route::get('/create', [ProductController::class, 'create'])->name('create');
        Route::post('/', [ProductController::class, 'store'])->name('store');
        Route::get('/{product}/edit', [ProductController::class, 'edit'])->name('edit');
        Route::get('/{product}', [ProductController::class, 'show'])->name('show');
        Route::match(['put', 'patch'], '/{product}', [ProductController::class, 'update'])
            ->name('update');
        Route::delete('/{product}', [ProductController::class, 'destroy'])->name('destroy');
    });
```

Declare static paths such as `/create` before a matching dynamic path such as `/{product}`. Use parameter names matching controller model-binding arguments. Choose `put`, `patch`, or both according to the API's replacement/partial-update contract. Add only required actions: `create` and `edit` serve form pages and are usually unnecessary for a JSON-only API. API routes follow the same explicit grouped style, with the project's actual auth middleware and automatic API prefix accounted for. Preserve existing public URLs and route names unless changing them is requested. [Routing documentation](https://laravel.com/framework/docs/13.x/routing).

Use route model binding when useful and parent scoping for nested resources. Controllers coordinate rather than accumulate workflows. Extract a reused operation instead of duplicating it. Laravel supports resource and single-action controllers; choose according to the operation. [Controller documentation](https://laravel.com/framework/docs/13.x/controllers).

## Requests and writable fields

Use Store/Update Form Requests for substantial rules. Validate types, bounds, allowed values, and nested array keys. Use validated data and map writable fields explicitly. Distinguish omitted update fields from nulls. Normalize only transformations allowed by the input contract.

An existence check is not access control. Scope relationship validation to permitted records and recheck mutable rules when races matter. Never pass user-controlled input as a uniqueness rule's `ignore` identity; use a trusted model/key. Validation gives feedback; a database unique constraint protects integrity. [Validation documentation](https://laravel.com/framework/docs/13.x/validation).

Keep business invariants in reusable operations/domain behavior when other callers must enforce them too. CLI commands and jobs must not bypass invariants because they lack a Form Request.

## Authorization

Use policies for resource permissions and gates for suitable non-resource checks. A request's `authorize()` may delegate to a policy; `true` is valid only for public operations or authorization covered elsewhere. Choose an intentional authorization point for each protected operation.

Authentication identifies; authorization permits. Check ownership/tenant boundaries on lists, details, mutations, and related identifiers. Never accept tenant, owner, role, or privileged status merely because it validates. Pass trusted actor/context into reusable operations enforcing actor-specific rules. [Authorization documentation](https://laravel.com/framework/docs/13.x/authorization).

Preserve the project's deliberate 403 versus 404 policy for inaccessible records. Serialization is not the primary access-control mechanism.

## Resources and response contract

Prefer API Resources for explicit public model output. Allowlist fields and format dates, money, nulls, and identifiers consistently. Use conditional relationships such as `whenLoaded`, with loading planned before serialization. Preserve pagination metadata and avoid duplicating resource wrappers. [Resource documentation](https://laravel.com/framework/docs/13.x/eloquent-resources).

Common status choices are 200 for reads/updates, 201 for creation, 202 for genuinely accepted asynchronous processing, and 204 with no response body. Validation commonly uses 422; authentication 401; denial 403; absence 404. Follow documented business-conflict semantics instead of returning 200 with a failure flag.

Preserve established exception handling unless a documented contract needs adaptation. Do not expose raw errors/debug information or catch every controller exception and turn it into a successful response.

## Security and integrations

- Treat client identifiers as untrusted. Bind query values and allowlist sort columns, directions, filters, and selectable fields. Bound pagination and expensive inputs.
- Preserve CSRF protection on session routes. Use intended cookie/token authentication, guard, and restrictive CORS settings.
- Use framework password hashing. Keep secrets out of responses, fixtures, logs, and exception context.
- Validate upload size/content type; use generated storage names and appropriate access controls. Do not trust client paths/extensions.
- Restrict user-influenced server-side fetch destinations. Verify webhook signatures under the provider contract and handle replay/duplicate delivery.
- Set HTTP timeouts. Retry only safe or idempotency-protected operations; a lost response does not justify repeating a payment blindly.
- Log useful context without secrets or unnecessary personal data. Use existing correlation identifiers.
