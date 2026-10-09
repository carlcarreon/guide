# BACKEND-RULES Skill

A reusable agent skill for Laravel implementation, refactoring, and review: controllers, requests, resources, models, policies, actions/services, migrations, queues, SOLID, clean code, security, and behavior tests.

This is an instruction package, not a Laravel runtime dependency or code generator. It adapts to installed PHP/Laravel versions and existing conventions. Add layers only when the feature warrants them.

Routes are declared explicitly for actions such as `index`, `create`, `store`, `show`, `edit`, `update`, and `destroy`, grouped with a URL prefix and route-name prefix. Resource route-registration shortcuts are prohibited. API Resources for JSON responses are still supported.

Use caching when repeated expensive work justifies it, with scoped keys, TTL, and invalidation. Use job batching for related bulk work needing shared progress, cancellation, and completion handling.

## Install by cloning

Publish **the contents of this `backend-rules` folder** as the root of your Git repository. Its root must contain `SKILL.md`, `agents/`, and `references/`; avoid an extra nested `backend-rules` directory.

From a Laravel project root, replace the example URL with your actual repository URL:

```bash
git clone https://github.com/YOUR-USERNAME/backend-rules.git backend-rules
```

This location supports the folder reference `[backend-rules](backend-rules/)` from the project root. Ask the agent to read and follow the folder's `SKILL.md`; a Markdown link alone is a file reference, not a registered skill invocation.

For immediate local use before publishing, run from the Laravel project root:

```bash
cp -R /Users/cjcarreon/dev/guides/backend-rules backend-rules
```

The destination must not already exist. Update a separately cloned skill with:

```bash
git -C backend-rules pull --ff-only
```

For team-managed installation in the application's Git history, use a submodule instead of a nested clone, or copy and track the files. A normal nested clone keeps its own Git history; the parent repository does not automatically track its contents. For a local independent clone, add `backend-rules/` to the application's ignore rules if appropriate.

## Use

Reference the folder as **`[backend-rules](backend-rules/)`**, then describe the task and ask the agent to follow its rules:

```text
Use [backend-rules](backend-rules/). Read and follow its SKILL.md
to build an orders API with validation, policies, resources, and tests.
```

```text
Use [backend-rules](backend-rules/). Read and follow its SKILL.md
to review this controller for SOLID, authorization, query performance,
and clean-code issues. Report findings first.
```

For optional Codex skill discovery, install under `.agents/skills/backend-rules` instead. Then use `$backend-rules` or automatic matching, and change folder references to `[backend-rules](.agents/skills/backend-rules/)`. See [official skill documentation](https://learn.chatgpt.com/docs/build-skills). Other agents' discovery paths and invocation syntax may differ.

## Files

- [SKILL.md](SKILL.md): workflow, component selection, essential rules.
- [Architecture](references/architecture.md): layers, naming, practical SOLID.
- [HTTP and security](references/http-and-security.md): controllers, requests, resources, authorization, APIs.
- [Data and testing](references/data-and-testing.md): persistence, transactions, jobs, verification.
- [Caching and batches](references/caching-and-batches.md): cache freshness/invalidation and bulk-job lifecycle.
- [agents/openai.yaml](agents/openai.yaml): Codex display metadata.

Architecture rules are this skill's conventions. Linked official Laravel documentation explains framework features; consult documentation matching the target project's installed version for version-specific APIs.
