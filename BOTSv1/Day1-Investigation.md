# Day 1: Environmental Baseline and Data Discovery

## Objective
To map the available log sources feeding into the SIEM and understand the defensive visibility of the network before hunting for specific threats.

## Query
\`\`\`spl
index="botsv1" | stats count by sourcetype | sort - count
\`\`\`

## Findings: Top 5 Log Sources
1. **xmlwineventlog:microsoft-windows-sysmon/operational (270,597 events):** Sysmon provides granular endpoint visibility, including process creation, network connections, and file modifications. This will be critical for tracking malware execution.
2. **stream:smb (151,568 events):** Network traffic logs specifically capturing Server Message Block (SMB) activity. This is highly useful for detecting lateral movement and unauthorized file transfers.
3. **suricata (125,584 events):** Network Intrusion Detection System (NIDS) alerts. This indicates we have network boundary monitoring to flag known malicious signatures.
4. **wineventlog:security (87,430 events):** Standard Windows security logs. Essential for tracking authentication failures, successful logons, and potential privilege escalation.
5. **winregistry (74,720 events):** Logs detailing modifications to the Windows Registry, which is a primary location to hunt for persistence mechanisms.

## Conclusion
The environment has strong endpoint visibility (Sysmon, Windows Event Logs) combined with network-level detection (Suricata, Splunk Stream). We are well-equipped to track an attacker from initial network compromise down to host-level execution.

## Host Inventory & Domain Infrastructure

Query:
\`\`\`spl
index="botsv1" | stats count by host | sort - count
\`\`\`

Key Assets Identified:
* **Domain:** `waynecorpinc.local`
* **Endpoints:** `we8105desk` (Workstation)
* **Servers:** `we1149srv`, `we9041srv` (Web/App Server)
* **Security Monitoring:** `suricata-ids.waynecorpinc.local`

## Note on User Account Extraction
Standard `by user` aggregation yields incomplete data due to field naming variance across sourcetypes. Accurate user enumeration requires targeting Windows Security Event ID `4624` (`Account_Name`).

## User Account Enumeration
Standard `by user` aggregation yields incomplete data due to field naming variance across sourcetypes. Accurate user enumeration requires targeting Windows Security Event ID `4624` (Successful Logon).

**Query:**
\`\`\`spl
index="botsv1" sourcetype="wineventlog:security" EventCode=4624 | stats count by Account_Name | sort - count
\`\`\`

**Key Accounts Identified:**
* **Human User:** `bob.smith` (The primary endpoint user)
* **Privileged Account:** `Administrator` (High volume of authentications; requires monitoring for brute-force or lateral movement)
* **Machine Accounts:** `WE9041SRV$`, `WE8105DESK$` (Standard Active Directory computer accounts)
