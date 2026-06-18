# Codex Plugins

A marketplace of MCP plugins for **OpenAI Codex CLI** and **Codex IDE extensions**.

## Install

```bash
codex plugin marketplace add pavankalyanm/codex-plugins
```

## Plugins

| Plugin | Description |
|--------|-------------|
| [mails-bridge](mails-bridge/) | Multi-account email via IMAP/SMTP — Gmail, Outlook, Yahoo, iCloud, and more |

## Adding a new plugin

1. Create `<plugin-name>/` with `.codex-plugin/plugin.json`, `.mcp.json`, `server/`, and `skills/`
2. Add an entry to `.agents/plugins/marketplace.json`

## Requirements

- Python 3.11+
- [uv](https://docs.astral.sh/uv/) package manager
