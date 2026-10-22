# claude-extended

A [Docker Sandboxes](https://docs.docker.com/ai/sandboxes/) kit for Claude Code.
It extends Docker's
[`sbx-kit-claude`](https://docs.docker.com/ai/sandboxes/customize/use-kits/#published-v3-workloads) with:

- [`git-ssh-sign`](https://github.com/docker/sbx-kits-contrib/tree/v3/git-ssh-sign): signs commits
  and tags with your host's SSH key (run `ssh-add` on the host first).
- [`claude-sbx-statusline`](https://github.com/docker/sbx-kits-contrib/tree/v3/claude-sbx-statusline):
  two-line terminal status line (replaces the base kit's Docker Desktop status feed).

Built for Apple silicon (linux/arm64).

## Usage

```console
sbx run docker.io/joaopestana/claude-extended:latest --name my-project
```

Tags: `latest` and `<claude version>` move; `sha-<commit>` doesn't.

Dependencies built on macOS (e.g. `node_modules`) don't run in the Linux sandbox. Use `--clone`
to work on a private clone instead of the host checkout, then fetch from the host while it runs:

```console
sbx run docker.io/joaopestana/claude-extended:latest --name my-project --clone
git fetch sandbox-my-project
```

Make sbx refuse the kit unless CI signed it (keeps Docker's default signer):

```console
sbx settings set kit.trustedSigners '[{"identityRegexp":"^.*@docker\\.com$","issuer":"https://accounts.google.com"},{"identity":"https://github.com/joaocarmo/sbx-template-cc/.github/workflows/publish.yml@refs/heads/main","issuer":"https://token.actions.githubusercontent.com"}]'
sbx settings set kit.requireSignature true
```

Built-in agents (`sbx run claude`) are exempt; unsigned kits, including Docker's by OCI reference and
local builds, are refused.

## Personal configuration

Sandboxes don't read your host's `~/.claude`. Skills and MCP servers are added from the host instead
(see `sbx skills --help` and the [MCP gateway docs](https://docs.docker.com/ai/sandboxes/mcp-gateway/)):

```console
sbx skills import   # copies from ~/.claude/skills, ~/.agents/skills, …
sbx skills add owner/repo
sbx mcp add linear --url https://mcp.linear.app/mcp   # OAuth tokens stay on the host
sbx run docker.io/joaopestana/claude-extended:latest --name my-project --static-mcp linear
```

For a global `CLAUDE.md`, put it in `~/.config/claude-sbx/` and mount that folder read-only (not
`~/.claude`, which holds transcripts and settings). The kit links it into `~/.claude/rules/`; host
edits apply from the next session. Local sandboxes only:

```console
sbx run docker.io/joaopestana/claude-extended:latest --name my-project . ~/.config/claude-sbx:ro
```

## Development

Run from source (needs a `docker-container` buildx builder):

```console
docker buildx create --name kit --driver docker-container
BUILDX_BUILDER=kit sbx run ./claude-extended --name dev
```

With signatures required, run `sbx settings set kit.requireSignature false` first (the env var doesn't
reach the daemon), and set it back to `true` afterwards.

Every push to `main` smoke-tests, builds, pushes and signs the image. Pull requests smoke-test and build.

[Renovate](https://github.com/apps/renovate) keeps dependencies up to date and automerges
non-major updates.

CI needs the repo variable `DOCKERHUB_NAMESPACE` and the secret `DOCKERHUB_TOKEN`
(Docker Hub access token, Read & Write), plus `DOCKERHUB_READ_TOKEN` (Read-only) for the smoke test.

## License

[MIT](LICENSE)
