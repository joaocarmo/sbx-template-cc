# claude-extended

Docker Sandboxes v3 kit for Claude Code. Currently a vanilla set over
[`docker/sbx-kit-claude`](https://docs.docker.com/ai/sandboxes/customize/use-kits/#published-v3-workloads).
Built for linux/arm64 only (Apple silicon hosts). Requires `sbx` v0.45+.

## Run

```console
sbx run docker.io/<namespace>/claude-extended:latest --name my-project
sbx run ./claude-extended --name dev   # from source
```

Tags: `latest`, `<upstream Claude version>` (both move), `sha-<commit>` (immutable).

## Verify signature

```console
sbx kit verify docker.io/<namespace>/claude-extended:latest \
  --certificate-identity https://github.com/<org>/<repo>/.github/workflows/publish.yml@refs/heads/main \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com
```

## Release

Automatic: every push to `main` builds, pushes and signs. PRs build only.

[Renovate](https://docs.renovatebot.com/) (`renovate.json`) opens PRs for the upstream
`sbx-kit-claude` tag + digest, the `docker/sandbox-kit` frontend digest, the `sbx` CLI and
GitHub Actions (SHA-pinned), and automerges non-major ones when CI is green.

An `sbx` bump fails CI until you update `SBX_DEB_SHA256` in the workflow
(`sha256sum DockerSandboxes-linux-amd64-ubuntu2404.deb` from the new release).

## Setup

- Repo variable `DOCKERHUB_NAMESPACE`: Docker Hub username.
- Repo secret `DOCKERHUB_TOKEN`: Docker Hub personal access token, Read & Write.
- Install the [Renovate GitHub App](https://github.com/apps/renovate) on the repo.
