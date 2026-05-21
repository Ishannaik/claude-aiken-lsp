# aiken-lsp

Claude Code plugin that wires up the [Aiken](https://aiken-lang.org) language
server for Cardano smart contract development. When you (or Claude Code's `LSP`
tool) operate on a `.ak` file, this plugin spawns `aiken lsp --stdio` and
forwards Language Server Protocol messages so you get diagnostics, hovers,
go-to-definition, document symbols, and the rest of the LSP feature set.

## Prerequisites

You need the Aiken toolchain installed and on your `PATH`. Aiken v1.1.22 or
newer is recommended.

- Install instructions: <https://aiken-lang.org/installation-instructions>
- Verify in a fresh shell before installing the plugin:
  ```
  aiken --version
  ```
  You should see something like `aiken v1.1.22+...`. If the command is not
  found, the plugin cannot launch the language server.

## Installation

Add the marketplace and enable the plugin in your `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "claude-aiken-lsp": {
      "source": { "source": "github", "repo": "Ishannaik/claude-aiken-lsp" }
    }
  },
  "enabledPlugins": {
    "aiken-lsp@claude-aiken-lsp": true
  }
}
```

Restart Claude Code so the new marketplace and plugin are picked up.

## What it does

This plugin declares an LSP server entry inside its `plugin.json` /
`marketplace.json`:

```json
"lspServers": {
  "aiken": {
    "command": "aiken",
    "args": ["lsp", "--stdio"],
    "transport": "stdio",
    "extensionToLanguage": {
      ".ak": "aiken"
    }
  }
}
```

When Claude Code's `LSP` tool is invoked against a `.ak` file, it:

1. Looks up the language for the extension (`.ak` -> `aiken`).
2. Spawns `aiken lsp --stdio` as a child process if it is not already running.
3. Forwards `initialize`, `textDocument/didOpen`,
   `textDocument/documentSymbol`, `textDocument/hover`,
   `textDocument/definition`, `textDocument/references`, diagnostics, etc.

The language identifier `"aiken"` matches what the official
[`txpipe/vscode-aiken`](https://github.com/txpipe/vscode-aiken) extension uses,
which is what the Aiken language server expects on incoming
`textDocument/didOpen` notifications.

## Verification

After restart, open any `.ak` file in your project and ask Claude Code to use
its LSP tool on it, e.g.:

> Use the LSP tool to list the document symbols for `validators/pool_vault.ak`.

You should get a structured `documentSymbol` response describing validators,
functions, and types in the file. If you instead get
`No LSP server available for this file`, see Troubleshooting below.

## Troubleshooting

**`No LSP server available`** — confirm `aiken --version` works in a fresh
shell. The plugin invokes the bare command `aiken`; it does not resolve PATH
overrides from `~/.bashrc` or similar files that are not sourced by Claude
Code's launch environment.

**Windows-without-PATH fallback** — if you have Aiken installed but it is not
on your global `PATH` (e.g. it lives at
`C:/Users/<you>/Projects/Dream/.tools/aiken/aiken.exe`), the simplest fix is to
add that directory to your user `PATH` environment variable in Windows
Settings. Alternatively you can fork this plugin and replace the `command`
field in `marketplace.json` with the absolute path to `aiken.exe`.

**Language server crashes immediately** — run `aiken lsp --stdio` manually in
a terminal; the process should sit waiting for LSP input on stdin. If it exits
with an error, that error is the underlying problem (most often: not run from
inside an Aiken project that has an `aiken.toml`).

**Stale diagnostics** — Claude Code restarts LSP servers on plugin reload.
Toggle the plugin off and on in your settings, or restart Claude Code.

## License

MIT - see [LICENSE](../../LICENSE) at repo root.
