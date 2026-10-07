# 05 — Attacker Infrastructure (IOC Summary)

## Objective
Consolidate every indicator of compromise identified during this investigation
into a single reference table, with the role each indicator played and the
evidence source it came from.

---

## IP Addresses

| IOC | Role | First Seen | Evidence Source |
|---|---|---|---|
| `40.80.148.42` | Reconnaissance / vulnerability scanning (Acunetix WVS) | 2016-08-10 21:36:45 | `02-reconnaissance.md`, `03-exploitation.md` |
| `23.22.63.114` | Brute-force authentication, defacement file host | 2016-08-10 21:45:08 | `03-exploitation.md`, `04-defacement-confirmation.md` |

## Domains

| IOC | Role | Notes |
|---|---|---|
| `prankglassinebracket.jumpingcrab.com` | Hosted the defacement image, port 1337 | Resolves to `23.22.63.114`; `jumpingcrab.com` is a free dynamic-DNS provider — disposable attacker infrastructure |
| `www.po1s0n1vy.com` | Actor-claimed domain (from Pastebin signature) | Never observed in internal telemetry (`01-allegation-and-osint.md`) — could not be used as a search pivot |

## Files

| IOC | Role | Notes |
|---|---|---|
| `poisonivy-is-coming-for-you-batman.jpeg` | Defacement image | Retrieved twice by the compromised web server itself; filename matches actor signature and Pastebin taunt message |

## Credentials

| IOC | Role | Notes |
|---|---|---|
| `admin` / `batman` | Compromised Joomla CMS account | Confirmed via frequency analysis (`04-defacement-confirmation.md`, Step 3), corrected from an initial timing-based guess |

## Tooling

| IOC | Role | Notes |
|---|---|---|
| Acunetix WVS 10.0 (Free Edition) | Reconnaissance/vulnerability scanning tool | Identified via `src_headers` in `stream:http` and independently via Fortinet IPS signature in `fgt_utm` |
| `Python-urllib/2.7` | Brute-force client | User-Agent on all `23.22.63.114` login POSTs |

## Vulnerability

| CVE/Class | Component | Parameter |
|---|---|---|
| Path Traversal → LFI | Joomla `com_mailto` | `tmpl` |

---

## Next Step
Proceed to `06-timeline.md` to assemble these indicators into a single
chronological narrative of the full attack.