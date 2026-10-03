# renovate-config

[Renovate](https://docs.renovatebot.com) presets shared by donvargax's
repositories. Public on purpose: Renovate reads them with each account's own
token, and a token minted for one account cannot read another account's
private repository. They hold rules, nothing secret.

| Preset | Extends as | What it does |
| --- | --- | --- |
| `default.json` | `github>donvargax/renovate-config#v1.0.0` | `config:recommended`; a weekly window, Mondays 00:00–05:59 UTC; every release waits seven days, except donvargax's own; lockfile maintenance in the same window; every update, majors included, lands once CI is green; no Dependency Dashboard; versions pinned in workflow `run:` steps move too, when written as `# renovate: datasource=… depName=…` above a `…_VERSION:` variable (`customManagers:githubActionsVersions`) |
| `vite-plus.json` | `github>donvargax/renovate-config:vite-plus#v1.0.0` | Node projects whose runtime is in `.node-version`: `node` and `@types/node` move as one update, `node`, never past the major this release allows (`<=24`) |

## Using them

```json5
{
  extends: [
    "github>donvargax/renovate-config#v1.0.0",
    "github>donvargax/renovate-config:vite-plus#v1.0.0",
  ],
}
```

Pin a tag. Renovate's `renovate-config` manager moves a pinned tag like any
other dependency, so a new release reaches every repository as an update of
its own, landed once its CI is green. A repository that lists
`enabledManagers` adds `renovate-config`, `nodenv` (`.node-version`) and
`custom.regex` (the `run:` step versions) to it.

A repository's own `packageRules` come after the presets', and the later rule
wins: a rule that groups every npm package (`matchManagers: ["npm"]`,
`groupName`) takes `@types/node` out of the `node` group, unless it leaves it
out (`matchPackageNames: ["*", "!@types/node"]`).

## Moving to a new Node major

Raise `allowedVersions` in `vite-plus.json` to the next LTS major and release
it as a new major (`v2.0.0`). Renovate moves each repository's pin to it, and
on its next run moves `.node-version` and `@types/node` there together, each
landing on its repository's green CI. The pattern is int128's
(github.com/int128/typescript-action-renovate-config).

## Releasing

Every change is validated in CI (`renovate-config-validator --strict`). A
release is a tag, `vMAJOR.MINOR.PATCH`, made with `gh release create`: major
when a repository's updates may change in kind (a new Node major, a new
grouping), minor for a new rule, patch for a fix.
