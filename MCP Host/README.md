# MCP Host Reference

Perbandingan config MCP per host. "Host" di sini merujuk ke aplikasi tempat model berjalan — Claude Desktop, VS Code, Cursor, dan lainnya.

> Konvensi di repo ini: **host** = aplikasinya, **client** = instance koneksi per server yang berjalan di dalam host.

## Quick Comparison

| Parameter | Claude Desktop | Claude Code | VS Code | Cursor | Windsurf |
|-----------|---------------|-------------|---------|--------|----------|
| Config file | `claude_desktop_config.json` | CLI / `.mcp.json` | `.vscode/mcp.json` | `.cursor/mcp.json` | `mcp_config.json` |
| Config location | `%APPDATA%/Claude` (Win) `~/Library/Application Support/Claude` (Mac) | Project dir / `~/.claude` | Workspace / Global | `~/.cursor/` | `~/.codeium/windsurf/` |
| `command` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `args` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `env` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `type` (stdio/http) | ✅ | ✅ | ✅ | ✅ | ✅ |
| `url` (remote) | ✅ | ✅ | ✅ | ✅ | ✅ |
| Scope isolation | ❌ Global only | ✅ `-s local` / `-s user` | ✅ Workspace / Global | ❌ Global only | ❌ Global only |
| GUI config editor | ✅ | ❌ | ⚠️ Partial | ✅ | ✅ |

## Per Host Detail

- [Claude Desktop](claude-desktop.md)
- [Claude Code](claude-code.md)
- [VS Code](vscode.md)
- [Cursor](cursor.md)
- [Windsurf](windsurf.md)

## Security TL;DR per Host

| Host | Primary Risk | Mitigation |
|------|-------------|------------|
| Claude Desktop | Config readable by any user-level process | File permission hardening |
| Claude Code | Scope misconfiguration | Always use `-s local` for project-specific servers |
| VS Code | Secrets committed via workspace config | Use env vars, add `.vscode/mcp.json` to `.gitignore` |
| Cursor | Tool shadowing (InvariantLabs demo) | Review tool descriptions before approving |
| Windsurf | Global config, no scope isolation | Limit servers to what's actively needed |

> Full analysis → [../Security Notes/attack-surface-per-client.md](../Security%20Notes/attack-surface-per-client.md)
