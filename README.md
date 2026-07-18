# Open Design SDK for Workshop

This SDK runs [Open Design](https://github.com/nexu-io/open-design) — the
open-source Claude Design alternative — as a headless daemon inside a workshop.
The daemon serves the web UI and the `/api` surface, exposes an MCP server, and
turns the code-agent CLIs already on your `PATH` (OpenCode, Claude Code, Codex,
Gemini, and 20+ more) into a design engine for prototypes, dashboards, decks,
images, and video. Projects, artifacts, the SQLite store, and configuration are
persisted on the host so your work survives workshop updates.

---

## Reference workshop

A minimal workshop:

```yaml
# workshop.yaml
name: design
base: ubuntu@24.04
sdks:
  - name: opencode
  - name: open-design
    channel: latest/stable
  - name: system
    slots:
      open-design-server:
        interface: tunnel
        endpoint: 127.0.0.1:7456
```

The reference pairs Open Design with a code-agent CLI (`opencode`) so the daemon
has a runtime to drive, and wires the `open-design-server` tunnel to the host so
the web UI at `http://127.0.0.1:7456` is reachable from your browser.

---

## Using the SDK

### Prerequisites, project layout

1. A code-agent CLI SDK (for example `opencode`, `copilot`, or `codex`) is
   recommended so Open Design has a local runtime to drive; otherwise configure
   a BYOK provider in the app's Settings.
2. No specific project layout is needed. Open Design manages its own projects
   and artifacts under the persisted data directory.
3. On launch, the SDK builds nothing at runtime: it installs the `od` CLI on
   `PATH`, starts the daemon as a user service on port `7456`, and creates the
   persisted data directory.

### Open the design studio

The daemon auto-detects any agent CLI visible on `PATH` at startup. Open the web
UI from your host through the tunnel:

```bash
# On the host, with the reference workshop running:
xdg-open http://127.0.0.1:7456   # or open the URL in a browser
```

Pick a runtime, a design template, and a design system, type a brief, and the
agent streams artifacts into the sandboxed preview.

### Drive it from the shell

Every capability is also reachable through the `od` CLI:

```bash
workshop shell
od --help
od mcp install opencode      # wire the MCP server into a code agent
od media generate --help
```

### Verify from the command line

```bash
workshop shell
systemctl --user is-active open-design
curl -fsS http://127.0.0.1:7456/api/health
```

`workshop info` shows the SDK health line once the daemon answers `/api/health`.

---

## Plugs (resources this SDK consumes)

### `open-design-data`

- Interface: `mount`
- Workshop target: `/home/workshop/.local/share/open-design`
- Purpose: Persists the daemon data root (`OD_DATA_DIR`) — managed projects,
  generated artifacts, the SQLite store, app config, memory, and MCP
  credentials — so your work and settings survive workshop updates.

## Slots (resources this SDK provides)

### `open-design-server`

- Interface: `tunnel`
- Endpoint: `7456`
- Purpose: Exposes the Open Design daemon (web UI, `/api`, and MCP server) so it
  can be reached from the host.

---

## Documentation and guidance

- [Open Design repository and documentation](https://github.com/nexu-io/open-design)
- [Open Design website](https://open-design.ai/)
- [Workshop documentation](https://ubuntu.com/workshop/docs/)

---

## Community and support

- Open Design community:
  [Discord](https://discord.gg/mHAjSMV6gz)
- Workshop forum:
  [Discourse](https://discourse.ubuntu.com/)
- Please review our
  [Code of Conduct](https://ubuntu.com/community/ethos/code-of-conduct) before
  participating.

---

## Contributions

All contributions, including code, documentation updates, and issue reports,
are welcome!

- See `CONTRIBUTING.md` for guidelines.
- Open issues or pull requests on the official repository.

---

## License and copyright

Copyright 2026 Canonical Ltd.

This SDK is licensed under the
[Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0).

Open Design is developed by nexu-io and licensed under the
[Apache License 2.0](https://github.com/nexu-io/open-design/blob/main/LICENSE).
