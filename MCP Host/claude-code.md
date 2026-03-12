# Claude Code — MCP Config Reference

> 📖 Official docs: [docs.anthropic.com — Claude Code MCP](https://docs.anthropic.com/en/docs/claude-code/mcp)

Claude Code adalah CLI tool dari Anthropic untuk agentic coding. Berbeda dari Claude Desktop, semua config MCP dilakukan via command line. Keunggulan utama dari sisi security: **scope isolation** — server bisa dibatasi hanya aktif di satu project tertentu.

## Config File Location

| Scope | Path |
|-------|------|
| Local (per project) | `.mcp.json` di root project directory |
| User (global) | `~/.claude/settings.json` |

## Base Structure

Config tidak ditulis manual — dibuat via `claude mcp add`. Tapi kalau perlu edit langsung, format `.mcp.json` adalah:

```json
{
  "mcpServers": {
    "<nama-server>": {
      "command": "<executable>",
      "args": ["<arg1>"],
      "env": {
        "API_KEY": "value"
      }
    }
  }
}
```

## Supported Parameters (CLI Flags)

| Flag | Shorthand | Required | Description |
|------|-----------|----------|-------------|
| `--scope` | `-s` | ✅ | `local` (project) atau `user` (global) |
| `--env` | `-e` | ❌ | Environment variable: `-e KEY=value` |
| `--transport` | — | ❌ | `stdio` (default) atau `http` |
| `--` | — | ✅ | Separator antara flag dan command |

## Transport Types

### stdio — Local Process

```bash
claude mcp add virustotal -s local -- uvx mcp-virustotal==0.2.1
```

### Streamable HTTP — Remote Server

```bash
claude mcp add --transport http qradar http://your-qradar-mcp:8080/mcp
```

## Scope Isolation

Ini yang paling penting di Claude Code dan tidak ada di host lain.

| Scope | Flag | Kapan Pakai |
|-------|------|-------------|
| Local | `-s local` | Server spesifik satu project atau lab |
| User | `-s user` | Server yang dipakai di semua project |

**Selalu prefer `-s local`** untuk server dengan akses ke sistem sensitif. Server SIEM yang di-set sebagai user scope tersedia di semua project — termasuk yang tidak butuh akses ke sana.

## Manage Servers

```bash
# Tambah server (local scope)
claude mcp add virustotal -s local \
  -e VT_API_KEY=${VT_API_KEY} \
  -- uvx mcp-virustotal==0.2.1

# List semua server
claude mcp list

# Lihat detail server
claude mcp get virustotal

# Hapus server
claude mcp remove virustotal -s local
```

## Real Example: IOC Enrichment Setup

```bash
cd ~/projects/threat-hunting

claude mcp add virustotal -s local \
  -e VT_API_KEY=${VT_API_KEY} \
  -- uvx mcp-virustotal==0.2.1

claude mcp add abuseipdb -s local \
  -e ABUSEIPDB_API_KEY=${ABUSEIPDB_API_KEY} \
  -- uvx mcp-abuseipdb

claude mcp list
```

## ⚠️ Security Considerations

**`.mcp.json` jangan di-commit ke Git**

File `.mcp.json` di project directory bisa ikut ter-commit.

```bash
echo ".mcp.json" >> .gitignore
```

**Version pinning**

```bash
# ❌ Tanpa pinning
claude mcp add virustotal -s local -- uvx mcp-virustotal

# ✅ Pin ke versi spesifik
claude mcp add virustotal -s local -- uvx mcp-virustotal==0.2.1
```

**Review user scope berkala**

```bash
# Cek server yang aktif secara global
claude mcp list -s user
```

Server di user scope sering lupa di-remove setelah project selesai. Review mencegah akses yang tidak perlu tetap aktif.

**Debug mode untuk investigasi**

```bash
claude --mcp-debug
```

Tampilkan semua JSON-RPC communication secara real-time — berguna untuk verify tool mana yang dipanggil dan dengan parameter apa.

> Full security analysis → [../security-notes/attack-surface-per-client.md](../security-notes/attack-surface-per-client.md)
