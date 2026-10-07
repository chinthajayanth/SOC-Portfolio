# 04 — Defacement Confirmation

## Objective
Determine whether the brute-force compromise of the Joomla admin account was
used to actually deface www.imreallynotbatman.com, and obtain direct evidence
of the defacement content and its source infrastructure.

---

## Step 1 — Confirm the brute-force attack succeeded

**Query:**
```spl
index=botsv1 sourcetype="stream:http" c_ip=23.22.63.114 uri_path="/joomla/administrator/index.php"
| stats count by status, bytes_out
```

**Result:**

| status | bytes_out | count |
|---|---|---|
| 303 | 449 | 411 |
| 200 | 6,093 | 412 |
| 200 | 6,287 | 411 |
| **200** | **30,661** | **1** |

**Screenshot:**
[](screenshots/04-01-status-bytes-breakdown.png)

**Explanation:** 823 failed-login-sized responses, and a single outlier nearly
5x larger — consistent with a successful authentication returning the full
admin dashboard instead of the login form. Pulled directly and confirmed below.

---

## Step 2 — Direct proof of successful authentication

**Query:**
```spl
index=botsv1 sourcetype="stream:http" c_ip=23.22.63.114 uri_path="/joomla/administrator/index.php" bytes_out=30661
| table _time, dest_content, status
```

**Result (excerpt of `dest_content`):**
```html
<title>I'm Not Batman - Administration - Control Panel</title>
...
<a class="brand ..." href="http://imreallynotbatman.com/joomla/" title="Preview I'm Not Batman" ...>I'm Not Batman</a>
```
Timestamp: `2016-08-10 21:46:40.781`

**Screenshot:**
[](screenshots/04-02-authenticated-dashboard-html.png)

**Explanation:** This is direct, primary evidence — the raw rendered HTML of
the authenticated Joomla "Control Panel" page — proving `23.22.63.114` achieved
full administrative access to the CMS at this exact timestamp.

---

## Step 3 — Identify the correct password (with correction)

**Initial (incorrect) approach:** Time-adjacency to the successful login
suggested `passwd=skippy` as the likely credential, based on it being the
closest preceding POST attempt.

**Corrected approach — frequency analysis across all attempts:**
```spl
index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST uri=/joomla/administrator/index.php
| rex field=form_data "passwd=(?<string>\w+)"
| stats count by string
| sort -count
```

**Result (top rows):**

| string | count |
|---|---|
| **batman** | **2** |
| 000000 | 1 |
| 1111 | 1 |
| 111111 | 1 |
| ... (all others) | 1 |

**Screenshot:**
[](screenshots/04-03-password-frequency.png)

**Explanation:** Every password in the brute-force list was attempted exactly
once, except `batman`, which appears twice — once as part of the automated
brute-force sequence, and once more as the actual successful authenticated
login shortly after. This is a more reliable method than timestamp proximity,
since it does not depend on assuming no other request could fall between the
guess and the success. **Correction note:** the initial timing-based guess
(`skippy`) is superseded by this frequency-based finding (`batman`) as the
confirmed correct password.

---

## Step 4 — Identify the defacement file and its source infrastructure

**Query:**
```spl
index=botsv1 sourcetype="stream:http" src_ip="192.168.250.70" http_method=GET
| table _time, uri_path, dest_ip, site
| sort _time
```

**Result (relevant entries):**

| _time | uri_path | dest_ip | site |
|---|---|---|---|
| 2016-08-10 22:06:21.569 | /poisonivy-is-coming-for-you-batman.jpeg | 23.22.63.114 | prankglassinebracket.jumpingcrab.com:1337 |
| 2016-08-10 22:13:46.915 | /poisonivy-is-coming-for-you-batman.jpeg | 23.22.63.114 | prankglassinebracket.jumpingcrab.com:1337 |

**Screenshot:**
[](screenshots/04-04-defacement-file-retrieval.png)

**Explanation:** This is the conclusive finding. The compromised web server
itself (`192.168.250.70`) initiated outbound GET requests retrieving a file
named `poisonivy-is-coming-for-you-batman.jpeg` from attacker-controlled
infrastructure at `prankglassinebracket.jumpingcrab.com` (resolving to
`23.22.63.114`) over port `1337`. The filename directly matches the actor
signature (`po1s0n1vy`) and the taunting message found in the original
Pastebin post (`01-allegation-and-osint.md`), closing the loop between the
external allegation and internal technical evidence. `jumpingcrab.com` is a
free dynamic DNS provider, consistent with disposable attacker infrastructure.
The file was fetched twice, six minutes apart, likely a retry or re-display.

---

## Step 5 — Check for antivirus/UTM-level detection of the transfer

**Query:**
```spl
index=botsv1 sourcetype="fgt_utm" dstip=23.22.63.114 OR srcip=23.22.63.114
| table _time, eventtype, subtype, msg, filename, file_hash
| sort _time
```

**Result:** Only `webfilter` subtype events were found
(`"URL belongs to an allowed category in policy"`); no antivirus-engine
`filename`/`file_hash` entries were present for this transfer.

**Screenshot:**
[](screenshots/04-05-utm-webfilter-only.png)

**Explanation:** This is a documented limitation, not a gap in the
investigation: the UTM profile's antivirus engine did not inspect this
specific transfer as a file-scan event, so no hash was generated at the
firewall layer. The firewall's web-filter component did see and explicitly
permit the traffic under an allowed URL category — the same policy-gap
finding noted in `03-exploitation.md`, now confirmed to extend to the
defacement delivery itself, not just the scanning phase.

---

## Finding Summary

| Attribute | Value |
|---|---|
| Compromised account | `admin` (Joomla CMS) |
| Correct password | `batman` |
| Authentication confirmed | 2016-08-10 21:46:40 UTC, via direct HTML evidence |
| Defacement file | `poisonivy-is-coming-for-you-batman.jpeg` |
| Delivery infrastructure | `prankglassinebracket.jumpingcrab.com:1337` (23.22.63.114) |
| First/last retrieval | 22:06:21 / 22:13:46 (2016-08-10) |
| UTM/AV detection | Not present (web-filter allowed; no AV inspection triggered) |
| **Verdict** | **Confirmed: www.imreallynotbatman.com was compromised and defaced** |

## Next Step
Proceed to `05-attacker-infrastructure.md` to consolidate all IOCs (IPs, domain,
tooling) and `06-timeline.md` to assemble the full chronological attack chain
from `01` through `04`.