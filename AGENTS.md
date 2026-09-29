# AGENTS.md — pod-mcp-layer

Standalone candy repo for the `mcp-layer` candy — `harness-mcp-fixture`, a
minimal stdlib-only Streamable-HTTP MCP server stub that exercises the `mcp:`
check verb. The entire candy lives in `charly.yml` at the repo root.

Canonical files:

- `charly.yml` — the `mcp-layer:` candy entity (description, `require`,
  `distro`, `port`, `mcp_provide`, `service`, `plan`), including the inline
  `mcp_server.py` written by a `write:` step.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-check:check` — the owning skill: the `check:` step verbs, the `mcp:`
  verb, and `charly check run <bed>` / `charly check live <pod> --filter mcp`.
  Load before editing, building, deploying, or troubleshooting this candy.
- `/charly-build:charly-mcp-cmd` — the `mcp:` check verb surface and
  `charly mcp serve`.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs, `mcp_provide`, service declarations).
- `/charly-internals:git-workflow` — before any git/PR action.

The candy carries no `skill:` entity of its own; the `mcp:` verb is owned by
`/charly-check:check`. The gap is routed to the named skill-authoring batch
[opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291);
when that lands, add the owning skill here.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The candy's own `check:` steps are the R10 witness: they assert the server
  script is present at `/opt/harness-mcp/mcp_server.py`, the `python3`
  interpreter and package, the running fixture answering an `mcp: ping`, a
  `tools/list` advertising `list_notebooks` / `insert_cell` / `execute_cell`, and
  the reachable port.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.

## Modify this repo

- Edit the `mcp-layer:` candy entity in `charly.yml`. The `mcp_server.py` body
  lives inline in the `write:` step's `content:`; the JSON-RPC handlers there are
  the contract the `mcp:` check verb depends on.
- The `port:` field (`8888`), the `mcp_provide` URL (`:8888/mcp`), and the
  service exec must stay in step.
- Keep the advertised tool names aligned with what the harness recipe matchers
  expect; a stub that drifts from the recipe hides a real integration break.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
