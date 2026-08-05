# Day 2: Web Server Attack Investigation

## Executive Summary
On August 10, 2016, a massive spike in HTTP traffic was detected targeting the Wayne Corp web server (`we9041srv`, IP: `192.168.250.70`). Through systematic SIEM analysis of network stream logs, this traffic was identified as an automated reconnaissance and scanning attack. The threat actor utilized the Acunetix Web Vulnerability Scanner to aggressively map the attack surface and probe for web application flaws.

---

## Scope & Target Selection Rationale

### Why Focus on `we9041srv`?
During an initial log distribution audit across all hosts in the environment, several machines exhibited higher raw event counts (such as the SIEM infrastructure `splunk-02` at ~293k events and the workstation `we8105desk` at ~244k events). `we9041srv` recorded a lower volume (~90k events). 

However, triage prioritization in incident response is driven by **perimeter exposure**, not raw log volume:
1. **Perimeter vs. Internal:** `we9041srv` hosts public-facing web applications, making it the primary vector for **Initial Access** (external scans and web injections). Workstations and internal servers represent lateral movement or background noise.
2. **Host Profiling:** Querying log sourcetypes specifically associated with `we9041srv` (`sourcetype="iis"` and `stream:http`) verified its role as the organization's primary web server handling external HTTP/HTTPS traffic.

---

## Investigation Methodology & Timeline

### 1. Identifying the Traffic Spike
To establish when the attack took place, network HTTP logs were aggregated by hourly intervals. The timeline revealed an abnormal influx of requests concentrated entirely within a two-hour window:
* **2016-08-10 21:00:** 10,927 requests logged.
* **2016-08-10 22:00:** 9,134 requests logged.
* **2016-08-10 23:00:** Traffic abruptly returned to baseline zero.

### 2. Attributing the Source IP
Filtering the traffic specifically destined for the web server during this timeline isolated a single dominant external source address (`40.80.148.42`) responsible for over 17,500 requests.

### 3. Fingerprinting the Tooling (User-Agent Analysis)
While the majority of the requests initially appeared to originate from a standard Google Chrome browser, deeper header inspection of individual payload injections unmasked the underlying tool signature (`acunetix_wvs_security_test`), confirming the use of a commercial vulnerability scanner.

---

## Indicators of Compromise (IOCs)
* **Target IP:** `192.168.250.70` (`we9041srv`)
* **Attacker IP:** `40.80.148.42`
* **Identified Tool:** Acunetix Web Vulnerability Scanner
* **Spoofed User-Agent:** `Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.21 (KHTML, like Gecko) Chrome/41.0.2228.0 Safari/537.21`

---

## SPL Queries Used

### Discovering the Traffic Spike
\`\`\`spl
index="botsv1" sourcetype="stream:http" dest_ip="192.168.250.70"
| timechart span=1h count
\`\`\`

### Identifying the Attacker IP
\`\`\`spl
index="botsv1" sourcetype="stream:http" dest_ip="192.168.250.70"
| stats count by src_ip 
| sort - count
\`\`\`

### Fingerprinting the Tooling
\`\`\`spl
index="botsv1" sourcetype="stream:http" dest_ip="192.168.250.70" src_ip="40.80.148.42"
| stats count by http_user_agent 
| sort - count
\`\`\`

---

## Evidence & Screenshots
* **Traffic Timeline Spike:** `![Traffic Spike](Screenshots/web_traffic_spike.png)`
* **Attacker Source IP Table:** `![Attacker IP](Screenshots/attacker_ip.png)`
* **Tool User-Agent Analysis:** `![User Agent](Screenshots/attacker_user_agent.png)`

---

## Next Steps
1. **Analyze HTTP Response Status Codes:** Determine if the Acunetix scans resulted in successful page returns (`200 OK`) or failed requests (`404 Not Found` / `500 Server Error`).
2. **Transition to Day 3:** Check internal logs on `we9041srv` to see if the vulnerability scanner successfully executed a payload or gained unauthorized access.