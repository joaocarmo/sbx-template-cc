# claude-extended

Docker Sandboxes v3 kit for Claude Code. Currently a vanilla set over
[`docker/sbx-kit-claude`](https://docs.docker.com/ai/sandboxes/customize/use-kits/#published-v3-workloads).

Requires `sbx` v0.45+.

## Run

```console
sbx run docker.io/<namespace>/claude-extended:<version> --name my-project
sbx run ./claude-extended --name dev   # from source
```

## Verify signature

```console
sbx kit verify docker.io/<namespace>/claude-extended:<version> \
  --certificate-identity https://github.com/<org>/<repo>/.github/workflows/publish.yml@refs/tags/v<version> \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com
```

## Release

```console
git tag v1.0.0 && git push --tags
```

CI builds linux/arm64 only (Apple silicon hosts), pushes to Docker Hub and signs. Needs repo secrets
`DOCKERHUB_NAMESPACE`, `DOCKERHUB_USERNAME`, `DOCKERHUB_TOKEN`.

## Upstream updates

[Renovate](https://docs.renovatebot.com/) (`renovate.json`) opens PRs bumping the upstream
`sbx-kit-claude` tag + digest, the `sbx` CLI used for signing, and GitHub Actions.
Merge, then tag a release.

Manual bump: update `ref` and `digest` in `claude-extended/claude-extended.yaml`
(`docker buildx imagetools inspect docker.io/docker/sbx-kit-claude:<tag>` → top-level `Digest`).
