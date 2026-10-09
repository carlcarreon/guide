# FRONTEND-RULES Skill

React frontend rules for formatting, reusable components/hooks, shadcn/ui-only controls, accessible forms, state, API integration, and behavior tests. This is an AI instruction package, not a React runtime dependency.

## Install

Publish **the contents of this `frontend-rules` folder** as a standalone Git repository root containing `SKILL.md`, `agents/`, and `references/`. Replace the example URL with the actual repository URL, then run from your application's root:

```bash
git clone https://github.com/YOUR-USERNAME/frontend-rules.git frontend-rules
```

For immediate local use before publishing, run from the application root:

```bash
cp -R /Users/cjcarreon/dev/guides/frontend-rules frontend-rules
```

The destination must not already exist. A nested clone maintains separate Git history; use a submodule or a tracked file copy when the application repository should manage the rules for the team. For a local independent clone, ignore `frontend-rules/` in the application repository if appropriate.

Update an independent clone with `git -C frontend-rules pull --ff-only`.

## Use the folder reference

```text
Use [frontend-rules](frontend-rules/). Read and follow its SKILL.md
to build a responsive Products page using shadcn/ui.
Include a create/edit form, validation, loading and error states,
and reusable components where appropriate.
```

For a Laravel/React feature, use both packages:

```text
Use [backend-rules](backend-rules/) and [frontend-rules](frontend-rules/).
Read and follow their SKILL.md files to build a Products CRUD feature.
Users may manage only their own products. Use shadcn/ui for frontend controls.
```

Folder links reference the files; the accompanying instruction tells the agent to read and apply them. Adjust the path if installed elsewhere.

For optional Codex discovery, clone into `.agents/skills/frontend-rules` instead. You can then invoke `$frontend-rules` or use automatic matching. See [official skill documentation](https://learn.chatgpt.com/docs/build-skills).

## Included rules

- [SKILL.md](SKILL.md): workflow and shadcn-only component boundary.
- [Formatting and reuse](references/formatting-and-reuse.md): defaults, names, architecture, TypeScript, composition, hooks.
- [shadcn and forms](references/shadcn-and-forms.md): component selection, themes, validation, accessibility.
- [State and testing](references/state-and-testing.md): React behavior, effects, API boundaries, verification.
- [agents/openai.yaml](agents/openai.yaml): display metadata.

Formatting defaults are this skill's conventions, not mandatory React rules. Existing project formatters win. shadcn-only means UI controls and visual primitives come from local official shadcn components or their compositions; semantic HTML and appropriate non-UI libraries remain allowed. Version-specific component APIs must match the installed project.
