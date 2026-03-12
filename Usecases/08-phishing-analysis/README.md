# Use Case 08 — Phishing Analysis

**Workflow:** Email mencurigakan masuk → ekstrak artifact → analisis URL/attachment → verdict → user notification draft

## Tools Used

| Tool | MCP Server | Type | Function |
|------|-----------|------|----------|
| VirusTotal | `mcp-virustotal` | Community | URL scan, attachment hash lookup |
| AbuseIPDB | `mcp-abuseipdb` | Community | IP reputation sender |
| REMnux | `remnux/remnux-mcp-server` | Official | Analisis attachment jika ada |

## Prerequisites

- VirusTotal API key
- AbuseIPDB API key
- Docker (kalau ada attachment yang perlu dianalisis dengan REMnux)

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
    "abuseipdb": {
      "command": "uvx",
      "args": ["mcp-abuseipdb"],
      "env": {
        "ABUSEIPDB_API_KEY": "your_key"
      }
    },
    "remnux": {
      "command": "docker",
      "args": [
        "run", "--rm", "-i",
        "--network", "none",
        "-v", "/path/to/attachments:/samples:ro",
        "remnux/mcp-server"
      ]
    }
  }
}
```

## Demo

### Skenario

User melaporkan email mencurigakan. Email mengklaim dari HR, berisi link dan attachment `.docx`.

### Artifacts yang Diekstrak dari Email

```
From: hr-noreply@company-update.net
Reply-To: hr@legit-company.com
Sender IP: 185.220.101.45
URL di body: https://bit.ly/3xFakeLink → redirect ke http://malicious.example.com/payload
Attachment: Q1-Benefits-Update.docx
Hash attachment MD5: d41d8cd98f00b204e9800998ecf8427e
```

### Prompt ke Model

```
Email mencurigakan dilaporkan user. Artifacts yang sudah diekstrak:

From: hr-noreply@company-update.net (bukan domain company)
Sender IP: 185.220.101.45
URL: http://malicious.example.com/payload (setelah unshorten)
Attachment hash MD5: d41d8cd98f00b204e9800998ecf8427e
Attachment tersimpan di /samples/Q1-Benefits-Update.docx

Analisis:
1. Cek reputasi sender IP di AbuseIPDB
2. Scan URL di VirusTotal
3. Cek hash attachment di VirusTotal
4. Kalau VT miss attachment, analisis dengan REMnux:
   - Identify file type (benar-benar docx atau rename?)
   - Extract macro atau embedded object
   - Extract IOC
5. Verdict: phishing atau legitimate?
6. Draft notifikasi singkat untuk user yang menerima email ini
```

### Expected Tool Call Sequence

1. `abuseipdb_check(185.220.101.45)`
2. `virustotal_url_lookup(http://malicious.example.com/payload)`
3. `virustotal_file_lookup(d41d8cd98f00b204e9800998ecf8427e)`
4. Jika VT tidak detect: `identify_file_type(/samples/Q1-Benefits-Update.docx)`
5. `extract_strings(/samples/...)` — cari macro atau embedded URL
6. `extract_ioc(/samples/...)` — IOC untuk detection rule
7. Synthesize → verdict + draft notifikasi user

## ⚠️ Security Notes

- **Jangan buka attachment langsung:** Semua attachment harus dianalisis di environment isolated — REMnux via Docker dengan `--network none`
- **URL unshortening:** Unshorten URL sebelum submit ke VT — jangan klik langsung. Pakai tool seperti `unshorten.me` atau cek redirect chain manual
- **User notification:** Draft notifikasi ke user harus jelas tapi tidak menimbulkan panik — tone yang tepat penting untuk user compliance

> Security notes lengkap → [../../Security Notes/mcp-threat-landscape.md](../../Security%20Notes/mcp-threat-landscape.md)
