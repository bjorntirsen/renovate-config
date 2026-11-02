# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) preset for my repositories.

## Usage

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>bjorntirsen/renovate-config"]
}
```

Repos linted with typescript-eslint (e.g. via `eslint-config-next`) also extend `github>bjorntirsen/renovate-config:typescript-eslint`, which caps `typescript` at the range typescript-eslint supports. Raise the cap there once and every such repo follows.

Prefer adding rules here over repo-specific `packageRules`: a rule that is harmless in repos without the package (groups, holds for a shared tool) belongs in the preset. Only truly repo-specific exceptions go below the `extends`, always with a `description` explaining why, and when they can be removed.

## Behaviour

| | |
|---|---|
| Schedule | Weekly, any time on Monday (Europe/Stockholm). Security fixes any time. |
| Release age | Waits 3 days after a release before proposing it (1 day for security fixes). |
| Non-major | Stable minor/patch/pin/digest updates grouped into one PR, automerged on green CI. |
| 0.x packages | Patches join the weekly group and automerge. Minors get separate PRs, not automerged (minor bumps are breaking), except CI-exercised tooling (`oxfmt`, `tsdown`, `prettier-plugin-tailwindcss`), which joins the weekly group. |
| Majors | One PR per package, manual review. |
| Node / `@types/node` majors | Held on the Dependency Dashboard until approved (includes the `setup-node` `node-version` input). |
| vitest + `@vitest/*` majors | Grouped. |
| `drizzle-orm` + `drizzle-kit` minors/majors | Grouped, manual review. |
| GitHub Actions | Pinned to commit digests. Majors automerge on green CI, except deploy-only actions (`docker/login-action`) that PR CI never runs. |
| Commit messages | Conventional Commits (`chore(deps): …`) on Renovate's own PRs only. |

## Maintenance

- This repo has its own `renovate.json` extending this preset, so Renovate keeps its CI actions and `.nvmrc` current like any other repo.
- CI re-validates the preset weekly against the latest Renovate release; a red scheduled run means an option was deprecated or renamed. Fix it here and every repo picks it up on its next Renovate run.
- Changes here apply to all consuming repos at once. Repo-specific exceptions belong in that repo's `renovate.json`.
- CI validates every preset file (`default.json`, `typescript-eslint.json`); add new ones to the validate step.

## Repo requirements

- Renovate GitHub App installed on the repo.
- Public repos: "Allow auto-merge" enabled and a ruleset requiring CI checks, so Renovate uses GitHub's native automerge. Private repos on the free plan can't have either; Renovate then merges itself once status checks pass.
- CI runs on `pull_request` so automerge has checks to wait for.
- CI validates `renovate.json`:

  ```yaml
  - run: npx --yes --package renovate -- renovate-config-validator --strict
  ```
