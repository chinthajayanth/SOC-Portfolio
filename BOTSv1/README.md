# Scenario 1 — Website Defacement Investigation
### www.imreallynotbatman.com | Wayne Enterprises SOC

## Summary

GCPD identified a public Pastebin post claiming that www.imreallynotbatman.com
had been compromised and defaced by a group signing as "po1s0n1vy." This
investigation independently verified the claim using only internal Splunk
telemetry (BOTSv1 dataset) — reconstructing the full attack chain from initial
reconnaissance through confirmed defacement, without relying on the CTF's
published question set.

**Verdict: Confirmed.** The site was compromised via an unpatched Joomla LFI
vulnerability combined with a successful admin credential brute-force attack,
resulting in a public defacement tied directly to the alleged actor's signature.

---

## Key Findings

| Finding | Detail |
|---|---|
| Vulnerability exploited | Local File Inclusion — Joomla `com_mailto`, `tmpl` parameter |
| Reconnaissance tool | Acunetix WVS 10.0 (Free Edition) |
| Scanning IP | `40.80.148.42` |
| Brute-force / defacement IP | `23.22.63.114` |
| Compromised credential | `admin` / `batman` |
| Backdoor planted | `agent.php` (suspected web shell) |
| Defacement file | `poisonivy-is-coming-for-you-batman.jpeg` |
| Delivery infrastructure | `prankglassinebracket.jumpingcrab.com:1337` |
| Attack window | 2016-08-10, 21:36 – 22:21 UTC (~45 minutes) |

---

## Investigation Report

| # | File | Contents |
|---|---|---|
| 01 | [Allegation & OSINT](01-allegation-and-osint.md) | Pastebin claim, actor handle, scope definition |
| 02 | [Reconnaissance](02-reconnaissance.md) | Scanning IP, path enumeration, spoofed user-agent |
| 03 | [Exploitation](03-exploitation.md) | LFI confirmation, Acunetix attribution, firewall corroboration |
| 04 | [Defacement Confirmation](04-defacement-confirmation.md) | Brute force success, password correction, defacement file proof |
| 05 | [Attacker Infrastructure](05-attacker-infrastructure.md) | Full IOC table (IPs, domain, file, credential, tooling) |
| 06 | [Timeline](06-timeline.md) | Full chronological attack narrative |
| 07 | [MITRE ATT&CK Mapping](07-mitre-mapping.md) | Technique IDs per attack phase |
| 08 | [IOCs & Recommendations](08-iocs-and-recommendations.md) | Final IOC list, remediation, impact assessment |

---

## Methodology

This investigation was conducted as an independent analysis, not by following
the BOTSv1 CTF's published question list. Each finding was derived from raw
Splunk `stream:http` and `fgt_utm` evidence, cross-source corroborated where
possible (e.g., Acunetix detection confirmed independently via both web logs
and Fortinet IPS), and one earlier finding (the brute-forced password) was
explicitly corrected mid-investigation using a more rigorous method — documented
transparently in [`04-defacement-confirmation.md`](04-defacement-confirmation.md)
rather than silently fixed.

**Tools used:** Splunk (BOTSv1 dataset), SPL
**Dataset note:** All credentials, IP addresses, and file names in this report
originate from Splunk's publicly released BOTSv1 training dataset (2016) and
refer to no real individual, organization, or live infrastructure.

---

## Screenshots

All supporting screenshots referenced throughout this report are in
[`/screenshots`](screenshots/).
