# 07 — MITRE ATT&CK Mapping

## Objective
Classify each confirmed phase of this attack against MITRE ATT&CK techniques,
with the specific evidence supporting each classification.

---

## Technique Mapping

| Phase | Tactic | Technique ID | Technique Name | Evidence |
|---|---|---|---|---|
| Reconnaissance | Reconnaissance | **T1595.002** | Active Scanning: Vulnerability Scanning | Acunetix WVS scan from `40.80.148.42`, confirmed via `src_headers` and independently via Fortinet IPS (`02`, `03`) |
| Exploitation | Initial Access | **T1190** | Exploit Public-Facing Application | LFI vulnerability in Joomla `com_mailto` `tmpl` parameter, confirmed with `win.ini`/`boot.ini` proofs-of-concept (`03`) |
| Credential Access | Credential Access | **T1110.001** | Brute Force: Password Guessing | 412 sequential password attempts against the `admin` account from `23.22.63.114` (`03`, `04`) |
| Valid Account Use | Initial Access / Persistence | **T1078** | Valid Accounts | Authenticated Joomla admin session confirmed via direct dashboard HTML evidence (`04`) |
| Tool Transfer | Command and Control | **T1105** | Ingress Tool Transfer | File upload action via `com_extplorer` file manager to `/joomla`, consistent with planting `agent.php` (`03`) |
| Backdoor Placement | Persistence | **T1505.003** | Server Software Component: Web Shell | `agent.php` — non-standard PHP file in web root, accessed repeatedly post-compromise (`04`) |
| C2 Beaconing | Command and Control | **T1071.001** | Application Layer Protocol: Web Protocols | 194 beacon requests to `agent.php` disguised with forged Google/Translate referrer headers (`04`) |
| Defacement | Impact | **T1491.002** | Defacement: External Defacement | `poisonivy-is-coming-for-you-batman.jpeg` retrieved from attacker dynamic-DNS infrastructure and displayed on the compromised site (`04`) |

---

## Attack Chain Visualized (Tactic Order)
Reconnaissance → Initial Access → Credential Access → Persistence → Command and Control → Impact
T1595.002 T1190 T1110.001 T1505.003 T1071.001 T1491.002
T1078 T1105


## Notes on Classification Confidence

- **High confidence** (direct evidence): T1595.002, T1190, T1110.001, T1078, T1491.002 — all backed by raw request/response content, not inference.
- **Moderate confidence** (strong circumstantial evidence): T1105, T1505.003, T1071.001 — the file upload action and backdoor beaconing pattern are well-supported, but the exact file written by the `com_extplorer` upload was not directly captured in these logs; `agent.php`'s appearance and behavior afterward constitute the supporting evidence.

## Next Step
Proceed to `08-iocs-and-recommendations.md` to finalize the IOC list (already
drafted in `05-attacker-infrastructure.md`) alongside concrete remediation
recommendations for each phase above.