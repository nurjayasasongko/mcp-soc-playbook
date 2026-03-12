# MCP Host Reference

Perbandingan config MCP per host. "Host" di sini merujuk ke aplikasi tempat model berjalan.

> Konvensi di repo ini: **host** = aplikasinya, **client** = instance koneksi per server yang berjalan di dalam host.

## Quick Comparison

| Parameter | Claude Desktop | Claude Code | VS Code | Cursor | Windsurf | ChatGPT | Open WebUI |
|-----------|---------------|-------------|---------|--------|----------|---------|------------|
| Config file | `claude_desktop_config.json` | CLI / `.mcp.json` | `.vscode/mcp.json` | `.cursor/mcp.json` | `mcp_config.json` | UI only | UI / MCPO config |
| stdio support | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ | Via MCPO proxy |
| HTTP remote | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ Only | ✅ Native |
| Local server | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ | Via MCPO |
| Scope isolation | ❌ | ✅ `-s local/user` | ✅ Workspace/Global | ❌ | ❌ | ❌ | ❌ |
| GUI config | ✅ | ❌ | ⚠️ Partial | ✅ | ✅ | ✅ | ✅ |
| Self-hosted LLM | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |

## Per Host Detail

- [Claude Desktop](claude-desktop.md) — paling mudah untuk mulai
- [Claude Code](claude-code.md) — scope isolation terbaik
- [VS Code](vscode.md) — familiar, input variables untuk secrets
- [Cursor](cursor.md) — GUI paling user-friendly
- [Windsurf](windsurf.md) — mirip Cursor, dari Codeium
- [ChatGPT](chatgpt.md) — remote HTTP only, butuh URL publik
- [Open WebUI](openwebui.md) — self-hosted, pakai MCPO proxy untuk stdio server

## Security TL;DR per Host

| Host | Primary Risk | Mitigation |
|------|-------------|------------|
| Claude Desktop | Config readable by user-level process | File permission hardening |
| Claude Code | Scope misconfiguration | Always use `-s local` for project-specific servers |
| VS Code | Secrets committed via workspace config | Use `${env:}` atau `${input:}`, add to `.gitignore` |
| Cursor | Tool shadowing | Review tool descriptions, run `mcp-scan` |
| Windsurf | Global config, no scope isolation | Limit servers to active use only |
| ChatGPT | No local server support, data via OpenAI infra | Jangan connect tools internal sensitif |
| Open WebUI | MCPO unauthenticated, arbitrary code via Native Tools | Set `--api-key` di MCPO, restrict Workspace access |

> Full analysis → [../security-notes/attack-surface-per-client.md](../security-notes/attack-surface-per-client.md)
