# Use Case 07 — Lateral Movement Detection

**Workflow:** Anomali login terdeteksi → correlate event → identifikasi pola → timeline → assessment

## Tools Used

| Tool | MCP Server | Type | Function |
|------|-----------|------|----------|
| Wazuh | community | Community | Windows event log, authentication events |
| AbuseIPDB | `mcp-abuseipdb` | Community | Reputasi source IP eksternal |
| VirusTotal | `mcp-virustotal` | Community | File hash yang mungkin di-drop |

## Prerequisites

- Wazuh dengan Windows agent terdeploy di endpoint
- Windows event logging aktif: event ID 4624, 4625, 4648, 4776
- AbuseIPDB API key

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
        "WAZUH_PASSWORD": "your_password"
      }
    },
    "abuseipdb": {
      "command": "uvx",
      "args": ["mcp-abuseipdb"],
      "env": {
        "ABUSEIPDB_API_KEY": "your_key"
      }
    }
  }
}
```

## Demo

### Skenario

Wazuh alert: satu user account login ke 5 host berbeda dalam 20 menit. Pola tidak normal untuk user tersebut.

### Prompt ke Model

```
Wazuh alert: user "jdoe" login ke 5 host berbeda dalam 20 menit.
Time window: 2026-03-12 10:00:00 - 10:20:00 UTC

Investigasi:
1. Query Wazuh untuk semua event login user "jdoe" dalam 2 jam terakhir
   (event ID 4624, logon type 3 dan 10)
2. Buat timeline: host mana, kapan, dari source IP mana
3. Identifikasi apakah ada logon failure sebelum success (brute force pattern)
4. Cek apakah ada proses atau file yang di-create setelah login di tiap host
5. Assessment: lateral movement atau legitimate activity?
   Kalau lateral movement, siapa yang mungkin jadi initial access point?
```

### Expected Tool Call Sequence

1. `wazuh_search_alerts(user:jdoe AND rule.id:(4624 OR 4625), timerange:2h)`
2. Model build timeline dari hasil query
3. `wazuh_search_alerts(hostname:<each_host> AND timerange:<post_login>)` — cek aktivitas post-login
4. `abuseipdb_check(<source_ip>)` — kalau ada source IP eksternal
5. Synthesize → lateral movement assessment + timeline

## ⚠️ Security Notes

- **Data sensitivity tinggi:** Query ini return username, hostname, dan IP internal — treat hasilnya dengan appropriate data handling
- **Time range:** Batasi query ke window yang relevan — query tanpa batas waktu di SIEM besar bisa berdampak ke performa
- **Korelasi lintas host:** Untuk detection lateral movement yang akurat, pastikan Wazuh agent sudah di-deploy di semua endpoint yang relevan — coverage gap akan membuat timeline tidak lengkap

> Security notes lengkap → [../../Security Notes/mcp-threat-landscape.md](../../Security%20Notes/mcp-threat-landscape.md)
