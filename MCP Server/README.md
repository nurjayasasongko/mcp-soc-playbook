# MCP Server Reference — SOC Tools

Complete reference of MCP servers relevant to SOC workflows.

> Legend: ✅ Official (vendor-maintained) | 🟡 Community (third-party)

---

## SIEM

| Tool | Status | Server / Repo | Notes |
|------|--------|--------------|-------|
| IBM QRadar | ✅ Official | `IBM/qradar-mcp-server` | 728+ API endpoints |
| Microsoft Sentinel | ✅ Official | Microsoft official | Support Copilot, Copilot Studio, ChatGPT |
| Splunk | ✅ Official | Listed Microsoft Marketplace | |
| Google Chronicle / SecOps | ✅ Official | Google official | Chronicle + Mandiant integration |
| Elastic Security | ✅ Official | Elastic official | SIEM & threat hunting via NL |
| Wazuh | 🟡 Community | `gbrigandi/wazuh-mcp-server` | Open source, widely used |

---

## EDR / XDR

| Tool | Status | Server / Repo | Notes |
|------|--------|--------------|-------|
| CrowdStrike Falcon | ✅ Official | `CrowdStrike/falcon-mcp` | Detections, incidents, TI, vuln, identity |
| Palo Alto Cortex | ✅ Official | Palo Alto official | Full Cortex platform |

---

## Threat Intelligence

| Tool | Status | Server / Repo | Notes |
|------|--------|--------------|-------|
| VirusTotal | 🟡 Community | BurtTheCoder, ADEOSec | Not official Google/VT |
| Shodan | 🟡 Community | BurtTheCoder, ADEOSec, X3r0K | Multiple versions |
| OpenCTI | 🟡 Community | `Spathodea-Network/opencti-mcp` | Relatively mature |
| MISP | 🟡 Community | `jmstar85/misp-mcp` | Hardened version available |
| AbuseIPDB | 🟡 Community | Multiple | IP reputation |
| URLhaus | 🟡 Community | Community | Malicious URL lookup |
| MalwareBazaar | 🟡 Community | Community | Sample & hash lookup |
| ThreatFox | 🟡 Community | Community | IOC database by Abuse.ch |
| Malware Patrol | 🟡 Community | Community | Threat actor intel |

---

## SOAR / Case Management

| Tool | Status | Server / Repo | Notes |
|------|--------|--------------|-------|
| TheHive | 🟡 Community | `gbrigandi/thehive-mcp` | Not official StrangeBee |
| Cortex | 🟡 Community | `gbrigandi/cortex-mcp` | Paired with TheHive |
| Atlassian Jira | ✅ Official | Atlassian official | Ticket & case management |

---

## Recon / OSINT

| Tool | Status | Server / Repo | Notes |
|------|--------|--------------|-------|
| Nmap | 🟡 Community | FuzzingLabs/mcp-security-hub | Network scanning |
| Masscan | 🟡 Community | cyproxio/mcp-for-security | Fast port scanning |
| Nuclei | 🟡 Community | FuzzingLabs | Vulnerability scanning |
| Subfinder | 🟡 Community | Community | Subdomain enumeration |
| Amass | 🟡 Community | Community | Passive + active recon |
| Assetfinder | 🟡 Community | Community | Subdomain recon |
| WhatWeb | 🟡 Community | Community | Tech fingerprinting |
| FFUF | 🟡 Community | cyproxio | Web fuzzing |
| BloodHound | 🟡 Community | Community | AD attack path analysis |
| RoadRecon | 🟡 Community | Community | Azure AD enumeration |
| SQLMap | 🟡 Community | cyproxio | SQL injection testing |

---

## Malware Analysis / RE

| Tool | Status | Server / Repo | Notes |
|------|--------|--------------|-------|
| REMnux | ✅ Official | `REMnux/remnux-mcp-server` | Static analysis, IOC, PE |
| Radare2 | 🟡 Community | FuzzingLabs | Binary analysis |
| Ghidra | 🟡 Community | Community | Reverse engineering |
| IDA Pro | 🟡 Community | Community | LLM-controlled RE |
| Binary Ninja | 🟡 Community | Community | AI-driven RE |
| WinDBG | 🟡 Community | Community | Kernel debugging |
| YARA | 🟡 Community | FuzzingLabs | Pattern matching |
| CAPA | 🟡 Community | FuzzingLabs | Capability detection |
| Binwalk | 🟡 Community | Community | Firmware analysis |
| Gitleaks | 🟡 Community | Community | Secrets detection |
| Hashcat | 🟡 Community | Community | Hash cracking via NL |
| Metasploit | 🟡 Community | Community | Offensive framework |

---

## Cloud Security

| Tool | Status | Server / Repo | Notes |
|------|--------|--------------|-------|
| Wiz | ✅ Official | Wiz official | Posture management |
| Cloudflare | ✅ Official | Cloudflare official | WAF, edge, API protection |
| Cycode | ✅ Official | Cycode official | SAST, SCA, IaC scanning |
| AWS | ✅ Official | AWS official | Security tools & best practices |
| Azure | ✅ Official | Microsoft official | Storage, CLI, security |

---

## Network / Firewall

| Tool | Status | Server / Repo | Notes |
|------|--------|--------------|-------|
| Palo Alto PAN-OS | ✅ Official | Palo Alto official | FastMCP, policy query |
| Check Point Quantum | ✅ Official | Check Point official | Compliance, policy |
| Illumio | ✅ Official | Illumio official | Workload & traffic flow |

---

## DevSecOps

| Tool | Status | Server / Repo | Notes |
|------|--------|--------------|-------|
| GitHub | ✅ Official | `github/github-mcp-server` | Dependabot, secret scanning |
| Semgrep | ✅ Official | Semgrep official | Code security via AI agents |
| SonarQube | ✅ Official | Sonar official | Code analysis |
| Sentry | ✅ Official | Sentry official | Error tracking |
| Docker | ✅ Official | Docker Inc. official | Container management |

---

## Detection Engineering

| Tool | Status | Server / Repo | Notes |
|------|--------|--------------|-------|
| Sigma Rules | 🟡 Community | Community | Aggregates Sigma, Splunk ESCU, Elastic, KQL → SQLite |

---

## Bundled Collections

| Collection | Status | Notes |
|-----------|--------|-------|
| FuzzingLabs mcp-security-hub | 🟡 Community | 36 servers, 175+ tools, Docker Compose ready |
| cyproxio mcp-for-security | 🟡 Community | SQLMap, FFUF, Nmap, Masscan bundle |
| HexStrike AI | 🟡 Community | 150+ tools, 12 autonomous agents, offensive-focused |

> ⚠️ Bundled collections = larger attack surface. Review each server individually before use.
> See: [official-vs-community.md](../security-notes/official-vs-community.md)
