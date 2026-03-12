# Claude Code — MCP Config Reference

Claude Code adalah CLI tool dari Anthropic. Berbeda dari Claude Desktop, semua config MCP dilakukan lewat command line — tidak ada GUI. Keunggulan utamanya dari sisi security: **scope isolation**.

## Add Server

```bash
# Sintaks dasar
claude mcp add <nama-server> [flags] -- <command> [args]

# Local scope — hanya aktif di project ini
claude mcp add virustotal -s local -- uvx mcp-virustotal

# User scope — aktif di semua project
claude mcp add virustotal -s user -- uvx mcp-virustotal

# Dengan environment variable
claude mcp add virustotal -s local \
  -e VT_API_KEY=your_key_here \
  -- uvx mcp-virustotal
```

## Scope

Ini yang paling penting di Claude Code dan tidak ada di host lain.

| Scope | Flag | Config Location | Kapan Pakai |
|-------|------|----------------|-------------|
| Local | `-s local` | `.mcp.json` di project dir | Server spesifik satu project/lab |
| User | `-s user` | `~/.claude/settings.json` | Server yang dipakai di semua project |

**Selalu prefer `-s local`** untuk server dengan akses ke sistem sensitif. Server SIEM yang di-set sebagai user scope tersedia di semua project — termasuk yang tidak perlu akses ke sana.

## Config File Location

| Scope | Path |
|-------|------|
| Local | `.mcp.json` di root project directory |
| User | `~/.claude/settings.json` |

## Supported Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `-s` / `--scope` | string | ✅ | `local` atau `user` |
| `-e` / `--env` | string | ❌ | Environment variable: `-e KEY=value` |
| `--` | — | ✅ | Separator antara flag dan command |

## Manage Servers

```bash
# List semua server yang terkonfigurasi
claude mcp list

# Lihat detail satu server
claude mcp get virustotal

# Remove server
claude mcp remove virustotal

# Remove dari scope tertentu
claude mcp remove virustotal -s local
```

## Debug Mode

```bash
# Jalankan dengan MCP debug output
claude --mcp-debug
```

Berguna untuk investigasi ad-hoc — lihat semua JSON-RPC communication secara real-time.

## Real Example: IOC Enrichment Setup

```bash
# Setup untuk project threat hunting
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

File `.mcp.json` di project directory bisa ikut ter-commit kalau tidak hati-hati.

```bash
echo ".mcp.json" >> .gitignore
```

**Version pinning**

Selalu pin ke versi spesifik untuk community servers:

```bash
# Jangan
claude mcp add virustotal -s local -- uvx mcp-virustotal

# Pin versi
claude mcp add virustotal -s local -- uvx mcp-virustotal==0.2.1
```

**Scope review berkala**

```bash
# Cek semua server yang terdaftar di user scope
claude mcp list -s user
```

Server di user scope sering lupa di-remove setelah project selesai. Review berkala mencegah akses yang tidak perlu tetap aktif.

> Full analysis → [../security-notes/attack-surface-per-client.md](../security-notes/attack-surface-per-client.md)
