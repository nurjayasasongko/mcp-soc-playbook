# Use Case 05 — OSINT Recon

**Workflow:** Target domain/IP/org → passive recon → surface map → threat context → recon report

## Tools Used

| Tool | MCP Server | Type | Function |
|------|-----------|------|----------|
| Shodan | `mcp-shodan` | Community | Internet-facing asset discovery |
| VirusTotal | `mcp-virustotal` | Community | Domain/IP reputation, passive DNS |
| AbuseIPDB | `mcp-abuseipdb` | Community | IP abuse history |

## Prerequisites

- Shodan API key (free tier untuk basic search)
- VirusTotal API key
- AbuseIPDB API key

## Config

### Claude Desktop

```json
{
  "mcpServers": {
    "shodan": {
      "command": "uvx",
      "args": ["mcp-shodan"],
      "env": {
        "SHODAN_API_KEY": "your_shodan_key"
      }
    },
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
        "ABUSEIPDB_API_KEY": "your_abuseipdb_key"
      }
    }
  }
}
```

## Demo

### Skenario

Threat intel report menyebut domain `evil-example.com` sebagai bagian dari kampanye phishing yang sedang aktif. Mau lakukan recon untuk understand infrastructure-nya.

### Prompt ke Model

```
Domain mencurigakan dari threat intel report: evil-example.com

Lakukan passive recon:
1. Cek reputasi domain di VirusTotal — termasuk passive DNS dan historical data
2. Cari IP yang berasosiasi dengan domain ini
3. Untuk setiap IP yang ditemukan, cek di Shodan:
   - Port apa yang terbuka?
   - Service apa yang jalan?
   - Ada banner atau fingerprint yang menarik?
4. Cek IP-IP itu di AbuseIPDB
5. Buat infrastructure map: domain → IP → port/service
6. Assessment: apakah ini dedicated server atau shared hosting?
   Ada indikasi infrastructure lain yang terkait?
```

### Expected Tool Call Sequence

1. `virustotal_domain_lookup(evil-example.com)` — passive DNS, reputation
2. Extract IP list dari hasil VT
3. `shodan_host_lookup(<ip>)` — untuk tiap IP
4. `abuseipdb_check(<ip>)` — untuk tiap IP
5. Synthesize → infrastructure map + assessment

## ⚠️ Security Notes

- **Passive only:** Use case ini menggunakan passive recon — tidak ada koneksi langsung ke target. Shodan query data yang sudah di-index, bukan scan real-time
- **Rate limiting Shodan:** Free tier Shodan sangat terbatas. Kalau banyak IP yang perlu dicek, pertimbangkan batching manual
- **Legal boundary:** OSINT passive recon umumnya aman secara legal — tapi pastikan scope engagement jelas sebelum mulai kalau ini untuk engagement formal

> Security notes lengkap → [../../security-notes/official-vs-community.md](../../security-notes/official-vs-community.md)
