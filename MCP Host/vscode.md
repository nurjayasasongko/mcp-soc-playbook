# VS Code — MCP Config Reference

VS Code support MCP lewat ekstensi AI — GitHub Copilot (agent mode) atau Continue. Ada dua level config: workspace dan global. Pilihan level ini punya implikasi security yang berbeda.

## Config File Location

| Level | Path | Scope |
|-------|------|-------|
| Workspace | `.vscode/mcp.json` | Hanya project ini |
| Global | `settings.json` via UI | Semua project |

**Prefer workspace config** untuk server yang spesifik ke satu project. Tapi perhatikan: workspace config bisa ter-commit ke repo kalau tidak dijaga.

## Base Structure

```json
{
  "mcp": {
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

## Env Var Syntax

VS Code pakai syntax khusus untuk referensikan env var dari OS:

```json
"env": {
  "VT_API_KEY": "${env:VT_API_KEY}"
}
```

Ini yang membedakan VS Code dari host lain — `${env:NAMA_VAR}` langsung pull dari environment OS. API key tidak perlu ditulis di config file.

## Real Example: Threat Hunting dengan Wazuh

```json
{
  "mcp": {
    "servers": {
      "wazuh": {
        "type": "stdio",
        "command": "python",
        "args": ["-m", "wazuh_mcp_server"],
        "env": {
          "WAZUH_API_URL": "${env:WAZUH_API_URL}",
          "WAZUH_USER": "${env:WAZUH_USER}",
          "WAZUH_PASSWORD": "${env:WAZUH_PASSWORD}"
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
}
```

## Real Example: Remote MCP Server via HTTP

```json
{
  "mcp": {
    "servers": {
      "qradar": {
        "type": "http",
        "url": "http://your-qradar-mcp-server:8080/mcp"
      }
    }
  }
}
```

## ⚠️ Security Considerations

**Risiko terbesar: commit ke repo**

`.vscode/mcp.json` di workspace sangat mudah ikut ter-commit — terutama kalau tim belum set `.gitignore` dengan benar.

```bash
# Tambahkan ini ke .gitignore sebelum apapun
echo ".vscode/mcp.json" >> .gitignore
```

**Gunakan `${env:}` syntax, jangan hardcode**

```json
// ❌ Jangan
"env": { "API_KEY": "vt-abc123-real-key" }

// ✅ Ini
"env": { "VT_API_KEY": "${env:VT_API_KEY}" }
```

**Set env var di OS sebelum buka VS Code**

```bash
# WSL / Linux / macOS
export VT_API_KEY="your-key-here"
export WAZUH_API_URL="https://localhost:55000"
code .
```

> Full analysis → [../security-notes/attack-surface-per-client.md](../security-notes/attack-surface-per-client.md)
