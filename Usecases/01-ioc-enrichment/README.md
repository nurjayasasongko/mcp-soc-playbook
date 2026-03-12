# Use Case 01 — IOC Enrichment

**Workflow:** Alert masuk → ekstrak IOC → enrichment otomatis via multiple sources → summary actionable

## Tools Used

| Tool | MCP Server | Type | Function |
|------|-----------|------|----------|
| VirusTotal | `mcp-virustotal` | Community | Hash, IP, domain, URL lookup |
| AbuseIPDB | `mcp-abuseipdb` | Community | IP reputation & abuse history |
| MalwareBazaar | `mcp-malwarebazaar` | Community | Malware sample lookup by hash |

## Prerequisites

- VirusTotal API key (free tier works)
- AbuseIPDB API key (free tier works)
- Claude Desktop atau Claude Code

## Config

### Claude Desktop

```json
{
  "mcpServers": {
    "virustotal": {
      "command": "uvx",
      "args": ["mcp-virustotal"],
      "env": {
        "VT_API_KEY": "your_vt_api_key"
      }
    },
    "abuseipdb": {
      "command": "uvx",
      "args": ["mcp-abuseipdb"],
      "env": {
        "ABUSEIPDB_API_KEY": "your_abuseipdb_key"
      }
    },
    "malwarebazaar": {
      "command": "uvx",
      "args": ["mcp-malwarebazaar"]
    }
  }
}
```

### VS Code (workspace)

```json
{
  "mcp": {
    "servers": {
      "virustotal": {
        "type": "stdio",
        "command": "uvx",
        "args": ["mcp-virustotal"],
        "env": {
          "VT_API_KEY": "${env:VT_API_KEY}"
        }
      },
      "abuseipdb": {
        "type": "stdio",
        "command": "uvx",
        "args": ["mcp-abuseipdb"],
        "env": {
          "ABUSEIPDB_API_KEY": "${env:ABUSEIPDB_API_KEY}"
        }
      }
    }
  }
}
```

## Demo

### Skenario

Alert dari EDR menunjukkan koneksi outbound ke IP `185.220.101.45` dengan file hash `d41d8cd98f00b204e9800998ecf8427e`.

### Prompt ke LLM

```
Saya punya alert EDR dengan detail berikut:
- Source IP: 192.168.1.50 (internal)
- Destination IP: 185.220.101.45
- File hash (MD5): d41d8cd98f00b204e9800998ecf8427e
- Timestamp: 2026-03-12 08:43:21 UTC

Tolong lakukan enrichment lengkap:
1. Cek reputasi IP destination di VirusTotal dan AbuseIPDB
2. Cek hash file di VirusTotal dan MalwareBazaar
3. Berikan summary: apakah ini true positive atau false positive?
4. Kalau TP, apa recommended next step?
```

### Expected Output

LLM akan secara otomatis:
1. Call `virustotal_ip_lookup(185.220.101.45)`
2. Call `abuseipdb_check(185.220.101.45)`
3. Call `virustotal_file_lookup(d41d8cd98f00b204e9800998ecf8427e)`
4. Call `malwarebazaar_lookup(d41d8cd98f00b204e9800998ecf8427e)`
5. Synthesize semua hasil jadi actionable summary

## ⚠️ Security Notes

- **Rate limiting:** VirusTotal free tier = 4 requests/min. Kalau volume alert tinggi, pertimbangkan batching atau upgrade tier
- **Data leakage:** Hash dan IP yang dikirim ke VirusTotal/AbuseIPDB akan ter-log di server mereka — jangan submit IOC dari investigasi yang sifatnya confidential sebelum disclosure
- **Community server trust:** Server VirusTotal dan AbuseIPDB ini bukan official vendor — review source code sebelum pakai di environment production

> Untuk threat model lengkap → [../../security-notes/official-vs-community.md](../../security-notes/official-vs-community.md)
