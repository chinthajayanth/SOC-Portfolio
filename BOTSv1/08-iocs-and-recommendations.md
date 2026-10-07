# 08 — IOCs & Recommendations

## Objective
Finalize the indicator list and pair each confirmed attack phase with a concrete,
actionable remediation. This file is the closing section of the Scenario 1
investigation — intended to be read on its own by someone who wants the
bottom line without reading every prior file.

---

## Final IOC List

| Type | Indicator | Role |
|---|---|---|
| IP | `40.80.148.42` | Reconnaissance / vulnerability scanning |
| IP | `23.22.63.114` | Brute force, backdoor C2, defacement delivery |
| Domain | `prankglassinebracket.jumpingcrab.com:1337` | Defacement file hosting (dynamic DNS) |
| Domain (unconfirmed) | `www.po1s0n1vy.com` | Actor-claimed, not observed internally |
| File | `poisonivy-is-coming-for-you-batman.jpeg` | Defacement image |
| File (suspected) | `agent.php` | Backdoor/web shell planted in `/joomla` |
| Account | `admin` (password `batman`) | Compromised Joomla CMS credential |
| Tool | Acunetix WVS 10.0 (Free Edition) | Scanning tool used by attacker |

*(Full detail and evidence sources: [`05-attacker-infrastructure.md`](05-attacker-infrastructure.md))*

---

## Recommendations by Phase

| Phase | Finding | Recommendation |
|---|---|---|
| Reconnaissance | Scanner traffic was seen by the firewall but allowed through (`03-exploitation.md`) | Configure IPS to **block**, not just alert, on known scanner signatures (e.g. Acunetix) at the perimeter |
| Exploitation | Unpatched LFI in Joomla `com_mailto` (`03-exploitation.md`) | Patch/update the Joomla installation and audit all third-party components for known CVEs; disable or remove unused components like `com_extplorer` |
| Credential Access | Admin account used a weak, dictionary-guessable password (`04-defacement-confirmation.md`) | Enforce strong password policy and MFA on all CMS admin accounts; add account lockout after N failed attempts |
| Persistence | Backdoor (`agent.php`) planted via file manager upload (`04-defacement-confirmation.md`) | Restrict file-manager/upload extensions from writing executable file types (`.php`, etc.) to web-root directories; integrity-monitor the web root for unexpected file changes |
| Command and Control | Beaconing disguised as search-engine referral traffic (`04-defacement-confirmation.md`) | Deploy outbound traffic inspection capable of detecting anomalous/forged referrer patterns, not just domain reputation |
| Policy Gap | Web filter allowed malicious traffic under an "allowed category" (`03-exploitation.md`, Step 5) | Pair category-based web filtering with signature-based IPS enforcement; review default "allow" categories for overly permissive rules |

---

## Impact Assessment

- **Confidentiality:** Low-to-moderate — LFI allowed arbitrary file read, but no evidence of broader data exfiltration was found in this investigation.
- **Integrity:** High — the public-facing website content was altered without authorization (confirmed defacement).
- **Availability:** No evidence of denial-of-service or destructive impact.
- **Reputational:** The defacement was public-facing and tied to a named threat actor group, aligning with that group's stated goal of victim embarrassment.

---

## Final Verdict

**Confirmed.** www.imreallynotbatman.com was compromised through a combination
of an unpatched Local File Inclusion vulnerability and a successful admin
credential brute-force attack, resulting in a confirmed public defacement.
Full technical evidence and chronology: [`06-timeline.md`](06-timeline.md).

---

## Report Index

- [`01-allegation-and-osint.md`](01-allegation-and-osint.md) — Allegation and OSINT review
- [`02-reconnaissance.md`](02-reconnaissance.md) — Scanning activity identified
- [`03-exploitation.md`](03-exploitation.md) — LFI vulnerability confirmed
- [`04-defacement-confirmation.md`](04-defacement-confirmation.md) — Brute force, backdoor, defacement proof
- [`05-attacker-infrastructure.md`](05-attacker-infrastructure.md) — Full IOC list
- [`06-timeline.md`](06-timeline.md) — Chronological attack narrative
- [`07-mitre-mapping.md`](07-mitre-mapping.md) — ATT&CK technique mapping
