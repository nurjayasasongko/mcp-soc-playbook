# Claude Desktop — MCP Config Reference

> 📖 Official docs: [docs.anthropic.com — MCP Overview](https://docs.anthropic.com/en/docs/agents-and-tools/mcp)

Claude Desktop adalah aplikasi desktop dari Anthropic yang paling umum dipakai untuk explorasi MCP. Config MCP dilakukan via file JSON — tidak ada CLI, tidak ada scope isolation. Semua server yang dikonfigurasi aktif secara global di semua sesi.

## Config File Location

| OS | Path |
|----|------|
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Linux | `~/.config/Claude/claude_desktop_config.json` |

## Base Structure

```json
{
  "mcpServers": {
    "<nama-server>": {
      "command": "<executable>",
      "args": ["<arg1>", "<arg2>"],
      "env": {
        "API_KEY": "<your-key>"
      }
    }
  }
}
```

## Supported Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `command` | string | ✅ | Executable yang dijalankan (`python`, `npx`, `uvx`, `docker`) |
| `args` | array | ✅ | Arguments ke command |
| `env` | object | ❌ | Environment variables yang di-inject saat runtime |
| `type` | string | ❌ | Transport: `stdio` (default) atau `http` |
| `url` | string | ❌ | Wajib jika `type: "http"` — URL remote server |

## Transport Types

### stdio — Local Process

Untuk server yang berjalan di mesin yang sama. Paling umum untuk lab dan dev environment.

```json
{
  "mcpServers": {
    "wazuh": {
      "command": "python",
      "args": ["-m", "wazuh_mcp_server"],
      "env": {
        "WAZUH_API_URL": "https://localhost:55000",
        "WAZUH_USER": "wazuh",
        "WAZUH_PASSWORD": "your_password"
      }
    }
  }
}
```

### Streamable HTTP — Remote Server

Untuk server yang berjalan di mesin terpisah atau di network.

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

> **Catatan:** SSE (Server-Sent Events) adalah transport lama yang sudah deprecated. Gunakan Streamable HTTP untuk setup remote baru.

## Scope

Claude Desktop **tidak mendukung scope isolation**. Semua server di config ini aktif di seluruh sesi dan conversation — tidak ada pembeda per project.

Implikasi: server SIEM dengan write access yang dikonfigurasi di Claude Desktop akan aktif bahkan saat kamu tidak sedang melakukan investigasi. Remove server yang tidak aktif dipakai.

## Real Example: IOC Enrichment

```json
{
  "mcpServers": {
    "virustotal": {
      "command": "uvx",
      "args": ["mcp-virustotal==0.2.1"],
      "env": {
        "VT_API_KEY": "your_virustotal_api_key"
      }
    },
    "abuseipdb": {
      "command": "uvx",
      "args": ["mcp-abuseipdb"],
      "env": {
        "ABUSEIPDB_API_KEY": "your_abuseipdb_key"
      }
    }
  }
}
```

## ⚠️ Security Considerations

**Config file readable oleh semua proses user-level**

File config bisa dibaca oleh proses apapun yang berjalan di bawah user yang sama. Di shared machine, ini berarti API key yang disimpan di `env` bisa diakses proses lain.

```bash
# macOS/Linux — restrict file permissions
chmod 600 ~/Library/Application\ Support/Claude/claude_desktop_config.json

# Windows — check permissions
icacls "%APPDATA%\Claude\claude_desktop_config.json"
```

**Jangan hardcode API key**

```json
// ❌ Jangan
"env": { "VT_API_KEY": "vt-abc123-hardcoded" }

// ✅ Set env var di OS, referensikan di sini
"env": { "VT_API_KEY": "${VT_API_KEY}" }
```

**Version pinning untuk community server**

```json
// ❌ Tanpa pinning — versi bisa berubah kapan saja
"args": ["mcp-virustotal"]

// ✅ Pin ke versi spesifik
"args": ["mcp-virustotal==0.2.1"]
```

> Full security analysis → [../security-notes/attack-surface-per-client.md](../security-notes/attack-surface-per-client.md)
> Secrets management guide → [../security-notes/secrets-management.md](../security-notes/secrets-management.md)
