  # CASE-002 — Web Scanner Activity Against Juice Shop Detected (Suricata + UFW corroboration)

**Date:** 04-02-2026   
**Environment:** Cyberlab  
**Target:** 192.168.68.112 (SOA)  
**Service targeted:** OWASP Juice Shop (Docker) on TCP/3000  
**Source observed:** 192.168.68.117 (Kali VM)

---

## 1) Summary
A burst of web-scanner-style activity was observed from the Kali VM (192.168.68.117) against OWASP Juice Shop running on SOA (192.168.68.112:3000). Suricata generated multiple web-related alert signatures (ET WEB_SERVER / ET HUNTING) in short time windows, consistent with automated probing and exploit-pattern testing. UFW telemetry corroborated repeated blocked connection attempts to the same target service port (TCP/3000). This was expected in Cyberlab because the scan was intentional (OWASP ZAP), but it confirms the detection logic and telemetry pipeline are working end-to-end.

---

## 2) What triggered investigation
- **Matched detection:** DET-008 Suricata — Web Attack Signature Burst (ET WEB / HUNTING)
- **Supporting telemetry:** SOA UFW blocks against TCP/3000 during the same activity window (DET-005 context)

---

## 3) Evidence summary (high-signal fields)

### 3.1 Suricata IDS evidence (web attack signature burst)
Observed Suricata alert bursts associated with web probing/exploitation patterns:

- **dest_ip:** 192.168.68.112  
- **src_ip:** 192.168.68.117  
- **app_proto:** http  
- **Example signatures observed (burst context):**
  - ET HUNTING Suspicious PHP Code in HTTP POST (Inbound)
  - ET HUNTING Suspicious PHP Code in HTTP POST (Outbound)
  - ET WEB_SERVER Generic PHP Remote File Include
  - ET WEB_SERVER PHP tags in HTTP POST
  - ET WEB_SERVER PHP.//Input in HTTP POST
  - ET WEB_SERVER Possible SQL Injection (exec) in HTTP Request Body
  - ET WEB_SERVER allow_url_include PHP config option in uri
  - ET WEB_SERVER auto_prepend_file PHP config option in uri

**Evidence (DET-008):** High-volume alert bursts and multiple distinct signatures within short 5-minute windows, consistent with automated web scanner behaviour rather than normal browsing.

### 3.2 UFW firewall corroboration (blocks)
UFW telemetry corroborated repeated probing/connection attempts from the Kali VM toward the Juice Shop service on SOA.

- **SRC:** 192.168.68.117 (Kali)
- **DST:** 192.168.68.112 (SOA)
- **proto:** TCP
- **dest_port:** 3000
- **unique_dest_ports:** 1
- **blocks:** 77
- **src_ports:** multiple ephemeral ports observed (repeated connection attempts)

**Interpretation:** Host firewall logs confirm repeated connection/probe attempts against SOA:3000 during the lab activity window. This corroborates Suricata’s web-signature bursts with an independent data source.

---

## 4) Timeline (approx, based on 5-minute Splunk bins)
> Note: Times are approximate because DET-008 bins activity into 5-minute windows.

- **~13:30–15:50 UTC (approx):** Multiple 5-minute bins show Suricata web-signature bursts associated with automated probing against 192.168.68.112.  
- **Within the same general window:** UFW recorded repeated blocked connections to TCP/3000 from 192.168.68.117.

---

## 5) Triage notes (what I checked)
- **Data source:** Suricata alert telemetry corroborated by UFW blocks.
- **What happened:** Web-scanner-style probing against a web app service (Juice Shop) — payload patterns included SQLi, RFI, and PHP injection.
- **Who did it (source):** 192.168.68.117 (Kali VM).
- **What was targeted:** SOA (192.168.68.112), specifically the web app service on TCP/3000.
- **How loud / pattern:** Burst-style alert clusters with multiple distinct web signatures (scan/probe/exploit-pattern behavior).
- **Corroboration:** UFW telemetry shows repeated blocks toward the same service port during the same window.
- **Risk call (lab):** Low–Medium. The behaviour is suspicious by nature (it looks like exploitation attempts), but it was authorized lab testing. No follow-on host compromise indicators were assessed in this case.

---

## 6) MITRE ATT&CK mapping
- **Tactic:** Reconnaissance
- **Technique:** [T1595.002 - Active Scanning: Vulnerability Scanning](https://attack.mitre.org/techniques/T1595/002/)
- **Rationale:** Suricata showed repeated web attack signature bursts against OWASP Juice Shop on TCP/3000, including SQL injection, remote file inclusion, and PHP-related payload patterns, consistent with automated vulnerability scanning rather than normal browsing.

---

## 7) Response (lab note + real-org playbook)
**Lab note:** Authorized test (I initiated the scan). No containment/remediation applied.

**If this was *not* authorized in a real org:**

If I saw this for real, the first thing I'd do is block or rate-limit the source — at the WAF if there is one, otherwise the host firewall or network firewall. If it's an internal machine, that's more concerning and I'd want it isolated (EDR containment or VLAN quarantine) while I figure out what happened.

Next I'd check whether the same source hit any other web services in the environment — same Suricata signature types (ET WEB_SERVER / ET HUNTING), same time window. That tells me if this was targeted at one app or broader.

Then I'd pivot into follow-on activity: did anything actually land? That means checking web server and app logs for errors or suspicious endpoint hits, looking at the target host for unexpected process creation or file writes, and checking for any outbound connections that started after the burst.

Longer term, I'd want to understand why this source could reach the web service at all — is segmentation right, is the app exposed to more of the network than it needs to be.

---

## 8) Related artifacts
- **DET-008:** [DET-008 Suricata — Web Attack Signature Burst (ET WEB / HUNTING)](../../docs/detections/det_008_suricata_web_attack)
- **DET-005:** [DET-005 SOA UFW - Top Blocked Sources (Ports/Proto)](../../docs/detections/det_005_ufw_top_blocked_sources.md)