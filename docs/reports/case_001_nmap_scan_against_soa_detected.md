# CASE-001 — Nmap Port Scan Against SOA Detected (Suricata + UFW corroboration)

**Date:** 02-02-2026  
**Environment:** Cyberlab  
**Target:** `192.168.68.112` (SOA)  
**Sources observed:** `192.168.68.123` (MacBook), `192.168.68.117` (Kali VM)

---

## 1) Summary
Two hosts in the local network performed Nmap-style port scanning against SOA (192.168.68.112). Suricata generated multiple **ET SCAN** alerts consistent with service probing across several ports within short time windows. UFW telemetry separately shows high-volume blocked multi-port probing from the same scanning source(s) backing up the recon picture. This was expected in Cyberlab because the scans were intentional but it confirms the detections and pipeline are working end-to-end.

---

## 2) What triggered investigation
- **Matched detection:** DET-007 Suricata — Nmap/Port Scan (ET SCAN by unique ports)
- **Corroborating detection:** DET-006 SOA UFW Port Sweep / Multi-Port Probe (Blocks)
- **Alert created:** ALERT-002 Suricata Nmap Scan Detected (scheduled)

---

## 3) Evidence summary (high-signal fields)

### 3.1 Suricata IDS evidence (ET SCAN)
Observed patterns consistent with port/service probing:

- **dest_ip:** 192.168.68.112  
- **src_ip:** 192.168.68.123 and 192.168.68.117  
- **Example signatures observed:**
    - ET SCAN Potential VNC Scan 5800-5820
    - ET SCAN Suspicious inbound to MSSQL port 1433
    - ET SCAN Suspicious inbound to Oracle SQL port 1521
    - ET SCAN Suspicious inbound to PostgreSQL port 5432
    - ET SCAN Suspicious inbound to mySQL port 3306

**Evidence (DET-007):** Short bursts of ET SCAN alerts with multiple destination ports probed in a 5-minute window, consistent with port scanning.

### 3.2 UFW firewall corroboration (blocks)
UFW detection output indicates broad, repeated blocked probing behaviour consistent with scanning:

- **SRC:** 192.168.68.123 and 192.168.68.117
- **DST:** 192.168.68.112 
- **unique_dest_ports:** high (multi-port sweep pattern)
- Confirms that, beyond IDS signatures, the host firewall observed and blocked large volumes of probing attempts.

---

## 4) Timeline (approx, based on 5-minute Splunk bins)
> Note: Times are approximate because DET-007 bins activity into 5-minute windows.

- **15:05–16:55 UTC (approx):** Scan activity observed across multiple 5-minute bins against 192.168.68.112 (SOA) from 192.168.68.123 (MacBook), consistent with Nmap probing (multiple ET SCAN clusters over time).
- **17:55–18:05 UTC (approx):** Additional scan burst against 192.168.68.112 (SOA) from 192.168.68.117 (Kali) producing ET SCAN alerts (multiple clusters across consecutive bins).

---

## 5) Triage notes (what I checked)
- **Data source:** Suricata alert telemetry corroborated by UFW blocks.
- **What happened:** Nmap-style service probing and port-scan behaviour observed (ET SCAN signatures).
- **Who did it (source):** `192.168.68.123` and `192.168.68.117`.
- **What was targeted:** SOA only in this dataset.
- **How loud / pattern:** Burst-style probing across multiple ports within short windows (consistent with recon, not exploitation).
- **Corroboration:** UFW telemetry shows blocked multi-port probing consistent with the same recon activity.
- **Risk call (lab):** Low–Medium. Recon is always worth flagging even without follow-on activity. No follow-on exploitation indicators were assessed in this case.

---

## 6) MITRE ATT&CK mapping
- **Tactic:** Discovery
- **Technique:** [T1046 - Network Service Discovery](https://attack.mitre.org/techniques/T1046/)
- **Rationale:** Suricata ET SCAN alerts and UFW telemetry both showed multi-port probing against `192.168.68.112`, consistent with Nmap-style network service discovery rather than exploitation.

---

## 7) Response (lab note + real-org playbook)
**Lab note:** Authorized test (I initiated the scan). No containment/remediation applied.

**If this was *not* authorized in a real org:**

First thing I'd do is slow or stop the scanning — block or rate-limit the source at whatever I control closest to it (host firewall, network firewall, NAC). If the machine looks compromised, isolate it (EDR containment or VLAN quarantine) so it can't keep probing.

Then I'd figure out what I'm dealing with. If the source is internal (LAN/VPN/NAC user), I'd pivot from the `src_ip` to endpoint evidence: Who was logged in, what process kicked off the scan, shell history, scheduled tasks. If it's external, I'd go the other way: Look at what services and ports were targeted, check auth logs and EDR for any follow-on activity after the scan, and enrich the `src_ip` (reputation/geo/ASN) to decide if this is internet noise or someone with intent.

I'd also want to check whether the same scan pattern shows up against other internal hosts (similar ET SCAN signatures or firewall multi-port blocks) to figure out if this was targeted at one box or if someone was sweeping the network.

Longer term, if it wasn't legit, I'd look at how they got access in the first place (Wi-Fi, VPN, exposed services) and whether segmentation is tight enough that a user on the network can't just freely scan the server VLAN.

---

## 8) Related artifacts
- **DET-007:** [DET-007 Suricata — Nmap/Port Scan (ET SCAN by unique ports)](../../docs/detections/det_007_suricata_port_scan.md)
- **DET-006:** [DET-006 SOA UFW - Port Sweep / Multi-Port Probe(Blocks)](../../docs/detections/det_006_ufw_port_sweep_blocks.md)
- **ALERT-002:** [ALERT-002 ET SCAN Recon Activity Detected (Port Scan/Probing)](../../docs/alerts/alert_002_port_scan_activity.md)