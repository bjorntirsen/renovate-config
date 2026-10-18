# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) preset for my repositories.

## Usage

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>bjorntirsen/renovate-config"]
}
```

Add repo-specific `packageRules` (version caps, holds, extra groups) below the `extends`. Always give them a `description` explaining why, and when they can be removed.

## Behaviour

| | |
|---|---|
| Schedule | Weekly, early Monday (Europe/Stockholm). Security fixes any time. |
| Release age | Waits 3 days after a release before proposing it (1 day for security fixes). |
| Non-major | Stable minor/patch/pin/digest updates grouped into one PR, automerged on green CI. |
| 0.x packages | Separate PRs, not automerged (minor bumps are breaking). |
| Majors | One PR per package, manual review. |
| Node / `@types/node` majors | Held on the Dependency Dashboard until approved. |
| vitest + `@vitest/*` majors | Grouped. |
| GitHub Actions | Pinned to commit digests. |
| Commit messages | Conventional Commits (`chore(deps): …`) on Renovate's own PRs only. |

## Repo requirements

- Renovate GitHub App installed on the repo.
- Public repos: "Allow auto-merge" enabled and a ruleset requiring CI checks, so Renovate uses GitHub's native automerge. Private repos on the free plan can't have either; Renovate then merges itself once status checks pass.
- CI runs on `pull_request` so automerge has checks to wait for.
- CI validates `renovate.json`:

  ```yaml
  - run: npx --yes --package renovate -- renovate-config-validator --strict
  ```
