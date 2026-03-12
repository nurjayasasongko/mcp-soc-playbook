# Windsurf — MCP Config Reference

> 📖 Official docs: [docs.windsurf.com — Cascade MCP Integration](https://docs.windsurf.com/windsurf/cascade/mcp)

Windsurf dari Codeium adalah code editor dengan built-in AI bernama Cascade yang support MCP. Setup mirip Cursor — ada plugin store dan config JSON. Tidak ada scope isolation; semua server bersifat global. Satu hal yang membedakan Windsurf: mendukung env var interpolation langsung di config file.

## Config File Location

| OS | Path |
|----|------|
| Windows | `%USERPROFILE%\.codeium\mcp_config.json` |
| macOS | `~/.codeium/mcp_config.json` |
| Linux | `~/.codeium/mcp_config.json` |

## Base Structure

```json
{
  "mcpServers": {
    "<nama-server>": {
      "command": "<executable>",
      "args": ["<arg1>"],
      "env": {
        "API_KEY": "$API_KEY"
      }
    }
  }
}
```

**Catatan:** Windsurf mendukung interpolasi env var dengan format `$NAMA_VAR` di field `command`, `args`, `env`, dan `serverUrl`. Ini memungkinkan API key disimpan di environment OS, bukan di config file.

## Supported Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `command` | string | ✅ (stdio) | Executable yang dijalankan |
| `args` | array | ❌ | Arguments ke command |
| `env` | object | ❌ | Environment variables |
| `serverUrl` | string | ✅ (http) | URL remote server (HTTP transport) |
| `headers` | object | ❌ | HTTP headers, misalnya untuk auth token |

## Transport Types

### stdio — Local Process

```json
{
  "mcpServers": {
    "virustotal": {
      "command": "uvx",
      "args": ["mcp-virustotal==0.2.1"],
      "env": {
        "VT_API_KEY": "$VT_API_KEY"
      }
    }
  }
}
```

### HTTP — Remote Server

Windsurf menggunakan field `serverUrl` (bukan `url`) untuk transport HTTP.

```json
{
  "mcpServers": {
    "qradar": {
      "serverUrl": "http://your-qradar-mcp-server:8080/mcp",
      "headers": {
        "Authorization": "Bearer your-token"
      }
    }
  }
}
```

## Setup via GUI

Windsurf punya MCP Plugin Store untuk onboarding yang lebih mudah:

`Settings → Tools → Windsurf Settings → Add Server`

Untuk server yang tidak ada di store, klik **View Raw Config** dan edit `mcp_config.json` langsung.

## Real Example: Malware Analysis

```json
{
  "mcpServers": {
    "remnux": {
      "command": "docker",
      "args": [
        "run", "--rm", "-i",
        "--network", "none",
        "-v", "/home/user/samples:/samples:ro",
        "remnux/mcp-server"
      ]
    },
    "virustotal": {
      "command": "uvx",
      "args": ["mcp-virustotal==0.2.1"],
      "env": {
        "VT_API_KEY": "$VT_API_KEY"
      }
    }
  }
}
```

## ⚠️ Security Considerations

**Tidak ada scope isolation**

Semua server aktif secara global di semua sesi Windsurf. Tidak ada equivalen dari Claude Code's `-s local`. Mitigasi: hanya tambahkan server yang aktif dipakai, remove segera setelah tidak dibutuhkan.

**Workaround untuk per-project isolation**

Karena tidak ada native scope isolation, bisa buat beberapa profil config manual:

```bash
# Simpan config aktif sebelum switch project
cp ~/.codeium/mcp_config.json ~/.codeium/mcp_config.backup.json

# Pakai config minimal saat tidak sedang investigation
cp ~/.codeium/mcp_config.minimal.json ~/.codeium/mcp_config.json
```

**Gunakan env var interpolation, bukan hardcode**

```json
// ❌ Jangan
"env": { "VT_API_KEY": "vt-abc123-real-key" }

// ✅ Interpolasi dari OS env
"env": { "VT_API_KEY": "$VT_API_KEY" }
```

**Version pinning**

```json
// ❌ Tanpa pinning
"args": ["mcp-virustotal"]

// ✅ Pin ke versi spesifik
"args": ["mcp-virustotal==0.2.1"]
```

> Full security analysis → [../security-notes/attack-surface-per-client.md](../security-notes/attack-surface-per-client.md)
