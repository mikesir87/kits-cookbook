# kits-cookbook

A small collection of example [kits](https://docs.docker.com/ai/sandboxes/customize/kits/)
for [Docker Sandboxes (`sbx`)](https://docs.docker.com/ai/sandboxes/), meant
as copy-and-adapt starting points rather than polished distributables.

## Kits

| Kit | Kind | What it does |
| --- | ---- | ------------ |
| [`ruff-lint`](./ruff-lint) | mixin | Installs Ruff and drops a shared `ruff.toml` into the workspace |
| [`kitty`](./kitty) | mixin | kitty terminfo + shell integration + `kitten` binary so `sbx exec` shells aren't degraded |
| [`mcp-memory`](./mcp-memory) | mixin | Wires the MCP memory server into a Claude Code sandbox |
| [`docker-review`](./docker-review) | mixin | Ships a Claude Code skill that reviews Dockerfiles |
| [`claude-safe`](./claude-safe) | agent | Fork of the built-in `claude` agent that runs without `--dangerously-skip-permissions` |
| [`code-server`](./code-server) | mixin | Runs web VS Code on port 8080 so you can open the sandbox in a browser |

## Loading a kit

Each directory is a self-contained kit. Load one with the Git URL form,
passing `#dir=<kit-name>` to point at a subdirectory:

```console
$ sbx run claude --kit "git+https://github.com/dvdksn/kits-cookbook.git#dir=ruff-lint" ~/my-project
```

Pin to a revision with `&ref=<branch|tag|commit>`:

```console
$ sbx run claude --kit "git+https://github.com/dvdksn/kits-cookbook.git#ref=v1.0&dir=ruff-lint" ~/my-project
```

Quote the URL — `&` starts a background job in some shells.

Stack several kits on the same sandbox by repeating `--kit`:

```console
$ sbx run claude \
    --kit "git+https://github.com/dvdksn/kits-cookbook.git#dir=ruff-lint" \
    --kit "git+https://github.com/dvdksn/kits-cookbook.git#dir=docker-review" \
    ~/my-project
```

## Validating a kit

If you're iterating on one of these, validate before loading:

```console
$ git clone https://github.com/dvdksn/kits-cookbook.git
$ sbx kit validate ./kits-cookbook/ruff-lint
$ sbx kit inspect  ./kits-cookbook/ruff-lint
```

## License

MIT. See [LICENSE](./LICENSE).
