# 🧪 Cyberlab

![Cyberlab Banner](assets/cyberlab-banner.png)

A physically networked SOC-style home lab built to simulate real analyst workflows across multiple endpoints. The lab uses Splunk Enterprise as the central SIEM, with Suricata IDS, Sysmon, UFW, and supporting services to collect telemetry, build detections, generate alerts, and produce incident-style documentation mapped to MITRE ATT&CK.

---

## 📚 Table of Contents
- [📌 Key outcomes](#-key-outcomes)
- [🛠️ What this lab demonstrates](#️-what-this-lab-demonstrates)
- [🖥️ Current environment](#️-current-environment)
- [🗺️ Network overview](#️-network-overview)
- [📚 Detections, alerts, and reports](#-detections-alerts-and-reports)
- [🛠️ Documentation and setup guides](#️-documentation-and-setup-guides)
- [🗺️ Roadmap](#️-roadmap)
- [📓 Progress Log](#-progress-log)
- [📁 Repository Structure](#-repository-structure)

---

## 📌 Key outcomes

- 8 documented detections
- 4 documented alerts
- 5 incident reports / case studies
- 1 documented vulnerability finding and remediation
- 4 Splunk dashboard write-ups
- 10 component setup guides covering Splunk, Suricata, Sysmon, Docker, UFW, and supporting services

---

## 🛠️ What this lab demonstrates

- Splunk Enterprise: log ingestion, SPL searches, dashboards, and alerting
- Multi-endpoint telemetry across Ubuntu, Windows, and macOS
- Windows endpoint visibility using Sysmon
- Network monitoring and alerting with Suricata IDS
- Host firewall telemetry and investigation using UFW
- Detection engineering and alert validation using controlled test activity
- MITRE ATT&CK-aligned documentation and investigation workflow
- Controlled attack simulation using tools such as Nmap, Hydra, Nikto, Nessus Essentials, and OWASP Juice Shop

---

## 🖥️ Current environment

### Primary SOC Server — Son-of-Anton
**Platform:** Ubuntu 24.04 LTS  
**Role:** Central SOC server, telemetry hub, and primary monitored target

**Runs:**
- Splunk Enterprise (Indexer; receives telemetry on TCP 9997)
- Suricata IDS
- UFW
- Docker containers:
  - Bitcoin pruned node
  - MariaDB
  - NGINX victim on `:8080`
  - OWASP Juice Shop on `:3000`

### Multi-OS Lab Workstation — Cyberlab
**Platform:** Lenovo ThinkPad T480 (Ubuntu 24.04 LTS / Windows 10 Pro dual boot)  
**Role:** Multi-purpose lab workstation and branch-office simulation host

**Ubuntu side:**
- VirtualBox Kali VM
- Splunk Universal Forwarder
- Branch-office simulation:
  - NGINX telemetry
  - SMB share activity
  - Heartbeat cron activity
  - Forwarding to `index=branch_office`

**Windows side:**
- VMware Windows 10 sandbox with Sysmon (Olaf Hartong config)
- Splunk Universal Forwarder

Related docs:
- [NGINX setup](setup/nginx-setup.md)
- [SMB / Samba setup](setup/smb-samba-setup.md)

### SOC Analyst Console
**Platform:** MacBook Pro  
**Role:** Splunk Web access, SSH administration, and analyst management workstation

---

## 🗺️ Network overview

- Virgin Media Hub in modem-only mode
- TP-Link Deco mesh system acting as main router and access point
- IoT devices isolated on a dedicated SSID
- Static IPs / DHCP reservations assigned to core lab devices
- Additional networking notes: [Networking Fundamentals](docs/networking_fundamentals.md)

![Network Topology](diagrams/cyberlab-network-diagram.png)

---

## 📚 Detections, alerts, and reports

### Detections
- [DET-001 Encoded Powershell (Sysmon EID 1)](docs/detections/det_001_encoded_powershell.md)
- [DET-002 Suspicious Powershell Download/Exec (Sysmon EID 1)](docs/detections/det_002_suspicious_powershell_download.md)
- [DET-003 BITSAdmin Transfer (Sysmon EID 1)](docs/detections/det_003_bitsadmin_transfer.md)
- [DET-004 CertUtil Suspicious Usage (Sysmon EID 1)](docs/detections/det_004_certutil_suspicious_usage.md)
- [DET-005 SOA UFW - Top Blocked Sources (Ports/Proto)](docs/detections/det_005_ufw_top_blocked_sources.md)
- [DET-006 SOA UFW - Port Sweep / Multi-Port Probe (Blocks)](docs/detections/det_006_ufw_port_sweep_blocks.md)
- [DET-007 Suricata — Nmap/Port Scan (ET SCAN by unique ports)](docs/detections/det_007_suricata_port_scan.md)
- [DET-008 Suricata — Web Attack Signature Burst (ET WEB / HUNTING)](docs/detections/det_008_suricata_web_attack)

### Alerts
- [ALERT-001 Encoded Powershell (Sysmon EID 1)](docs/alerts/alert_001_encoded_powershell.md)
- [ALERT-002 ET SCAN Recon Activity Detected (Port Scan/Probing)](docs/alerts/alert_002_port_scan_activity.md)
- [ALERT-003 Juice Shop — Possible Brute Force (≥10 login requests / 5m)](docs/alerts/alert_003_possible_brute_force.md)
- [ALERT-004 Possible SSH Brute Force (Linux Hosts) (≥5 failed logins / 5m)](docs/alerts/alert_004_possible_ssh_brute_force_linux_hosts.md)

### Incident reports / case studies / remediation
- [IR-001 Brute Force Attempt Against OWASP Juice Shop Login (Hydra) — Detected via Splunk + Suricata](docs/reports/ir_001_brute_force_attempt_juice_shop.md)
- [IR-002 — Suricata Web Alert Burst – Juice Shop (SOA:3000)](docs/reports/ir_002_suricata_web_alert_burst.md)
- [IR-003 — SSH Brute Force Attempt Detected (Linux Auth Logs)](docs/reports/ir_003_ssh_brute_force_attempt.md)
- [CASE-001 Nmap Port Scan Against SOA Detected (Suricata + UFW corroboration)](docs/reports/case_001_nmap_scan_against_soa_detected.md)
- [CASE-002 Web Scanner Activity Against Juice Shop Detected (Suricata + UFW corroboration)](docs/reports/case_002_web_scanner_activity.md)
- [VULN-001 Nessus Finding: Splunk Information Disclosure Vulnerability (SP-CAAAP5E) (Fixed)](docs/reports/vuln_001_nessus_finding_splunk_info_disclosure.md)

### Supporting analyst documentation
- [Cyberlab Incident Response Runbook](docs/ir_runbook.md)
- [Incident Report Template](docs/ir_report_template.md)

---

## 🛠️ Documentation and setup guides

### Splunk dashboards
- [Windows Sysmon Dashboard](docs/splunk_dashboards/windows_sysmon_dashboard.md)
- [Suricata IDS Dashboard](docs/splunk_dashboards/suricata_ids_dashboard.md)
- [Branch Office Telemetry Dashboard](docs/splunk_dashboards/splunk_branch_office_dashboard.md)
- [Endpoint Activity Dashboard](docs/splunk_dashboards/endpoint_activity_dashboard.md)

### Findings
- [Finding-001 UFW vs IPtables](docs/findings/finding_001_ufw_vs_iptables.md) — Docker-published ports can be reachable even if they do not appear in `ufw status`
- [Finding-002 Suricata default HOME_NET/EXTERNAL_NET settings suppress internal scan alerts in a lab](docs/findings/finding_002_suricata_settings_suppress_internal_scan_alerts.md)

### Setup guides
- [Docker setup](setup/docker-setup.md)
- [MariaDB setup](setup/mariadb-setup.md)
- [Splunk Enterprise setup](setup/splunk-enterprise-setup.md)
- [Splunk Universal Forwarder setup](setup/splunk-universal-forwarder-setup.md)
- [NGINX setup](setup/nginx-setup.md)
- [SMB / Samba setup](setup/smb-samba-setup.md)
- [Branch heartbeat setup](setup/branch-heartbeat-setup.md)
- [Suricata IDS setup](setup/suricata-ids-setup.md)
- [Sysmon setup](setup/windows-sysmon-to-splunk-setup.md)
- [UFW host firewall setup](setup/ufw-setup.md)

### Networking
- [Networking fundamentals](docs/networking_fundamentals.md)

---

## 🗺️ Roadmap

### Current focus
- Integrate a Windows 11 endpoint into the Cyberlab using Microsoft Entra / Intune workflows, with endpoint telemetry visible in Splunk
- Convert existing detections into Sigma rules to make detection logic more portable and tool-agnostic
- Generate a MITRE ATT&CK Navigator coverage layer to visualise current detection coverage
- Add 2–3 small detections inspired by MD-102 study, especially around Windows endpoint and administration activity
- Build one cross-source correlation detection using existing telemetry sources
- Add DNS logging incrementally as the next new telemetry source

### Next steps
- Expand DNS-based detections and dashboards once DNS logging is in place
- Extend cross-source correlation beyond a single detection into broader analyst workflows
- Add more alert → triage → quick report case workflows
- Expand network-focused detections beyond simple scan signatures
- Optionally compare with a second SIEM later, such as Microsoft Sentinel

---

## 📓 Progress Log

For a complete history of changes, updates, and development work, see:  
➡️ [progress-log.md](progress-log.md)

---

## 📁 Repository Structure

- `assets/` — banners and screenshots
- `diagrams/` — network and topology diagrams
- `docs/` — documentation, detections, alerts, reports, findings, dashboards, templates
  - `detections/` — detection write-ups / saved Splunk searches
  - `alerts/` — alert write-ups
  - `reports/` — incidents, case studies, and remediations
  - `findings/` — lab findings, root causes, and fixes
  - `splunk_dashboards/` — dashboard write-ups and SPL breakdowns
- `archive_scripts/` — archived / retired scripts
- `setup/` — install and configuration notes per component
- [📓 Progress Log](progress-log.md) — running diary of lab development