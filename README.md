
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

|   | Case Identifier / | Full |
| --- | --- | --- |
|   | Target | Investigation |
| Platform | Vulnerability Category Primary Threat & Summary | Report |
| LetsDefendSOC342 - CVE- Web Attack / Critical zero-day |   | View Full |
|   | Intrusion unauthenticated Remote 2025-53770 | Report |
|   | Code Execution (RCE) on |   |
|   | SharePoint Server using a |   |
|   | spoofed referer bypass. |   |
| Splunk | BOTSv1 - Day 1 Threat Initial endpoint | View Day 1 |
| BOTS | Hunting / | reconnaissance and scanning Report |
|   | SIEM behavior targeting active |   |
|   | domain assets. |   |
| Splunk | BOTSv1 - Day 2Web SQL Injection (SQLi) and | View Day 2 |
| BOTS | Application cross-site scripting (XSS) | Report |
|   | Attack analysis on public-facing |   |
|   | servers. |   |
| Splunk | BOTSv1 - Day 3Lateral Lateral movement detection | View Day 3 |
| BOTS | Movement using compromised domain | Report |
|   | administrator credentials |   |
|   | across internal subnets. |   |
| Splunk | BOTSv1 - Day 4Endpoint | Process auditing and registry View Day 4 |
| BOTS | Persistence modification analysis | Report |


|   | Case Identifier / | Full |
| --- | --- | --- |
|   | Target | Investigation |
| Platform | Vulnerability Category Primary Threat & Summary | Report |
|   | showing the establishment of remote system persistence. |   |
| Splunk | BOTSv1 - Day 5Data Identifying unauthorized | View Day 5 |
| BOTS | Exfiltration database queries and data staging pointing to anomalous network exfiltration. | Report |

## Repository Directory Structure

To demonstrate systematic change control and configuration management, this

repository is organized using a clean, standardized layout. Raw evidentiary files, screenshots, and containment proofs are organized separately from the analytical narratives:


## Operational Security & Safe Disclosure Guidelines

In accordance with standard professional security practices, all public files in this repository have been subjected to strict sanitization rules:

- No Active Code Execution: Malicious web shells and command scripts are saved with non-executable extensions (.txt) to completely block risk of execution.

- Defanged Network Indicators: All malicious IP addresses, URLs, and external domains are defanged (e.g., 107[.]191[.]58[.]76, hxxp://) to ensure zero clickable vectors in markdown or raw files.

- No PII Leakage: All internal, personal, or corporate identities have been stripped out or fully anonymized.
