# claude-extended

A [Docker Sandboxes](https://docs.docker.com/ai/sandboxes/) kit for Claude Code.
It extends Docker's
[`sbx-kit-claude`](https://docs.docker.com/ai/sandboxes/customize/use-kits/#published-v3-workloads) with:

- [`git-ssh-sign`](https://github.com/docker/sbx-kits-contrib/tree/v3/git-ssh-sign): signs commits
  and tags with your host's SSH key (run `ssh-add` on the host first).

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
non-major updates.

CI needs the repo variable `DOCKERHUB_NAMESPACE` and the secret `DOCKERHUB_TOKEN`
(Docker Hub access token, Read & Write).

## License

[MIT](LICENSE)
