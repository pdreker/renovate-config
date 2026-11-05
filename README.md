# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) rules for Patrick's repositories. A repository
uses them with a `renovate.json` of

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>pdreker/renovate-config"]
}
```

and adds only what is its own. This repository is public on purpose: Mend's hosted Renovate does
not load presets from a private repository into a public one, so a private preset failed for the
public repositories ("Cannot find preset's package"). It holds rules only, nothing secret.

What `default.json` sets:

- `config:recommended`, time zone Europe/Berlin, the Dependency Dashboard, label `dependencies`.
- **7 days' cooling-off** (`minimumReleaseAge`): a release becomes a PR only after a week.
- **Pinned versions:** container base images with a digest, GitHub Actions on a commit SHA (the
  tag stays as a comment). Pinning itself skips the cooling-off: it changes no version.
- **Lock file maintenance** every Monday before 6 am: one PR that refreshes the whole lock file,
  indirect dependencies included. Renovate sees no release dates here, so its cooling-off check
  would stay pending forever; the rule turns it off for this PR. The package manager enforces the
  week instead (for uv: `exclude-newer = "1 week"` under `[tool.uv]` in `pyproject.toml`).
- **Own reusable workflows** (`pdreker/github-workflows`, GitHub Actions manager) skip the
  cooling-off: the action updates inside them already waited their week there.
- **Labels by update type** (`labels.json`, also usable on its own as
  `github>pdreker/renovate-config:labels`): `major`, `minor`, `patch`, `pin`, `digest`, `lockfile`,
  and `security` for vulnerability fixes. The homelab's github_exporter counts open PRs by them.
