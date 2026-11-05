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
- **Exact versions, from a registry with release dates.** The week can only be measured against a
  release date: Docker Hub dates a tag by its last push, so a floating tag (`3.14-slim`, `latest`)
  starts the week again on every rebuild, and GHCR, Quay and ECR give no dates at all, so their
  updates wait forever. Use `name:x.y.z@sha256:…`, from Docker Hub where the image is there too.
  See [Minimum Release Age, digest updates](https://docs.renovatebot.com/key-concepts/minimum-release-age/#digest-updates).
- **Lock file maintenance** every Monday before 6 am: one PR that refreshes the whole lock file,
  indirect dependencies included. Renovate sees no release dates here, so its cooling-off check
  would stay pending forever; the rule turns it off for this PR. The package manager enforces the
  week instead (for uv: `exclude-newer = "1 week"` under `[tool.uv]` in `pyproject.toml`).
- **Own projects** (`pdreker/**`, `docker.io/pdreker/**`, `ghcr.io/pdreker/**`: the shared
  workflows and the own images) skip the cooling-off: their dependencies already waited their
  week in their own repositories.
- **Labels by update type** (`labels.json`, also usable on its own as
  `github>pdreker/renovate-config:labels`): `major`, `minor`, `patch`, `pin`, `digest`, `lockfile`,
  and `security` for vulnerability fixes. The homelab's github_exporter counts open PRs by them.
