# kitty

A mixin that installs kitty terminfo, shell integration, and the `kitten`
binary into a sandbox. Without this, `sbx exec` sessions don't know about the
kitty terminal and you get a degraded experience.

## Usage

```console
$ sbx run claude --kit "git+https://github.com/dvdksn/kits-cookbook.git#dir=kitty" ~/my-project
```

Open a shell:

```console
$ sbx exec -it <sandbox-name> bash
```

The `.bashrc` patch sets up terminfo and shell integration automatically.
Optionally alias it:

```bash
sshell() { sbx exec -it "${1:?sandbox name required}" bash; }
```

## What it installs

| Component                 | Location                                  |
| ------------------------- | ----------------------------------------- |
| `kitten` binary           | `/usr/local/bin/kitten`                   |
| Shell integration scripts | `/usr/local/lib/kitty/shell-integration/` |
| Terminfo (`xterm-kitty`)  | `/usr/local/share/terminfo/`              |
| `.bashrc` patch           | `/home/agent/.bashrc`                     |

## What works

- Shell integration (prompt marks, cursor shape, cwd reporting, title)
- `kitten transfer` — copy files out of the sandbox (useful for files outside the workspace mount)
- `kitten edit-in-kitty` — open a sandbox file in your host editor
- `kitten clipboard` — copy to host clipboard
- `kitten @ set-tab-title` and other remote control commands (requires `allow_remote_control` in `kitty.conf`)

## Configuration

Defaults to kitty v0.46.2. Override with `KITTY_VERSION` in the sandbox
environment. Shell integration features are controlled by
`KITTY_SHELL_INTEGRATION` (default: `enabled`, all features on).

## How it works

Downloads the standalone `kitten` binary (~24 MB, static Go binary) and
extracts shell integration scripts + terminfo from the kitty release tarball.
Everything else from the tarball is discarded.

The `.bashrc` patch is idempotent (checks for a marker comment before
appending). Re-applying with `sbx kit add` is safe.
