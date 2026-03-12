# Use Case 04 — Incident Case Creation

**Workflow:** Alert tervalidasi → enrichment → buat case di TheHive → assign observables → set severity → notify

## Tools Used

| Tool | MCP Server | Type | Function |
|------|-----------|------|----------|
| TheHive | community | Community | Case creation, observable management |
| VirusTotal | `mcp-virustotal` | Community | IOC enrichment sebelum masuk ke case |
| AbuseIPDB | `mcp-abuseipdb` | Community | IP reputation untuk severity scoring |

## Prerequisites

- TheHive instance yang berjalan (self-hosted atau cloud)
- TheHive API key
- VirusTotal + AbuseIPDB API key

## Config

### Claude Desktop

```json
{
  "mcpServers": {
    "thehive": {
      "command": "python",
      "args": ["-m", "thehive_mcp_server"],
      "env": {
        "THEHIVE_URL": "http://localhost:9000",
        "THEHIVE_API_KEY": "your_thehive_api_key"
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

Alert dari SIEM sudah divalidasi sebagai true positive: koneksi outbound ke IP C2 yang sudah diketahui, dari host internal `WORKSTATION-042`.

### Prompt ke Model

```
Alert sudah dikonfirmasi sebagai true positive. Detail:
- Host: WORKSTATION-042 (192.168.1.42)
- Destination IP: 185.220.101.45
- Port: 443
- Timestamp: 2026-03-12 09:15:00 UTC
- User yang login saat kejadian: john.doe

Lakukan:
1. Cek reputasi 185.220.101.45 di VirusTotal dan AbuseIPDB
2. Buat case baru di TheHive dengan:
   - Title: "C2 Communication from WORKSTATION-042"
   - Severity: sesuaikan berdasarkan hasil enrichment
   - Tags: c2, outbound, workstation
3. Tambahkan observables: IP destination, hostname, username
4. Set status ke "Open"
```

### Expected Tool Call Sequence

1. `virustotal_ip_lookup(185.220.101.45)`
2. `abuseipdb_check(185.220.101.45)`
3. `thehive_create_case(title, severity, tags)` — severity ditentukan berdasarkan hasil enrichment
4. `thehive_add_observable(case_id, ip, 185.220.101.45)`
5. `thehive_add_observable(case_id, hostname, WORKSTATION-042)`
6. `thehive_add_observable(case_id, username, john.doe)`
7. `thehive_update_status(case_id, Open)`

## ⚠️ Security Notes

- **TheHive write access:** Server ini punya akses untuk create dan modify case — pisahkan dari server read-only kalau ada
- **API key scope:** Gunakan API key TheHive dengan permission minimal yang dibutuhkan — bukan admin key
- **Observable sensitivity:** Username dan hostname internal yang masuk ke case bersifat sensitif — pastikan TheHive instance-nya di-access control dengan benar

> Least privilege guide → [../../security-notes/least-privilege-guide.md](../../security-notes/least-privilege-guide.md)
