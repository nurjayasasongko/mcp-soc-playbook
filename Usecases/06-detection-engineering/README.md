# Use Case 06 — Detection Engineering

**Workflow:** IOC atau TTP baru → research context → draft detection rule → validate logic → document

## Tools Used

| Tool | MCP Server | Type | Function |
|------|-----------|------|----------|
| VirusTotal | `mcp-virustotal` | Community | Sample behavior, YARA matches |
| OpenCTI | `opencti-mcp` | Community | TTP context, ATT&CK mapping |
| Wazuh | community | Community | Test rule di environment lab |

## Prerequisites

- VirusTotal API key
- OpenCTI instance (opsional tapi sangat membantu)
- Wazuh lab instance untuk test rule

## Config

### Claude Desktop

```json
{
  "mcpServers": {
    "virustotal": {
      "command": "uvx",
      "args": ["mcp-virustotal==0.2.1"],
      "env": {
        "VT_API_KEY": "your_vt_key"
      }
    },
    "wazuh": {
      "command": "python",
      "args": ["-m", "wazuh_mcp_server"],
      "env": {
        "WAZUH_API_URL": "https://localhost:55000",
        "WAZUH_USER": "your_user",
        "WAZUH_PASSWORD": "your_password"
      }
    }
  }
}
```

## Demo

### Skenario

Muncul malware family baru — `AgentTesla` variant dengan teknik persistence baru via Registry Run key. Mau buat detection rule untuk Wazuh.

### Prompt ke Model

```
Malware family: AgentTesla — variant baru dengan persistence via Registry Run key.
Hash sample: d41d8cd98f00b204e9800998ecf8427e

Bantu saya buat detection rule untuk Wazuh:
1. Cek behavior sample ini di VirusTotal — fokus ke Registry activity
2. Identifikasi Registry key yang dipakai untuk persistence
3. Draft Wazuh rule XML untuk detect aktivitas Registry ini
4. Identifikasi false positive yang mungkin muncul dari rule ini
5. Suggest additional data source yang perlu di-enable untuk coverage lebih baik
```

### Expected Tool Call Sequence

1. `virustotal_file_behavior(d41d8cd98f00b204e9800998ecf8427e)` — behavior report
2. Extract Registry keys dari behavior report
3. Model draft Wazuh rule XML berdasarkan temuan
4. Analisis potensi false positive
5. Rekomendasi data source tambahan

## ⚠️ Security Notes

- **Rule testing di lab dulu:** Jangan deploy rule langsung ke production — test di Wazuh lab instance untuk validate false positive rate
- **Sample hash sensitivity:** Hash yang dikirim ke VT akan ter-log. Kalau sample dari engagement yang sedang berjalan, koordinasikan disclosure dulu

> Least privilege guide → [../../Security Notes/least-privilege-guide.md](../../Security%20Notes/least-privilege-guide.md)
