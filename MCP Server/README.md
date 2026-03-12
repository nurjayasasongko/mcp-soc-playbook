# MCP Server Reference — SOC Tools

Daftar MCP server yang relevan untuk workflow SOC, dikelompokkan per kategori tools.

> **Legend:**
> - ✅ **Official** — dikelola langsung oleh vendor
> - 🟡 **Community** — third-party, belum diaudit vendor
>
> Sebelum pakai community server di environment nyata, baca → [security-notes/official-vs-community.md](../security-notes/official-vs-community.md)

---

## SIEM

| Tool | Status | Repo / Link | Catatan |
|------|--------|-------------|---------|
| IBM QRadar | ✅ Official | [IBM/qradar-mcp-server](https://github.com/IBM/qradar-mcp-server) | 728+ API endpoints via 4 tools |
| Microsoft Sentinel | ✅ Official | [Microsoft MCP](https://learn.microsoft.com/en-us/azure/sentinel/) | Support Copilot, Copilot Studio, ChatGPT |
| Splunk | ✅ Official | [Splunk MCP](https://splunkbase.splunk.com/) | Listed di Microsoft Marketplace |
| Google Chronicle / SecOps | ✅ Official | [Google SecOps](https://cloud.google.com/chronicle) | Chronicle + Mandiant integration |
| Elastic Security | ✅ Official | [Elastic MCP](https://www.elastic.co/security) | SIEM & threat hunting via natural language |
| Wazuh (Rust, lightweight) | 🟡 Community | [gbrigandi/mcp-server-wazuh](https://github.com/gbrigandi/mcp-server-wazuh) | stdio only, build dari source (Rust) |
| Wazuh (Python, production) | 🟡 Community | [gensecaihq/Wazuh-MCP-Server](https://github.com/gensecaihq/Wazuh-MCP-Server) | 48 tools, HTTP + auth, lebih production-ready |

---

## EDR / XDR

| Tool | Status | Repo / Link | Catatan |
|------|--------|-------------|---------|
| CrowdStrike Falcon | ✅ Official | [CrowdStrike/falcon-mcp](https://github.com/CrowdStrike/falcon-mcp) | Detections, incidents, TI, vuln, identity — modular |
| Palo Alto Cortex XDR | ✅ Official | [Palo Alto Cortex](https://docs.paloaltonetworks.com/cortex/cortex-xdr) | Full Cortex platform |

---

## Threat Intelligence

| Tool | Status | Repo / Link | Catatan |
|------|--------|-------------|---------|
| VirusTotal | 🟡 Community | [BurtTheCoder/mcp-virustotal](https://github.com/BurtTheCoder/mcp-virustotal) | Hash, IP, domain, URL — bukan official Google/VT |
| Shodan | 🟡 Community | [BurtTheCoder/mcp-shodan](https://github.com/BurtTheCoder/mcp-shodan) | IP recon, port, CVE, DNS lookup |
| OpenCTI | 🟡 Community | [Spathodea-Network/opencti-mcp](https://github.com/Spathodea-Network/opencti-mcp) | Threat intel query, STIX data |
| MISP | 🟡 Community | [Eacus/misp-mcp](https://github.com/Eacus/misp-mcp) | Event, attribute, IOC lookup |
| AbuseIPDB | 🟡 Community | [community](https://github.com/topics/mcp-server) | IP reputation & abuse history |
| URLhaus | 🟡 Community | [community](https://urlhaus.abuse.ch/) | Malicious URL lookup oleh Abuse.ch |
| MalwareBazaar | 🟡 Community | [community](https://bazaar.abuse.ch/) | Sample & hash lookup oleh Abuse.ch |
| ThreatFox | 🟡 Community | [community](https://threatfox.abuse.ch/) | IOC database oleh Abuse.ch |
| DNStwist | 🟡 Community | [BurtTheCoder/mcp-dnstwist](https://github.com/BurtTheCoder/mcp-dnstwist) | Typosquatting & phishing domain detection |
| MITRE ATT&CK | 🟡 Community | [stoyky/mitre-attack-mcp](https://github.com/stoyky/mitre-attack-mcp) | Query TTP, threat actor, malware attribution |

---

## SOAR / Case Management

| Tool | Status | Repo / Link | Catatan |
|------|--------|-------------|---------|
| TheHive | 🟡 Community | [gbrigandi/mcp-server-thehive](https://github.com/gbrigandi/mcp-server-thehive) | Bukan official StrangeBee — Rust-based |
| Cortex | 🟡 Community | [gbrigandi/mcp-server-cortex](https://github.com/gbrigandi/mcp-server-cortex) | Paired dengan TheHive, 140+ analyzers |
| Atlassian Jira | ✅ Official | [Atlassian MCP](https://developer.atlassian.com/platform/mcp/) | Ticket & case management |

---

## Recon / OSINT

| Tool | Status | Repo / Link | Catatan |
|------|--------|-------------|---------|
| Nmap | 🟡 Community | [FuzzingLabs/mcp-security-hub](https://github.com/FuzzingLabs/mcp-security-hub) | Bagian dari security-hub (Docker Compose) |
| Masscan | 🟡 Community | [FuzzingLabs/mcp-security-hub](https://github.com/FuzzingLabs/mcp-security-hub) | High-speed port scanning |
| Nuclei | 🟡 Community | [FuzzingLabs/mcp-security-hub](https://github.com/FuzzingLabs/mcp-security-hub) | Vulnerability scanning via templates |
| Subfinder / FFUF | 🟡 Community | [FuzzingLabs/mcp-security-hub](https://github.com/FuzzingLabs/mcp-security-hub) | Subdomain enum & web fuzzing |
| WhatWeb | 🟡 Community | [FuzzingLabs/mcp-security-hub](https://github.com/FuzzingLabs/mcp-security-hub) | Technology fingerprinting |
| BloodHound | 🟡 Community | [community](https://github.com/topics/mcp-server) | AD attack path analysis |
| ExternalAttacker | 🟡 Community | [MorDavid/ExternalAttacker-MCP](https://github.com/MorDavid/ExternalAttacker-MCP) | Attack surface mapping |
| Maigret | 🟡 Community | [BurtTheCoder/mcp-maigret](https://github.com/BurtTheCoder/mcp-maigret) | OSINT username search lintas platform |

---

## Malware Analysis / Reverse Engineering

| Tool | Status | Repo / Link | Catatan |
|------|--------|-------------|---------|
| REMnux | ✅ Official | [REMnux/remnux-mcp-server](https://github.com/REMnux/remnux-mcp-server) | Static analysis, IOC extraction, PE analysis |
| Radare2 | 🟡 Community | [FuzzingLabs/mcp-security-hub](https://github.com/FuzzingLabs/mcp-security-hub) | Binary analysis & disassembly |
| Ghidra | 🟡 Community | [FuzzingLabs/mcp-security-hub](https://github.com/FuzzingLabs/mcp-security-hub) | RE via pyghidra headless mode |
| IDA Pro | 🟡 Community | [FuzzingLabs/mcp-security-hub](https://github.com/FuzzingLabs/mcp-security-hub) | LLM-controlled reverse engineering |
| YARA | 🟡 Community | [FuzzingLabs/mcp-security-hub](https://github.com/FuzzingLabs/mcp-security-hub) | Pattern matching & malware detection |
| CAPA | 🟡 Community | [FuzzingLabs/mcp-security-hub](https://github.com/FuzzingLabs/mcp-security-hub) | Capability detection di binary |
| Binwalk | 🟡 Community | [FuzzingLabs/mcp-security-hub](https://github.com/FuzzingLabs/mcp-security-hub) | Firmware analysis & extraction |

---

## Web Security / Pentest

| Tool | Status | Repo / Link | Catatan |
|------|--------|-------------|---------|
| Burp Suite | ✅ Official | [PortSwigger Burp MCP](https://portswigger.net/burp/documentation) | Official dari PortSwigger |
| Burp Suite (alt) | 🟡 Community | [Cyreslab-AI/burpsuite-mcp-server](https://github.com/Cyreslab-AI/burpsuite-mcp-server) | Community alternative |
| SQLMap | 🟡 Community | [FuzzingLabs/mcp-security-hub](https://github.com/FuzzingLabs/mcp-security-hub) | SQL injection testing |
| Nikto | 🟡 Community | [FuzzingLabs/mcp-security-hub](https://github.com/FuzzingLabs/mcp-security-hub) | Web server scanning |
| Nessus | 🟡 Community | [Cyreslab-AI/nessus-mcp-server](https://github.com/Cyreslab-AI/nessus-mcp-server) | Vulnerability scanning via Tenable |
| AKTO | ✅ Official | [AKTO MCP](https://www.akto.io/) | API security testing |

---

## Cloud Security

| Tool | Status | Repo / Link | Catatan |
|------|--------|-------------|---------|
| Wiz | ✅ Official | [Wiz MCP](https://www.wiz.io/mcp) | Cloud posture management |
| Cloudflare | ✅ Official | [cloudflare/mcp-server-cloudflare](https://github.com/cloudflare/mcp-server-cloudflare) | WAF, edge, Workers, API protection |
| Cycode | ✅ Official | [Cycode MCP](https://cycode.com/) | SAST, SCA, IaC scanning |
| AWS | ✅ Official | [AWS MCP](https://github.com/awslabs/mcp) | Security best practices & tools |
| Azure | ✅ Official | [Microsoft MCP](https://learn.microsoft.com/en-us/azure/) | Storage, CLI, security |

---

## Network / Firewall

| Tool | Status | Repo / Link | Catatan |
|------|--------|-------------|---------|
| Palo Alto PAN-OS | ✅ Official | [Palo Alto MCP](https://live.paloaltonetworks.com/) | FastMCP, policy query |
| Check Point Quantum | ✅ Official | [Check Point MCP](https://www.checkpoint.com/) | Compliance, policy management |
| Illumio | ✅ Official | [Illumio MCP](https://www.illumio.com/) | Workload & traffic flow visibility |

---

## DevSecOps

| Tool | Status | Repo / Link | Catatan |
|------|--------|-------------|---------|
| GitHub | ✅ Official | [github/github-mcp-server](https://github.com/github/github-mcp-server) | Dependabot, secret scanning, code review |
| Semgrep | ✅ Official | [Semgrep MCP](https://semgrep.dev/) | Code security via AI agents |
| SonarQube | ✅ Official | [Sonar MCP](https://www.sonarqube.org/) | Code quality & security analysis |
| Docker | ✅ Official | [Docker MCP](https://www.docker.com/products/ai/) | Container management |
| Gitleaks | 🟡 Community | [community](https://github.com/topics/mcp-server) | Secrets detection di Git repos |

---

## Detection Engineering

| Tool | Status | Repo / Link | Catatan |
|------|--------|-------------|---------|
| Sigma Rules DB | 🟡 Community | [community](https://github.com/topics/sigma-rules) | Sigma + Splunk ESCU + Elastic + KQL → SQLite |

---

## Bundled Collections

Kumpulan server dalam satu repo — cocok untuk lab, tidak untuk production langsung.

| Collection | Status | Repo | Isi |
|-----------|--------|------|-----|
| FuzzingLabs mcp-security-hub | 🟡 Community | [FuzzingLabs/mcp-security-hub](https://github.com/FuzzingLabs/mcp-security-hub) | 36+ server: Nmap, Ghidra, Nuclei, SQLMap, Hashcat, YARA, dll — Docker Compose ready |
| cyproxio mcp-for-security | 🟡 Community | [cyproxio/mcp-for-security](https://github.com/cyproxio/mcp-for-security) | SQLMap, FFUF, Nmap, Masscan bundle |

> ⚠️ Bundled collections = attack surface lebih besar. Review tiap server individual sebelum aktifkan.

---

## Ingin Tahu Lebih Lanjut?

- Evaluasi server sebelum pakai → [security-notes/official-vs-community.md](../security-notes/official-vs-community.md)
- Cara handle credentials → [security-notes/secrets-management.md](../security-notes/secrets-management.md)
- Direktori community server terlengkap → [modelcontextprotocol.io/servers](https://modelcontextprotocol.io/servers)
