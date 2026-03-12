# Use Cases — SOC Workflows via MCP

Each use case follows the same structure:
- **Skenario** — realistic SOC scenario
- **Tools** — which MCP servers are used
- **Config** — ready-to-use config per client
- **Prompt** — example prompt to trigger the workflow
- **Expected output** — what the LLM should return
- **Security notes** — risks and mitigations specific to this workflow

---

## Use Case Index

| # | Use Case | Difficulty | Tools |
|---|----------|-----------|-------|
| [01](01-ioc-enrichment/) | IOC Enrichment | 🟢 Beginner | VirusTotal, AbuseIPDB, MalwareBazaar |
| [02](02-malware-triage/) | Malware Triage | 🟢 Beginner | REMnux, VirusTotal |
| [03](03-threat-hunting-siem/) | Threat Hunting via SIEM | 🟡 Intermediate | Wazuh / IBM QRadar |
| [04](04-incident-case-creation/) | Incident Case Creation | 🟡 Intermediate | TheHive, Cortex |
| [05](05-osint-recon/) | OSINT Recon | 🟡 Intermediate | Shodan, Subfinder, Nmap |
| [06](06-detection-engineering/) | Detection Engineering | 🔴 Advanced | Sigma, Wazuh |
| [07](07-lateral-movement-detection/) | Lateral Movement Detection | 🔴 Advanced | BloodHound, Wazuh |
| [08](08-phishing-analysis/) | Phishing Analysis | 🟡 Intermediate | URLhaus, VirusTotal, TheHive |

---

## How to Use

1. Pick a use case relevant to your workflow
2. Follow the lab setup in [../Labs/](../Labs/)
3. Copy the config from `config/` to your MCP client
4. Use the provided prompt as a starting point
5. Adapt to your real environment

---

## Contribute a Use Case

Have a SOC workflow that works well with MCP? Open a PR with:
- New folder under `Usecases/`
- `README.md` following the existing structure
- Config files for at least one MCP client
- Security notes specific to the workflow
