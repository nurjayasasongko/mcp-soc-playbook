# Cursor — MCP Config Reference

Cursor adalah code editor dengan built-in AI yang mendukung MCP via GUI. Paling user-friendly untuk setup awal. Ada dua level config: global dan project-specific.

## Config File Location

| Level | Path |
|-------|------|
| Global | `~/.cursor/mcp.json` |
| Project | `<project-dir>/.cursor/mcp.json` |

## Base Structure

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

Format-nya identik dengan Claude Desktop — perbedaannya hanya di lokasi file.

## Supported Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `command` | string | ✅ | Executable yang dijalankan |
| `args` | array | ❌ | Arguments ke command |
| `env` | object | ❌ | Environment variables |
| `type` | string | ❌ | `stdio` (default) atau `http` |
| `url` | string | ❌ | Required jika `type: "http"` |

## Real Example: OSINT Recon

```json
{
  "mcpServers": {
    "shodan": {
      "command": "uvx",
      "args": ["mcp-shodan==1.0.0"],
      "env": {
        "SHODAN_API_KEY": "your_shodan_key"
      }
    },
    "virustotal": {
      "command": "uvx",
      "args": ["mcp-virustotal==0.2.1"],
      "env": {
        "VT_API_KEY": "your_vt_key"
      }
    }
  }
}
```

## Setup via GUI

Cursor punya settings UI untuk tambah MCP server tanpa edit JSON manual:

`Settings → Features → MCP → Add New MCP Server`

Isi form: nama server, command, args, env vars. Cursor generate JSON-nya otomatis.

Meski ada GUI, tetap perlu review file JSON langsung — GUI kadang tidak expose semua parameter yang tersedia.

## ⚠️ Security Considerations

**Tool shadowing**

InvariantLabs mendemokan tool shadowing attack di Cursor — tool dengan nama mirip tools legitimate dipakai untuk manipulasi perilaku model. Cursor tidak ada mekanisme verifikasi otomatis untuk ini.

Mitigasi: selalu review tool descriptions sebelum approve server baru. Tool description yang terlalu verbose, ada instruksi tersembunyi di tengah teks teknikal, atau ada parameter yang tidak relevan dengan fungsi tool — itu red flag.

**Jalankan mcp-scan sebelum aktifkan server baru**

```bash
pip install mcp-scan
mcp-scan
```

mcp-scan akan baca config Cursor dan flag tool descriptions yang mencurigakan.

**Project config jangan di-commit**

```bash
echo ".cursor/mcp.json" >> .gitignore
```

**Version pinning**

```json
// Jangan
"args": ["mcp-virustotal"]

// Pin ke versi spesifik
"args": ["mcp-virustotal==0.2.1"]
```

> Full analysis → [../security-notes/attack-surface-per-client.md](../security-notes/attack-surface-per-client.md)
