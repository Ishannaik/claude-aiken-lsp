# claude-aiken-lsp

A Claude Code plugin marketplace that ships one plugin: **`aiken-lsp`**, which
wires up the [Aiken](https://aiken-lang.org) language server (`aiken lsp
--stdio`) so Claude Code's built-in `LSP` tool can answer document-symbol,
hover, definition, and diagnostic queries against `.ak` Cardano smart contract
files.

See [`plugins/aiken-lsp/README.md`](plugins/aiken-lsp/README.md) for what the
plugin does, prerequisites, installation, and troubleshooting.

## Quick install

In `~/.claude/settings.json`:

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

Restart Claude Code.

## Repo layout

```
claude-aiken-lsp/
├── .claude-plugin/
│   └── marketplace.json       # marketplace manifest (lists aiken-lsp)
├── plugins/
│   └── aiken-lsp/
│       ├── plugin.json        # plugin manifest (LSP server declaration)
│       └── README.md          # plugin-specific docs
├── LICENSE                    # MIT, Ishan Naik 2026
└── README.md                  # you are here
```

## License

MIT - see [LICENSE](LICENSE).
