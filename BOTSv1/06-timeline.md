# 06 — Timeline of Events

## Objective
Assemble every confirmed event from `01` through `05` into a single chronological
sequence, showing how reconnaissance, exploitation, credential compromise, and
defacement fit together as one attack.

---

## Full Timeline — August 10, 2016

| Time (UTC) | Actor | Event | Source |
|---|---|---|---|
| 21:36:45 | 40.80.148.42 | Acunetix WVS scan begins against imreallynotbatman.com | [02-reconnaissance.md](02-reconnaissance.md) |
| 21:36:45 | 40.80.148.42 | Fortinet IPS independently detects Acunetix scanner | [03-exploitation.md](03-exploitation.md), Step 5 |
| 21:45:08–21:46:39 | 23.22.63.114 | Brute-force login attempts begin against `/joomla/administrator/index.php` (admin account, `Python-urllib/2.7`) | [03-exploitation.md](03-exploitation.md) |
| **21:46:40.781** | **23.22.63.114** | **Brute force succeeds — password `batman` — authenticated admin dashboard HTML returned** | [04-defacement-confirmation.md](04-defacement-confirmation.md),    Step 2 |
| 21:48:05–21:48:11 | 40.80.148.42 | Routine Joomla/update.joomla.org traffic observed (benign, ruled out) | — |
| 21:51:32–21:52:48 | 40.80.148.42 | LFI confirmed via `com_mailto` `tmpl` parameter (win.ini, boot.ini); `com_extplorer` file manager browsed; upload action issued to `/joomla` | [03-exploitation.md](03-exploitation.md) |
| 21:55:22 | 23.22.63.114 | First beacon to `/joomla/agent.php` (planted backdoor) | [04-defacement-confirmation.md](04-defacement-confirmation.md) (C2 finding) |
| 21:55:22–22:21:34 | 23.22.63.114 | 194 beacon requests to `agent.php` over ~26 minutes, disguised with forged Google/Translate referrer headers | — |
| **22:06:21.569** | **23.22.63.114 / imreallynotbatman.com server** | **Web server retrieves `poisonivy-is-coming-for-you-batman.jpeg` from `prankglassinebracket.jumpingcrab.com:1337`** | [04-defacement-confirmation.md](04-defacement-confirmation.md),    Step 4 |
| 22:13:46.915 | 23.22.63.114 / imreallynotbatman.com server | Defacement image re-retrieved (retry/re-display) | [04-defacement-confirmation.md](04-defacement-confirmation.md),   Step 4 |

## Related — External Timeline

| Date | Event |
|---|---|
| 2016-08-10 | Compromise and defacement occur (per above) |
| 2016-09-08 | Pastebin post discovered by GCPD, prompting this investigation | [01-allegation-and-osint.md](01-allegation-and-osint.md)

---

## Narrative Summary

Two coordinated threads converge on the same target within a roughly 45-minute
window on August 10, 2016:

1. **`40.80.148.42`** ran an automated Acunetix vulnerability scan, which both
   enumerated the Joomla site and confirmed a Local File Inclusion vulnerability
   in the `com_mailto` component's `tmpl` parameter. It then used the
   `com_extplorer` file manager to browse the server and issue a file upload —
   most likely planting the `agent.php` backdoor found later.
2. **`23.22.63.114`**, operating in parallel, brute-forced the Joomla admin
   account using a common-password dictionary and succeeded in under 2 minutes,
   with the correct password being `batman`.
3. Shortly after both footholds were established, a backdoor (`agent.php`)
   began beaconing from the compromised server, and the server itself retrieved
   a defacement image — `poisonivy-is-coming-for-you-batman.jpeg` — from
   attacker-controlled dynamic-DNS infrastructure.

Whether these two IPs represent one coordinated actor using multiple tools, or
two separate automated/human actors exploiting the same exposed vulnerability
independently, cannot be determined from available logs alone — both are
plausible, and this is stated as an open interpretation rather than a firm
conclusion.

**Verdict:** The GCPD/Pastebin allegation is confirmed. www.imreallynotbatman.com
was compromised via a combination of LFI exploitation and admin credential
brute-forcing, and was defaced with content directly tied to the actor
signature referenced in the original allegation.

## Next Step
Proceed to [07-mitre-mapping.md](07-mitre-mapping.md) to classify each phase of this attack against
the MITRE ATT&CK framework.
