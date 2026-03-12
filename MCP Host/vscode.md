# VS Code — MCP Config Reference

> 📖 Official docs: [code.visualstudio.com — Add and manage MCP servers](https://code.visualstudio.com/docs/copilot/customization/mcp-servers)
> 📖 Config reference: [code.visualstudio.com — MCP configuration reference](https://code.visualstudio.com/docs/copilot/reference/mcp-configuration)

VS Code support MCP via GitHub Copilot agent mode (built-in) atau ekstensi Continue. Ada dua level config: workspace (per project) dan user profile (global). Keunggulan VS Code: syntax `${input:}` untuk menyimpan secrets secara aman tanpa menulis ke file config.

## Config File Location

| Level | Path | Scope |
|-------|------|-------|
| Workspace | `.vscode/mcp.json` | Hanya project ini |
| User profile | Via command `MCP: Open User Configuration` | Semua project |

## Base Structure

```json
{
  "servers": {
    "<nama-server>": {
      "type": "stdio",
      "command": "<executable>",
      "args": ["<arg1>"],
      "env": {
        "API_KEY": "${env:API_KEY}"
      }
    }
  }
}
```

## Supported Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `type` | string | ✅ | `stdio` atau `http` |
| `command` | string | ✅ (stdio) | Executable yang dijalankan |
| `args` | array | ❌ | Arguments ke command |
| `env` | object | ❌ | Environment variables |
| `url` | string | ✅ (http) | URL remote server |
| `inputs` | array | ❌ | Input variable definitions untuk secrets |

## Transport Types

### stdio — Local Process

```json
{
  "servers": {
    "virustotal": {
      "type": "stdio",
      "command": "uvx",
      "args": ["mcp-virustotal==0.2.1"],
      "env": {
        "VT_API_KEY": "${env:VT_API_KEY}"
      }
    }
  }
}
```

### Streamable HTTP — Remote Server

VS Code mencoba Streamable HTTP dulu, fallback ke SSE kalau tidak didukung server.

```json
{
  "servers": {
    "qradar": {
      "type": "http",
      "url": "http://your-qradar-mcp-server:8080/mcp"
    }
  }
}
```

## Input Variables untuk Secrets

VS Code punya dua cara referensikan secrets tanpa hardcode:

**`${env:NAMA_VAR}`** — pull dari environment OS secara langsung:

```json
"env": {
  "VT_API_KEY": "${env:VT_API_KEY}"
}
```

**`${input:variable-id}`** — VS Code prompt sekali, simpan secara terenkripsi:

```json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "vt-key",
      "description": "VirusTotal API Key",
      "password": true
    }
  ],
  "servers": {
    "virustotal": {
      "type": "stdio",
      "command": "uvx",
      "args": ["mcp-virustotal==0.2.1"],
      "env": {
        "VT_API_KEY": "${input:vt-key}"
      }
    }
  }
}
```

Dengan `${input:}`, VS Code akan minta API key sekali saat server pertama kali start, lalu menyimpannya secara encrypted — tidak perlu set env var di OS.

## Real Example: Threat Hunting dengan Wazuh

```json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "wazuh-password",
      "description": "Wazuh API Password",
      "password": true
    }
  ],
  "servers": {
    "wazuh": {
      "type": "stdio",
      "command": "python",
      "args": ["-m", "wazuh_mcp_server"],
      "env": {
        "WAZUH_API_URL": "${env:WAZUH_API_URL}",
        "WAZUH_USER": "${env:WAZUH_USER}",
        "WAZUH_PASSWORD": "${input:wazuh-password}"
      }
    },
    "virustotal": {
      "type": "stdio",
      "command": "uvx",
      "args": ["mcp-virustotal==0.2.1"],
      "env": {
        "VT_API_KEY": "${env:VT_API_KEY}"
      }
    }
  }
}
```

## ⚠️ Security Considerations

**Risiko utama: workspace config ter-commit ke repo**

`.vscode/mcp.json` sangat mudah ikut ter-commit, terutama jika belum ada `.gitignore` yang benar.

```bash
echo ".vscode/mcp.json" >> .gitignore
```

**Gunakan `${env:}` atau `${input:}`, bukan hardcode**

```json
// ❌ Jangan
"env": { "API_KEY": "vt-abc123-real-key" }

// ✅ Env var dari OS
"env": { "VT_API_KEY": "${env:VT_API_KEY}" }

// ✅ Atau input yang disimpan terenkripsi
"env": { "VT_API_KEY": "${input:vt-key}" }
```

**Trust prompt — jangan skip**

VS Code akan minta konfirmasi trust saat pertama kali start server baru. Baca config server-nya dulu sebelum approve.

> Full security analysis → [../security-notes/attack-surface-per-client.md](../security-notes/attack-surface-per-client.md)
