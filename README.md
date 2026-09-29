# pod-mcp-layer

The `mcp-layer` candy of the OpenCharly candy library, as a standalone repo
(kind-prefixed naming). It ships `harness-mcp-fixture` — a minimal
Streamable-HTTP MCP server stub that exercises the `mcp:` check verb.

## What it provides

A stdlib-only Python server implementing the MCP Streamable HTTP protocol surface
that the `mcp:` check verb probes (dispatched out-of-process by
`candy/plugin-mcp`): `initialize`, `ping`, `tools/list`, `tools/call`. It
advertises three jupyter-named tools (`list_notebooks`, `insert_cell`,
`execute_cell`) so the `mcp-protocol-probe` recipe's matchers all satisfy.
Stdlib-only (no `fastmcp` install) keeps the image small and the build fast.

| Property | Value |
|---|---|
| Service | `harness-mcp-fixture` (`python3 -u /opt/harness-mcp/mcp_server.py`, priority 50) |
| Port | `8888` |
| Requires | `layer-supervisord`, `plugin-mcp` (the out-of-process `mcp:` check verb) |
| Packages | `python3`, `iproute`, `procps-ng` (Fedora) |
| mcp_provide | `jupyter` at `http://{{.ContainerName}}:8888/mcp` (http transport) |
| Protocol | MCP `2025-11-25` |

## How to use it

Compose the candy into a harness box; the fixture service starts automatically.
The candy's own `check:` steps assert the server script is baked at
`/opt/harness-mcp/mcp_server.py`, the `python3` interpreter and package, the
running fixture answering an `mcp: ping`, a `tools/list` advertising the three
tools, and the reachable port.

```bash
charly box validate
charly check live <pod> --filter mcp
```

## Layout

- `charly.yml` — the `mcp-layer:` candy entity (description, `require`,
  `distro`, `port`, `mcp_provide`, `service`, `plan`), including the inline
  `mcp_server.py`.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-check:check` — the `mcp:` check verb and the
  Streamable-HTTP MCP client it dispatches.
- `/charly-build:charly-mcp-cmd` — the `mcp:` check verb surface and
  `charly mcp serve`.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
