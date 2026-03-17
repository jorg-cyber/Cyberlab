# IR-XXX — <Incident Title>

**Date/Time (UTC):** <DD-MM-YYYY HH:MM> to <HH:MM>  
**Severity:** <Low | Medium | High>  
**Status:** <Open | Monitoring | Contained | Closed>  
**Category:** <Recon/Probe | Exploit Attempt | Malware | Credential Attack | Lateral Movement | Other>

**Executive Summary:** <1-3 sentences covering what happened, what was targeted, and the bottom-line outcome.>

---

## 1) Trigger
What caused me to start investigating?
- **Source:** <Alert name / dashboard panel / manual observation>
- **Reason it looked suspicious:** <spike, signature, anomaly, etc.>

---

## 2) Evidence (Key Artifacts)
Bullet the *minimum* hard evidence I used.
- <Raw event example 1 — timestamp + indicator>
- <Raw event example 2 — timestamp + indicator>
- <Source-of-truth validation — e.g., endpoint/app/service log snippet>

(Screenshots/snippets will be attached at the bottom.)

---

## 3) Scope
What systems/entities are involved?
- **Primary target:** <host/service>
- **Other affected assets:** <if any>
- **Suspected source:** <src_ip/user/host>
- **Time window:** <first seen / last seen>
- **Volume:** <# events / burst pattern>

---

## 4) Triage Analysis
Answer these explicitly:
- **What was attempted?**  
  <one sentence>
- **Did it succeed? Why/why not?**  
  <blocked / possible / confirmed + supporting evidence>
- **Impact:**  
  <none / suspected / confirmed; what data/system was affected>

---

## 5) MITRE ATT&CK Mapping
- **Tactic:** <e.g. Discovery / Credential Access / Initial Access>
- **Technique:** <Technique ID - Technique Name>
- **Rationale:** <1-2 sentences explaining why the observed behaviour fits this technique based on the evidence above>

---

## 6) Actions Taken
What I actually did during the case (or would do in a real org).
- **Containment:** <none / block / isolate / stop service / reset creds>
- **Eradication/Hardening:** <config change / rule added / tuning>
- **Recovery:** <service restored / verified normal>

---

## 7) Lessons Learned / Improvements
What will I change so the lab gets better next time?
- <new detection / new dashboard panel / new log source / tuning / parsing fix>

---

## Evidence Attachments
- Screenshot 1: <Trigger view>
- Screenshot 2: <Expanded raw event>
- Screenshot 3: <Source-of-truth validation>
- (Optional) Log snippet: <copy/paste short excerpt>