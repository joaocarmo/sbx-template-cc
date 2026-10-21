# claude-extended

A [Docker Sandboxes](https://docs.docker.com/ai/sandboxes/) kit for Claude Code.
It currently adds nothing to Docker's
[`sbx-kit-claude`](https://docs.docker.com/ai/sandboxes/customize/use-kits/#published-v3-workloads).
Built for Apple silicon (linux/arm64).

## Usage

```console
sbx run docker.io/joaopestana/claude-extended:latest --name my-project
```

Tags: `latest` and `<claude version>` move; `sha-<commit>` doesn't.

Verify the signature:

```console
sbx kit verify docker.io/joaopestana/claude-extended:latest \
  --certificate-identity https://github.com/joaocarmo/sbx-template-cc/.github/workflows/publish.yml@refs/heads/main \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com
```

## Development

Run from source (needs a `docker-container` buildx builder):

```console
docker buildx create --name kit --driver docker-container
BUILDX_BUILDER=kit sbx run ./claude-extended --name dev
```

Every push to `main` builds, pushes and signs the image. Pull requests only build.

[Renovate](https://github.com/apps/renovate) keeps dependencies up to date and automerges
non-major updates. When it bumps `SBX_VERSION`, update `SBX_DEB_SHA256` in
`.github/workflows/publish.yml` to match the new release.

CI needs the repo variable `DOCKERHUB_NAMESPACE` and the secret `DOCKERHUB_TOKEN`
(Docker Hub access token, Read & Write).

## License

[MIT](LICENSE)
