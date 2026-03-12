# Windsurf — MCP Config Reference

Windsurf dari Codeium adalah code editor dengan built-in AI (Cascade) yang support MCP. Setup-nya mirip Cursor — ada GUI dan config file JSON. Tidak ada scope isolation, semua server bersifat global.

## Config File Location

| OS | Path |
|----|------|
| Windows | `%APPDATA%\Codeium\windsurf\mcp_config.json` |
| macOS | `~/.codeium/windsurf/mcp_config.json` |
| Linux | `~/.codeium/windsurf/mcp_config.json` |

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

## Supported Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `command` | string | ✅ | Executable yang dijalankan |
| `args` | array | ❌ | Arguments ke command |
| `env` | object | ❌ | Environment variables |
| `type` | string | ❌ | `stdio` (default) atau `http` |
| `url` | string | ❌ | Required jika `type: "http"` |

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
        "VT_API_KEY": "your_vt_key"
      }
    }
  }
}
```

## ⚠️ Security Considerations

**Tidak ada scope isolation**

Semua server yang dikonfigurasi aktif secara global di semua sesi Windsurf. Tidak ada equivalent dari Claude Code's `-s local`.

Mitigasi: hanya tambahkan server yang memang aktif dipakai. Remove server yang sudah tidak dibutuhkan — jangan biarkan menumpuk.

**Config file global, bukan per project**

Berbeda dari VS Code yang punya workspace config, Windsurf hanya punya satu config file global. Ini berarti tidak ada cara native untuk isolasi server per project.

Workaround: buat beberapa profil manual dengan rename config file sesuai kebutuhan:

```bash
# Simpan config untuk project threat hunting
cp ~/.codeium/windsurf/mcp_config.json ~/.codeium/windsurf/mcp_config.threat-hunting.json

# Switch ke config minimal saat tidak sedang hunting
cp ~/.codeium/windsurf/mcp_config.minimal.json ~/.codeium/windsurf/mcp_config.json
```

**Version pinning tetap berlaku**

```json
// Jangan
"args": ["mcp-virustotal"]

// Pin versi
"args": ["mcp-virustotal==0.2.1"]
```

> Full analysis → [../Security Notes/attack-surface-per-client.md](../Security%20Notes/attack-surface-per-client.md)
