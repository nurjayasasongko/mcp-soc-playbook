# Lab Setup

Setup environment lokal untuk reproduce semua use case di repo ini.

## Option 1 — Minimal Lab

Untuk use case 01, 02, 04, 08 (IOC enrichment, malware triage, case creation, phishing):

**Requirements:**
- Docker & Docker Compose
- API keys: VirusTotal, AbuseIPDB (free tier cukup)
- Claude Desktop atau Claude Code

```bash
# Clone repo
git clone https://github.com/nurjayasasongko/mcp-soc-playbook
cd mcp-soc-playbook/Labs

# Setup env vars
cp .env.example .env
# Edit .env — isi API keys

# Spin up stack
docker compose -f docker-compose.minimal.yml up -d
```

**Stack yang dijalankan:**
- TheHive 5
- Cortex 3
- Elasticsearch (backend TheHive)

---

## Option 2 — Full SOC Lab

Untuk semua use case termasuk SIEM-based detection dan lateral movement:

```bash
docker compose -f docker-compose.full.yml up -d
```

**Stack yang dijalankan:**
- Wazuh (SIEM + EDR)
- TheHive 5 + Cortex 3
- Elasticsearch
- OpenCTI (optional, berat)

---

## Option 3 — WSL (Windows)

Untuk yang prefer setup di WSL tanpa Docker overhead:

Lihat [wsl-setup.md](wsl-setup.md)

---

## .env.example

```bash
# VirusTotal
VT_API_KEY=your_key_here

# AbuseIPDB
ABUSEIPDB_API_KEY=your_key_here

# TheHive
THEHIVE_URL=http://localhost:9000
THEHIVE_API_KEY=your_key_here

# Wazuh
WAZUH_API_URL=https://localhost:55000
WAZUH_USER=wazuh-wui
WAZUH_PASSWORD=your_password

# OpenCTI (optional)
OPENCTI_URL=http://localhost:8080
OPENCTI_TOKEN=your_token
```

> ⚠️ **Never commit your `.env` file.** It's already in `.gitignore` — keep it that way.
