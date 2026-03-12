# Cursor — MCP Config Reference

> 📖 Official docs: [docs.cursor.com — Model Context Protocol (MCP)](https://docs.cursor.com/context/model-context-protocol)

Cursor adalah code editor dengan built-in AI (Composer Agent) yang support MCP via GUI maupun file JSON. Ada dua level config: global dan per project. Paling user-friendly untuk onboarding awal — bisa tambah server via settings UI tanpa edit JSON manual.

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

Format identik dengan Claude Desktop — perbedaannya hanya di lokasi file.

## Supported Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `command` | string | ✅ (stdio) | Executable yang dijalankan |
| `args` | array | ❌ | Arguments ke command |
| `env` | object | ❌ | Environment variables |
| `type` | string | ❌ | `stdio` (default) atau `http` |
| `url` | string | ✅ (http) | URL remote server |

## Transport Types

### stdio — Local Process

```json
{
  "mcpServers": {
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

### Streamable HTTP — Remote Server

```json
{
  "mcpServers": {
    "qradar": {
      "type": "http",
      "url": "http://your-qradar-mcp-server:8080/mcp"
    }
  }
}
```

## Setup via GUI

Cursor punya settings UI untuk tambah server tanpa edit JSON manual:

`Cursor Settings → Features → MCP → Add New MCP Server`

Isi form: nama server, tipe transport, command atau URL. Cursor generate JSON-nya otomatis.

Meski ada GUI, tetap perlu review file JSON langsung setelah setup — GUI kadang tidak expose semua parameter yang tersedia, terutama untuk flag keamanan seperti `--network none` di Docker.

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

## ⚠️ Security Considerations

**Tool shadowing**

InvariantLabs mendemokan tool shadowing attack di Cursor — tool dengan nama mirip tool legitimate dipakai untuk manipulasi perilaku model. Cursor tidak punya mekanisme verifikasi otomatis untuk ini.

Sebelum aktifkan server baru, cek tool description-nya: terlalu verbose, ada instruksi tersembunyi di tengah teks teknikal, atau parameter yang tidak relevan dengan fungsi tool — itu red flag.

**Jalankan `mcp-scan` sebelum aktifkan server baru**

```bash
pip install mcp-scan
mcp-scan
```

`mcp-scan` membaca config Cursor dan flag tool descriptions yang mencurigakan.

**Project config jangan di-commit**

```bash
echo ".cursor/mcp.json" >> .gitignore
```

**Version pinning**

```json
// ❌ Tanpa pinning
"args": ["mcp-virustotal"]

// ✅ Pin ke versi spesifik
"args": ["mcp-virustotal==0.2.1"]
```

> Full security analysis → [../security-notes/attack-surface-per-client.md](../security-notes/attack-surface-per-client.md)
