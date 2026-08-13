
## Security Operations & Incident Response Portfolio

Welcome to my professional security operations and threat-hunting portfolio. This repository serves as a showcase of my hands-on experience as a SOC Analyst, documenting structured investigations of simulated network intrusions, endpoint

compromises, and active web attacks.

## About Me

I am an analytical, detail-oriented Security Operations Center (SOC) Analyst specializing in triaging alerts, dissecting raw logs, and performing root-cause analysis to protect critical infrastructure. I combine a strong foundation in threat containment with automated and manual forensic investigation techniques.

## Core Technical Skillset

- SIEM & Log Analysis: Proficient in extracting, normalizing, and analyzing raw proxy, firewall, and DNS logs (Splunk, LetsDefend SIEM, and Elastic). Spans tracking malicious payloads to decoding unauthenticated traffic sequences.

- Endpoint Forensics: Experienced in reconstructing post-compromise activity, auditing Windows process trees, analyzing parent-child process relationships (e.g., shell spawns under IIS application pools), and identifying persistent registry/file modifications.


- Incident Response & Frameworks: Adept at executing the structured containment, eradication, and recovery phases of the NIST and SANS Incident Response lifecycles, with direct mapping to the MITRE ATT&CK framework.


## Case Index

Below is an indexed summary of my key investigations. Click on the hyperlinks in the table to view the full, detailed technical write-ups.

| Platform | Case Identifier & Category | Primary Threat & Summary | Full Investigation Report |
| :--- | :--- | :--- | :--- |
| **LetsDefend** | SOC342 - CVE-2025-53770<br>*(Web Attack Intrusion)* | Unauthenticated Remote Code Execution (RCE) on SharePoint Server using a spoofed referer bypass. | [View Full Report](LetsDefend/SOC342-CVE-2025-53770-SharePoint-RCE/README.md) |
### Splunk BOTSv1
| Investigation Phase | Primary Threat & Summary | Report |
| :--- | :--- | :--- |
| **Day 1: Threat Hunting** | Initial endpoint reconnaissance and scanning behavior targeting active domain assets. | [View Day 1 Report](BOTSv1/Day1-Investigation.md) |
| **Day 2: Web Attack** | SQL Injection (SQLi) and cross-site scripting (XSS) analysis on public-facing servers. | [View Day 2 Report](BOTSv1/Day2-WebAttack.md) |
| **Day 3: Lateral Movement** | Lateral movement detection using compromised domain administrator credentials across internal subnets. | [View Day 3 Report](BOTSv1/Day3-LateralMovement.md) |
| **Day 4: Persistence** | Process auditing and registry modification analysis showing the establishment of remote system persistence. | [View Day 4 Report](BOTSv1/Day4-Persistence.md) |
| **Day 5: Data Exfiltration** | Identifying unauthorized database queries and data staging pointing to anomalous network exfiltration. | [View Day 5 Report](BOTSv1/Day5-DataExfiltration.md) |

## Repository Directory Structure

To demonstrate systematic change control and configuration management, this

repository is organized using a clean, standardized layout. Raw evidentiary files, screenshots, and containment proofs are organized separately from the analytical narratives:


## Operational Security & Safe Disclosure Guidelines

In accordance with standard professional security practices, all public files in this repository have been subjected to strict sanitization rules:

- No Active Code Execution: Malicious web shells and command scripts are saved with non-executable extensions (.txt) to completely block risk of execution.

- Defanged Network Indicators: All malicious IP addresses, URLs, and external domains are defanged (e.g., 107[.]191[.]58[.]76, hxxp://) to ensure zero clickable vectors in markdown or raw files.

- No PII Leakage: All internal, personal, or corporate identities have been stripped out or fully anonymized.
