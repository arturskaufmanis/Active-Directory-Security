# Active Directory Security Homelab Portfolio

**Arturs Kaufmanis** | Cybersecurity Professional | HyperionDev Bootcamp Graduate  
Manchester Metropolitan University · 2026

![Windows Server 2025](https://img.shields.io/badge/Windows_Server-2025-0078D6?style=flat&logo=windows)
![Splunk](https://img.shields.io/badge/Splunk-Enterprise_10.2-000000?style=flat&logo=splunk&logoColor=white)
![BloodHound](https://img.shields.io/badge/BloodHound-CE-red?style=flat)
![Sysmon](https://img.shields.io/badge/Sysmon-v15.15-blue?style=flat)
![Docker](https://img.shields.io/badge/Docker-WSL2-2496ED?style=flat&logo=docker&logoColor=white)

---
[ hyperlynk to full overview] (https://raw.githubusercontent.com/arturskaufmanis/Active-Directory-Security/refs/heads/main/AD_Security_Homelab_Portfolio.html)
## Overview

This portfolio documents the design, implementation, and security hardening of a comprehensive Active Directory homelab environment. The project demonstrates enterprise-level cybersecurity skills including SIEM integration, attack path analysis, identity management, and security monitoring.

---

## Lab Environment

| Component | Specification |
|---|---|
| Domain Controller | Windows Server 2025 (Build 26100) |
| Domain Name | ADForest.local |
| Forest/Domain Functional Level | Windows Server 2016 |
| DC Hostname | WIN-ESVD1CAD1FJ |
| RAM | 24GB |
| SIEM | Splunk Enterprise 10.2 (Free) |
| EDR | Sysmon v15.15 with SwiftOnSecurity config |
| Attack Path Tool | BloodHound Community Edition (Docker) |
| Container Platform | Docker Engine on WSL2 Ubuntu 24.04 |
| Security Assessment | PingCastle v3.x |

---

## PingCastle Score Progression

| Session | Global Score | Privileged | Stale Objects | Anomaly | Trust |
|---|---|---|---|---|---|
| Baseline | 60 | 40 | 31 | 60 | 0 |
| Session 1 – Password/NTLMv1 fixes | 50 | 0 | 21 | 50 | 0 |
| Session 2 – Backup/Audit fixes | 35 | 0 | 21 | 35 | 0 |
| Session 3 – LAPS/AES/SecAdmin | 21 | 20 | 21 | 20 | 0 |
| Final – Schema Admins/Backup | 20 | 20 | 20 | 20 | 0 |

> **67% improvement** from baseline score of 60 to final score of 20.

---

## Project Phases

### Phase 1 – Active Directory Deployment & Baseline
- Windows Server 2025 installed and configured as Domain Controller for `ADForest.local`
- Roles deployed: AD DS, DNS Server, Group Policy Management
- Baseline PingCastle assessment established (global score: 60)

### Phase 2 – Security Hardening
Key hardening steps applied:

- **Password Policy** – Minimum length increased from 7 to 12 characters
- **AD Recycle Bin** – Enabled; deleted objects recoverable within 180 days
- **MachineAccountQuota** – Set to 0; only admins can join computers to the domain
- **Schema Admins Cleanup** – Administrator removed from Schema Admins
- **Account Delegation Protection** – Administrator marked as not delegatable
- **NTLMv1 Disabled** – Legacy authentication protocol disabled via registry and GPO
- **Audit Policy** – Advanced audit policies configured on Domain Controllers
- **Hardened UNC Paths** – Configured via GPO to prevent path hijacking
- **AES Kerberos** – RC4 encryption deprecated; AES256/AES128 enforced
- **LAPS Deployment** – Local Administrator Password Solution deployed and verified

### Phase 3 – Splunk SIEM Integration
- Splunk Enterprise 10.2 deployed (Free licence)
- Sysmon v15.15 with SwiftOnSecurity config installed for endpoint telemetry
- Splunk Add-on for Sysmon configured with custom index `sysmon`
- Dashboards built for: Process Creation, Network Connections, Registry Changes, File Operations, Sysmon Health

### Phase 4 – BloodHound CE Attack Path Analysis
- WSL2 + Docker Engine deployed on Windows Server 2025
- BloodHound Community Edition deployed via `docker-compose`
- SharpHound collected 328 AD objects (users, groups, computers, GPOs, ACLs, trusts)
- Custom Cypher queries saved for DA membership, DCSync rights, Kerberoastable accounts, and shortest path to Domain Admin

---

## Saved BloodHound Queries

| Query Name | Purpose |
|---|---|
| Domain Admin Members | List all Domain Admin members |
| DCSync Rights Check | Find accounts with DCSync privileges |
| Kerberoastable Accounts | Find accounts with SPNs |
| Shortest Path to DA | Attack path visualisation |

---

## Challenges & Resolutions

| Challenge | Root Cause | Resolution |
|---|---|---|
| Splunk errorCode=5 for Sysmon | Service lacked permission to read Sysmon event log | Changed Splunk service to run as LocalSystem |
| Sysmon Add-on not loading | Triple-nested folder structure after extraction | Used robocopy to copy correct inner folder |
| Docker Desktop incompatible | Requires Windows 10/11 desktop OS | Installed WSL2 + Ubuntu + Docker Engine |
| BloodHound CLI 404 error | v8.7.0 has no Linux CLI binary | Used docker-compose (recommended approach) |
| Schema update access denied | Schema Admin membership needs new login session | Restarted Windows Server to refresh Kerberos token |
| PingCastle score increased | SecAdmin left in Schema Admins after LAPS update | Removed SecAdmin from Schema Admins |
| SharpHound blocked by Defender | Windows Defender flags SharpHound as security tool | Temporarily disabled real-time monitoring |

---

## Planned Enhancements

### TopTon Security Gateway (In Progress)
TopTon mini PC (Intel N150, 33GB DDR5) being configured as an inline security gateway:

| Interface | Role |
|---|---|
| ETH0 | WAN – Incoming internet cable |
| ETH1 | LAN – WiFi router (home network) |
| ETH2 | Homelab – Windows Server 2025 |
| ETH3 | Management/spare |

**Software Stack:** Rocky Linux 9 · Suricata (inline IPS) · OpenVPN

### Additional Planned Items
- BadBlood – Realistic AD test data population
- Network architecture diagram (draw.io)
- Wazuh SIEM – Open source alternative/complement to Splunk
- MITRE ATT&CK framework mapping
- Incident response playbook
- Attack simulation and detection validation

---

## Skills Demonstrated

| Skill Area | Tools / Technologies |
|---|---|
| Active Directory Administration | Windows Server 2025, PowerShell, ADUC |
| Security Assessment | PingCastle, BloodHound CE, SharpHound |
| SIEM Administration | Splunk Enterprise, SPL queries, Dashboards |
| Endpoint Detection | Sysmon, Splunk Add-on for Sysmon |
| Identity Management | LAPS, GPO, Privileged Account Management |
| Containerisation | Docker Engine, WSL2, docker-compose |
| Linux Administration | Ubuntu, Rocky Linux, bash |
| Network Security | Suricata, TAP monitoring |
| Scripting | PowerShell, Bash |
| Documentation | GitHub, Portfolio writing |

---

## Repository Structure

```
ad-security-homelab/
├── README.md                        # This file
├── portfolio/
│   └── AD_Security_Homelab_Portfolio.docx
├── splunk/
│   └── dashboards/                  # Exported Splunk dashboard XMLs
├── bloodhound/
│   └── queries/                     # Saved Cypher queries
├── sysmon/
│   └── sysmonconfig.xml             # SwiftOnSecurity Sysmon config
├── scripts/
│   └── hardening/                   # PowerShell hardening scripts
└── docs/
    └── network-diagram.drawio       # Network architecture (planned)
```

---

*Arturs Kaufmanis | 2026 | Document generated for GitHub Portfolio*
