# Claude Desktop — MCP Config Reference

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
    "<server-name>": {
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
| `command` | string | ✅ | Executable to run (e.g. `python`, `npx`, `uvx`) |
| `args` | array | ✅ | Arguments passed to the command |
| `env` | object | ❌ | Environment variables injected at runtime |
| `type` | string | ❌ | Transport type: `stdio` (default) or `http` |
| `url` | string | ❌ | Required if `type: "http"` — remote server URL |

## Transport Types

### stdio (Local Process)
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

### Streamable HTTP (Remote Server)
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

## Real Example: VirusTotal MCP

```json
{
  "mcpServers": {
    "virustotal": {
      "command": "uvx",
      "args": ["mcp-virustotal"],
      "env": {
        "VT_API_KEY": "your_virustotal_api_key_here"
      }
    }
  }
}
```

## Real Example: REMnux MCP (via Docker)

```json
{
  "mcpServers": {
    "remnux": {
      "command": "docker",
      "args": [
        "run", "--rm", "-i",
        "-v", "/samples:/samples",
        "remnux/mcp-server"
      ]
    }
  }
}
```

## Scope

Claude Desktop **does not support scope isolation** (no local vs user distinction). All servers configured here are globally available to all conversations.

> ⚠️ **Security Note:** Because Claude Desktop has no scope isolation, avoid connecting high-privilege servers (e.g., SIEM write access) unless actively needed. Remove servers when not in use.

## ⚠️ Security Considerations

**Config file permissions**
The config file is readable by any process running under the same user account. On shared machines, this means API keys stored in `env` are accessible to other user-level processes.

```bash
# Windows — check who can read the config
icacls "%APPDATA%\Claude\claude_desktop_config.json"

# macOS/Linux — lock down permissions
chmod 600 ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

**Never hardcode secrets directly**
Prefer referencing environment variables from the system rather than embedding raw values:

```json
// ❌ Don't do this
"env": { "API_KEY": "vt-abc123-hardcoded" }

// ✅ Do this — set the env var at OS level, reference here
"env": { "VT_API_KEY": "${VT_API_KEY}" }
```

> Full security analysis → [../Security Notes/attack-surface-per-client.md](../Security%20Notes/attack-surface-per-client.md)
> Secrets management guide → [../Security Notes/secrets-management.md](../Security%20Notes/secrets-management.md)
