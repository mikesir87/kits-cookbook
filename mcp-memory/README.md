# mcp-memory

A mixin that wires the [Model Context Protocol memory server][memory] into a
Claude Code sandbox. The agent gets a persistent knowledge graph it can read
and write between turns.

[memory]: https://github.com/modelcontextprotocol/servers/tree/main/src/memory

## Usage

```console
$ sbx run claude --kit "git+https://github.com/dvdksn/kits-cookbook.git#dir=mcp-memory" ~/my-project
```

On first run, Claude Code prompts for approval to use the project-scoped
server (a one-time security check for `.mcp.json` entries).

## How it works

- `commands.install` runs `npm install -g @modelcontextprotocol/server-memory`
  once at sandbox creation, so the binary is baked into the image.
- `files/workspace/.mcp.json` drops a project-scoped MCP config into the
  workspace. Claude Code reads `.mcp.json` on start.
- `network.allowedDomains` opens `registry.npmjs.org` so the install step can
  fetch the package. The memory server stores its knowledge graph locally
  and doesn't need further network access at runtime.

## Adapt it to other MCP servers

Swap the package name in both `spec.yaml` and `.mcp.json`. For servers that
talk to an external API (GitHub, Linear, Slack, …), also:

1. Add the API host to `network.allowedDomains`.
2. Declare credentials with `credentials.sources` and reference them via
   `environment.proxyManaged` so the real token stays on the host.
3. Use `network.serviceDomains` + `serviceAuth` if you want the proxy to
   inject the auth header for you.

See the [spec reference][ref] for field details.

[ref]: https://docs.docker.com/ai/sandboxes/customize/kits/#spec-reference
