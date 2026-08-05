# Day 2: Web Server Attack Investigation

## Executive Summary
On August 10, 2016, a massive spike in HTTP traffic was detected targeting the Wayne Corp web server (`we9041srv`, IP: `192.168.250.70`). Through systematic SIEM analysis of network stream logs, this traffic was identified as an automated reconnaissance and scanning attack. The threat actor utilized the Acunetix Web Vulnerability Scanner to aggressively map the attack surface and probe for web application flaws.

---

## Scope & Target Assets
* **Compromised Asset:** `we9041srv` (Wayne Corp Web Server)
* **Server IP Address:** `192.168.250.70`
* **Log Sources Analyzed:** `stream:http`

---

## Investigation Methodology & Timeline

### 1. Identifying the Traffic Spike
To establish when the attack took place, network HTTP logs were aggregated by hourly intervals. The timeline revealed an abnormal influx of requests concentrated entirely within a two-hour window:
* **2016-08-10 21:00:** 10,927 requests logged.
* **2016-08-10 22:00:** 9,134 requests logged.
* **2016-08-10 23:00:** Traffic abruptly returned to baseline zero.

### 2. Attributing the Source IP
Filtering the traffic specifically destined for the web server during this timeline isolated a single dominant external source address responsible for over 17,500 requests.

### 3. Fingerprinting the Tooling (User-Agent Analysis)
While the majority of the requests initially appeared to originate from a standard Google Chrome browser (`Mozilla/5.0...`), deeper header inspection of individual payload injections unmasked the underlying tool signature (`acunetix_wvs_security_test`), confirming the use of a commercial vulnerability scanner.

---

## Indicators of Compromise (IOCs)
* **Target IP:** `192.168.250.70`
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
* **Purpose:** Groups HTTP traffic into 1-hour blocks to visually detect volume anomalies and map the exact window of attack.

### Identifying the Attacker IP
\`\`\`spl
index="botsv1" sourcetype="stream:http" dest_ip="192.168.250.70"
| stats count by src_ip 
| sort - count
\`\`\`
* **Purpose:** Aggregates requests by source IP to isolate external entities targeting the web application.

### Fingerprinting the User-Agent / Tooling
\`\`\`spl
index="botsv1" sourcetype="stream:http" dest_ip="192.168.250.70" src_ip="40.80.148.42"
| stats count by http_user_agent 
| sort - count
\`\`\`
* **Purpose:** Inspects HTTP header configurations to uncover automated scanning signatures and spoofed browsers.

---

## Evidence & Screenshots
*(Placeholders for your screenshots inside the repository's `Screenshots/` folder)*
* **Traffic Timeline Spike:** `![Traffic Spike](Screenshots/web_traffic_spike.png)`
* **Attacker Source IP Table:** `![Attacker IP](Screenshots/attacker_ip.png)`
* **Tool User-Agent Analysis:** `![User Agent](Screenshots/attacker_user_agent.png)`

---

## Recommendations / Next Steps
1. **Firewall Mitigation:** Implement an immediate edge block on the external attacker IP (`40.80.148.42`).
2. **Web Application Log Audit:** Review HTTP response status codes (e.g., 200 vs 404 vs 500) originating from this IP to determine if any specific injection attempts or file enumeration paths were successful.
3. **Transition to Day 3:** Investigate internal host logs (`we9041srv`) to check if the vulnerability scanner's automated probes resulted in code execution or compromise.