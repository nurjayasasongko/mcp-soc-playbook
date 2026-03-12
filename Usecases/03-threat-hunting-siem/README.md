# Use Case 03 — Threat Hunting via SIEM

**Workflow:** Hipotesis threat → query SIEM → analisis pola → pivot ke threat intel → hunting report

## Tools Used

| Tool | MCP Server | Type | Function |
|------|-----------|------|----------|
| Wazuh | community | Community | SIEM query, alert retrieval |
| VirusTotal | `mcp-virustotal` | Community | IOC enrichment dari temuan hunting |
| OpenCTI | `opencti-mcp` | Community | Threat actor & TTP context |

## Prerequisites

- Wazuh instance yang berjalan (self-hosted)
- Wazuh API credentials
- VirusTotal API key
- OpenCTI instance (opsional)

## Config

### Claude Desktop

```json
{
  "mcpServers": {
    "wazuh": {
      "command": "python",
      "args": ["-m", "wazuh_mcp_server"],
      "env": {
        "WAZUH_API_URL": "https://localhost:55000",
        "WAZUH_USER": "your_user",
        "WAZUH_PASSWORD": "your_password",
        "WAZUH_VERIFY_SSL": "false"
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

### Claude Code (local scope)

```bash
claude mcp add wazuh -s local \
  -e WAZUH_API_URL=https://localhost:55000 \
  -e WAZUH_USER=your_user \
  -e WAZUH_PASSWORD=your_password \
  -- python -m wazuh_mcp_server

claude mcp add virustotal -s local \
  -e VT_API_KEY=${VT_API_KEY} \
  -- uvx mcp-virustotal==0.2.1
```

## Demo

### Skenario

Ada hipotesis: kemungkinan ada lateral movement via RDP di environment. Mau hunting berdasarkan event ID Windows yang relevan.

### Prompt ke Model

```
Saya mau hunting kemungkinan lateral movement via RDP di environment.

Langkah yang saya minta:
1. Query Wazuh untuk event Windows dengan ID 4624 (successful logon) 
   dengan logon type 10 (RemoteInteractive) dalam 72 jam terakhir
2. Identifikasi source IP yang login ke lebih dari 3 host berbeda
3. Untuk IP yang mencurigakan, cek reputasinya di VirusTotal
4. Buat summary: ada indikasi lateral movement atau tidak?
   Kalau ada, sebutkan timeline dan host yang terlibat
```

### Expected Tool Call Sequence

1. `wazuh_search_alerts(rule.id:4624 AND data.win.eventdata.logonType:10, timerange:72h)`
2. Analisis pola — model identify IP dengan multiple targets
3. `virustotal_ip_lookup(<suspicious_ip>)` — untuk tiap IP mencurigakan
4. Synthesize → hunting report dengan timeline

## ⚠️ Security Notes

- **Credential Wazuh di env:** Password Wazuh jangan hardcode di config — gunakan env var dari OS
- **Query scope:** Batasi time range query — query tanpa batas waktu di SIEM besar bisa berdampak ke performa
- **Data sensitivity:** Hasil query SIEM mungkin mengandung data sensitif — perhatikan kalau model dipakai via cloud API

> Security notes lengkap → [../../security-notes/secrets-management.md](../../security-notes/secrets-management.md)
