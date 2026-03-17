# IR-003 — SSH Brute Force Attempt Detected (Linux Auth Logs) – SOA

**Date/Time (UTC):** 22-02-2026 15:10 to 15:15  
**Severity:** Medium 
**Status:** Closed
**Category:** Credential Attack

**Executive Summary:** An SSH brute-force attempt from 192.168.68.117 targeted SOA (192.168.68.112:22), generating 384 failed login attempts in five minutes. No successful access or impact was observed.

---

## 1) Trigger
- **Source:** ALERT-004 Possible SSH Brute Force (Linux Hosts)
- **Reason it looked suspicious:** spike in failed login attempts

---

## 2) Evidence (Key Artifacts)
- 2026-02-22T15:14:45.748675+00:00 SOA sshd[902807]: Failed password for root from 192.168.68.117 port 42100 ssh2
- 2026-02-22T15:14:45.748096+00:00 SOA sshd[902808]: Failed password for root from 192.168.68.117 port 42114 ssh2
- 22-02-2026 15:10 to 15:15 - 384 failed login attempts (source: /var/log/auth.log) triggering ALERT-004 Possible SSH Brute Force (Linux Hosts)

---

## 3) Scope
- **Primary target:** SOA `192.168.68.112` SSH port 22
- **Other affected assets:** none observed
- **Suspected source:** `192.168.68.117`
- **Time window:** 15:10 / 15:15
- **Volume:** 384 failed ssh login attempts

---

## 4) Triage Analysis
- **What was attempted?**  
  SSH brute force login attempt at SOA 192.168.68.112 port 22. (High volume attempts)
- **Did it succeed? Why/why not?**  
  Failed. No successful SSH sessions opened from 15:10 and 15:30
- **Impact:**  
  None observed

---

## 5) MITRE ATT&CK mapping
- **Tactic:** Credential Access
- **Technique:** [T1110.001 - Brute Force: Password Guessing](https://attack.mitre.org/techniques/T1110/001/)
- **Rationale:** The activity consisted of 384 failed SSH login attempts against the `root` account on SOA from a single source IP within a 5-minute window. This is consistent with automated password guessing rather than normal administrative use and no successful SSH session was observed.

---

## 6) Actions Taken
*What I actually did during the case (or would do in a real org).*
- **Containment:** None (In a real org: temporarily block `192.168.68.117`)
- **Eradication/Hardening:** (In a real org: enforce key-only auth)
- **Recovery:** Not required

---

## 7) Lessons Learned / Improvements
- Learned that Splunk Fast Mode hides manual field extractions, so SSH triage should be done in Smart Mode.

---

## Evidence Attachments
![Alert Trigger](/assets/ir_003_image_01.png)


![Raw Events](/assets/ir_003_image_02.png)


![Proof of no succesful logins](/assets/ir_003_image_03.png)


----

## Exploit Screenshot

![Hydra SSH bruteforce](/assets/ir_003_image_04.png)
