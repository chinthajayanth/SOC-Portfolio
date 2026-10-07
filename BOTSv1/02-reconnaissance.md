# 02 — Reconnaissance

## Objective
Identify whether www.imreallynotbatman.com was scanned or probed prior to any
exploitation, and identify the source responsible.

---

## Step 1 — Identify top traffic sources to the target site

**Query:**
```spl
index=botsv1 imreallynotbatman.com sourcetype="stream:http"
| stats count by c_ip
| sort -count
```

**Result:**

| c_ip | count |
|---|---|
| 40.80.148.42 | 20,964 |
| 23.22.63.114 | 1,236 |

**Screenshot:** `screenshots/02-01-top-source-ips.png`

**Explanation:** One IP, `40.80.148.42`, accounts for 94.4% of all HTTP traffic to
the site. A small corporate blog would not normally see this level of traffic
concentration from a single source. This IP is treated as the primary suspect for
reconnaissance/attack activity and is carried forward as the pivot for all
subsequent queries in this section.

---

## Step 2 — Measure path diversity for the suspect IP

**Query:**
```spl
index=botsv1 sourcetype="stream:http" c_ip=40.80.148.42
| stats dc(uri_path)
```

**Result:**

| dc(uri_path) |
|---|
| 1,881 |

**Screenshot:** `screenshots/02-02-distinct-uri-count.png`

**Explanation:** 1,881 distinct URL paths requested by a single source is not
consistent with normal human browsing of a small blog. This volume of path
diversity is characteristic of automated content/vulnerability scanning, where a
tool systematically requests a large dictionary of known paths to see what exists
on the server.

---

## Step 3 — Break down response codes for the suspect IP

**Query:**
```spl
index=botsv1 sourcetype="stream:http" c_ip=40.80.148.42
| stats dc(uri_path) by status
| sort -dc(uri_path)
```

**Result:**

| status | dc(uri_path) |
|---|---|
| 404 | 1,759 |
| 200 | 86 |
| 403 | 36 |
| 301 | 16 |
| 304 | 15 |
| 303 | 10 |
| 400 | 8 |
| 500 | 5 |
| 405 | 1 |
| 417 | 1 |
| 501 | 1 |

**Screenshot:** `screenshots/02-03-status-code-breakdown.png`

**Explanation:** 93.5% of requested paths returned HTTP 404 (not found) —
confirming most guessed paths do not exist on the server. This 404-heavy
distribution paired with high path diversity is the textbook signature of
automated scanning rather than targeted browsing. The 86 URIs that returned
HTTP 200 are significant: these are real, existing resources the scanner
successfully found, and become the candidate list for exploitation analysis
in the next section.

---

## Step 4 — Inspect the User-Agent used by the suspect IP

**Query:**
```spl
index=botsv1 sourcetype="stream:http" c_ip=40.80.148.42
| top limit=10 http_user_agent
```
*(replace `http_user_agent` with whichever field name your `fieldsummary` showed — 
confirm this before finalizing)*

**Result:**

| User-Agent | Count | % |
|---|---|---|
| Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.21 (KHTML, like Gecko) Chrome/41.0.2228.0 Safari/537.21 | 20,964 | 100% |

**Screenshot:** `screenshots/02-04-user-agent.png`

**Explanation:** All requests from this IP used a single, consistent User-Agent
string claiming to be Chrome version 41.0.2228.0. This version number does not
correspond to any genuine Chrome release (real Chrome 41 builds follow a
different versioning pattern), indicating a forged/spoofed User-Agent — a
common technique scanning tools use to blend in with legitimate browser traffic.

---

## Finding Summary

| Attribute | Value |
|---|---|
| Suspect IP | 40.80.148.42 |
| Total requests | 20,964 (94.4% of site traffic) |
| Distinct paths probed | 1,881 |
| Not-found rate | 93.5% |
| User-Agent | Spoofed Chrome 41.0.2228.0 |
| Assessment | **Automated reconnaissance / vulnerability scanning confirmed** |

## Next Step
Proceed to `03-exploitation.md` — inspect the 86 URIs that returned HTTP 200 to
identify what the scanner found and whether any request represents a successful
exploitation attempt.