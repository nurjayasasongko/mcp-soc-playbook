# 🛡️ MCP SOC Playbook

> A practical reference for connecting SOC tools to LLMs via Model Context Protocol (MCP) — with a security-first perspective.

## What is this?

This repo is a companion to the [MCP Security Article Series](https://medium.com/@nurjayasasongko) — a structured reference for SOC analysts and security engineers who want to integrate AI into their workflow using MCP.

Most MCP repos out there focus on "how to install." This one focuses on:
- ✅ **What to configure** — parameter reference per MCP client
- ✅ **How to use it for SOC workflows** — ready-to-use use cases
- ✅ **What to watch out for** — security notes per tool and client

---

## 📁 Repo Structure

```
mcp-soc-playbook/
│
├── MCP Host/         → MCP client config reference (Claude, Cursor, VS Code, etc.)
├── MCP Server/       → MCP server reference per SOC tool category
├── Usecases/         → End-to-end SOC workflow examples
├── Security Notes/   → Attack surface analysis, hardening guides
└── Labs/             → Docker Compose & WSL setup for local lab
```

---

## 🔌 MCP Clients Covered

| Client | Config Reference |
|--------|-----------------|
| Claude Desktop | [MCP Host/claude-desktop.md](MCP%20Host/claude-desktop.md) |
| Claude Code (CLI) | [MCP Host/claude-code.md](MCP%20Host/claude-code.md) |
| VS Code | [MCP Host/vscode.md](MCP%20Host/vscode.md) |
| Cursor | [MCP Host/cursor.md](MCP%20Host/cursor.md) |
| Windsurf | [MCP Host/windsurf.md](MCP%20Host/windsurf.md) |

---

## 🧰 SOC Tools Coverage

### ✅ Official MCP Servers (by Vendor)

| Category | Tool |
|----------|------|
| SIEM | IBM QRadar, Microsoft Sentinel, Splunk, Google Chronicle, Elastic Security |
| EDR/XDR | CrowdStrike Falcon, Palo Alto Cortex |
| Cloud Security | Wiz, Cloudflare, Cycode, AWS, Azure |
| Network/Firewall | Palo Alto PAN-OS, Check Point Quantum, Illumio |
| Malware Analysis | REMnux |
| Pentest/DAST | Burp Suite, AKTO |
| DevSecOps | GitHub, Semgrep, SonarQube |
| Collaboration | Atlassian Jira, Docker |

### 🟡 Community MCP Servers (Third Party)

| Category | Tool |
|----------|------|
| SIEM | Wazuh |
| Threat Intel | VirusTotal, Shodan, OpenCTI, MISP, AbuseIPDB, URLhaus, MalwareBazaar, ThreatFox |
| SOAR / Case Mgmt | TheHive, Cortex |
| Recon / OSINT | Nmap, Masscan, Nuclei, Subfinder, Amass, WhatWeb, BloodHound, SQLMap |
| Malware Analysis | Radare2, Ghidra, YARA, CAPA, Binwalk, Gitleaks |
| Bundled Collections | FuzzingLabs mcp-security-hub, HexStrike AI |

> ⚠️ **Important:** Community servers are not audited by vendors. Review source code before use in production. See [Security Notes/official-vs-community.md](Security%20Notes/official-vs-community.md)

---

## 🎯 Use Cases

| # | Use Case | Tools Involved |
|---|----------|---------------|
| 01 | [IOC Enrichment](Usecases/01-ioc-enrichment/) | VirusTotal + AbuseIPDB + MalwareBazaar |
| 02 | [Malware Triage](Usecases/02-malware-triage/) | REMnux + VirusTotal |
| 03 | [Threat Hunting via SIEM](Usecases/03-threat-hunting-siem/) | IBM QRadar / Wazuh |
| 04 | [Incident Case Creation](Usecases/04-incident-case-creation/) | TheHive + Cortex |
| 05 | [OSINT Recon](Usecases/05-osint-recon/) | Shodan + Subfinder + Nmap |
| 06 | [Detection Engineering](Usecases/06-detection-engineering/) | Sigma + Wazuh |
| 07 | [Lateral Movement Detection](Usecases/07-lateral-movement-detection/) | BloodHound + Wazuh |
| 08 | [Phishing Analysis](Usecases/08-phishing-analysis/) | URLhaus + VirusTotal + TheHive |

---

## 🔐 Security Notes

This repo includes a dedicated `Security Notes/` section — because connecting AI to your SOC tools creates **new attack surfaces** that need to be understood.

- [Attack Surface per Client](Security%20Notes/attack-surface-per-client.md)
- [Official vs Community: Why It Matters](Security%20Notes/official-vs-community.md)
- [Secrets Management](Security%20Notes/secrets-management.md)
- [Least Privilege Guide](Security%20Notes/least-privilege-guide.md)
- [MCP Threat Landscape](Security%20Notes/mcp-threat-landscape.md)

---

## 🧪 Lab Setup

Want to reproduce the use cases locally?

- [Docker Compose — Full SOC Stack](Labs/docker-compose.yml)
- [WSL Setup Guide](Labs/wsl-setup.md)
- [Minimal Lab (VirusTotal + TheHive + Wazuh)](Labs/minimal-lab.md)

---

## 📖 Related Articles

| # | Article | Link |
|---|---------|------|
| 1 | Mengenal MCP: Fondasi Baru Integrasi LLM dan Tools | [Medium](https://medium.com/@nurjayasasongko/mengenal-model-context-protocol-mcp-fondasi-baru-integrasi-llm-dan-tools-a340ba674d1d) |
| 2 | Membedah Arsitektur MCP: Komponen dan Layer Protokol | [Medium](https://medium.com/@nurjayasasongko/membedah-arsitektur-mcp-komponen-dan-layer-protokol-284020695cea) |
| 3 | MCP in Practice: Setup & Integrasi per Client | Coming soon |
| 4 | MCP Threat Landscape: Attack Surface & Vektor Serangan | Coming soon |
| 5 | Detection & Monitoring MCP di SOC Environment | Coming soon |

---

## ⚠️ Disclaimer

This repo is for **educational and research purposes only**. Offensive tools listed here (Metasploit, SQLMap, etc.) are included for documentation purposes. Use responsibly and only in environments you have explicit permission to test.

---

## 🤝 Contributing

Found a new SOC tool with MCP support? Config parameter that's outdated? Open a PR or issue.

---

*Maintained by [Nurjaya Sasongko](https://medium.com/@nurjayasasongko) — L2 SOC Analyst | AI Security*
