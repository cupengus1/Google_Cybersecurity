# Google Cybersecurity Professional Certificate — Study Guide & Comprehensive SOC Notes

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Modules: 8/8 Complete](https://img.shields.io/badge/Modules-8%2F8%20Complete-brightgreen.svg)](#study-guide-index)
[![Field: Cybersecurity](https://img.shields.io/badge/Field-Cybersecurity%20%7C%20SOC%20Analyst-blue.svg)](#core-domains--technologies)
[![Focus: CompTIA Security+ Aligned](https://img.shields.io/badge/Exam%20Prep-CompTIA%20Security%2B%20%7C%20Google%20Cert-red.svg)](#certification-alignment)

A structured, module-by-module study guide and technical reference for the **Google Cybersecurity Professional Certificate** and entry-level **Security Operations Center (SOC) Analyst** training.

Each module is consolidated into a single markdown document containing:
- **Core Theoretical Foundations & Frameworks** (NIST CSF 2.0, ISO 27001, MITRE ATT&CK).
- **Technical Toolkits & Practical Commands** (Linux CLI, SQL Queries, Python Automation, Wireshark, Snort).
- **Real-World Case Studies & Log Forensics** (Windows Security Event IDs, Syslog, Web Application Attacks).
- **Self-Assessment Checklists & Quick Checks with Expandable Answer Keys**.

---

## Study Guide Index

| Module | Title | Core Focus & Key Topics Covered | Study Notes |
| :---: | :--- | :--- | :---: |
| **01** | **Foundations of Cybersecurity** | CIA Triad, Risk Equation, 8 CISSP Security Domains, NIST CSF 2.0 (Govern $\rightarrow$ Recover), SOC Analyst Workflows, Threat Actors & Attack Vectors. | [📖 View Notes](module-01-foundations-of-cybersecurity.md) |
| **02** | **Play It Safe - Manage Security Risks** | Quantitative Risk ($\text{SLE}, \text{ALE}$), Qualitative $5\times5$ Matrices, 4 Risk Treatments, Security Audits (POA&M), Windows Event IDs (4624, 4625, 1102), SIEM Triage, IR Playbooks. | [📖 View Notes](module-02-manage-security-risks.md) |
| **03** | **Connect and Protect - Networks & Security** | OSI 7-Layer & TCP/IP Models, Encapsulation PDUs, Subnetting & DMZ Architecture, Port Protocols (22, 53, 80, 443, 445, 3389), TCP Handshakes, Network Attacks (DDoS, MitM, DNS Tunneling), Zero Trust (ZTNA). | [📖 View Notes](module-03-networks-and-network-security.md) |
| **04** | **Tools of the Trade - Linux and SQL** | Linux FHS Hierarchy (`/var/log`, `/etc`), I/O Streams & Pipeline Log Parsing (`grep`, `awk`, `sed`, `sort`, `uniq`), Permissions (`chmod 750`), SUID/SGID Security, SQL Filtering, Aggregations, Joins & SQLi Defense. | [📖 View Notes](module-04-linux-and-sql.md) |
| **05** | **Assets, Threats, and Vulnerabilities** | Asset Lifecycles, NIST SP 800-88 Sanitization (*Clear, Purge, Destroy*), Symmetric/Asymmetric Crypto & Hashing, AAA & Access Control (RBAC/ABAC), CVSS v3.1/v4.0, EPSS, CISA KEV, Pyramid of Pain, STRIDE Threat Modeling. | [📖 View Notes](module-05-assets-threats-and-vulnerabilities.md) |
| **06** | **Sound the Alarm - Detection & Response** | Event vs. Alert vs. Incident, Order of Volatility (RFC 3227), Packet Analysis (Wireshark Display Filters, `tcpdump`), TCP Flags, IoCs vs. IoAs, Root Cause Analysis (5 Whys), Snort/Suricata Rule Writing, Log Tiers. | [📖 View Notes](module-06-detection-and-response.md) |
| **07** | **Automate Cybersecurity Tasks with Python** | Data Types & Structures (Lists, Dictionaries, Sets), Control Flow, Security Modules (`re`, `ipaddress`, `requests`, `hashlib`), Regex Parsing for IPs/Hashes/CVEs, Context Managers (`with open`), Defensive Exception Handling. | [📖 View Notes](module-07-python-automation.md) |
| **08** | **Put It to Work - Prepare for Cybersecurity Jobs** | Evidence Integrity (Chain of Custody), PII/PHI Redaction, Shift Handover Protocols, Executive Briefings vs. Technical Reports, SOC KPIs (**MTTD, MTTR**), MITRE ATT&CK TTPs, STAR Method Interview Framework. | [📖 View Notes](module-08-prepare-for-cybersecurity-jobs.md) |

---

## Core Domains & Technologies

```
+----------------------------------------------------------------------------------------------------+
|                                    CYBERSECURITY KNOWLEDGE MAP                                     |
+-------------------+-------------------+-------------------+--------------------+-------------------+
| GOVERNANCE & RISK | NETWORK DEFENSE   | SYSTEM ANALYSIS   | THREAT DETECTION   | AUTOMATION & CODE |
+-------------------+-------------------+-------------------+--------------------+-------------------+
| • NIST CSF 2.0    | • TCP/IP & OSI    | • Linux CLI & FHS | • SIEM (Splunk)    | • Python 3        |
| • ISO/IEC 27001   | • Firewalls/NGFW  | • Bash Pipelines  | • IDS/IPS (Snort)  | • Security Regex  |
| • PCI-DSS / HIPAA | • Wireshark PCAP  | • SQL Queries     | • EDR Telemetry    | • REST APIs       |
| • Risk Modeling   | • Zero Trust/VPN  | • SUID Auditing   | • MITRE ATT&CK     | • File I/O & SOAR |
| • STRIDE Modeling | • DMZ & VLANs     | • User Management | • IoC / IoA Triage | • Error Handling  |
+-------------------+-------------------+-------------------+--------------------+-------------------+
```

---

## How to Use These Notes

1. **Sequential Study:** If you are progressing through the Coursera certificate, follow the modules in numerical order from **Module 1 to Module 8**.
2. **Review Key Terminology:** Review the summary tables and taxonomy charts before quizzes and graded assessments.
3. **Analyze Real Log Scenarios:** Practice reading the real-world log snippets (Windows Event Logs, Linux Syslog, Apache/Nginx access logs, and Wireshark filters).
4. **Test Your Recall:** Attempt the **Quick Check** questions at the end of each weekly section before clicking to reveal the detailed answer keys.
5. **Practical Hands-on Replication:** Run the provided Linux commands, SQL queries, and Python automation scripts inside a safe lab or local sandbox environment.

---

## Certification Alignment

These study notes cover the dual curriculum of:
- **Google Cybersecurity Professional Certificate** (All 8 Modules & Capstone Portfolio).
- **CompTIA Security+ (SY0-701):** General security concepts, threats/vulnerabilities/mitigations, security architecture, security operations, and security program management & oversight.

---

## Quick Reference Commands

<details>
<summary><b>🐧 Linux Log Parsing One-Liners</b></summary>

```bash
# Extract top 5 failed SSH login IPs
grep "Failed password" /var/log/auth.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -nr | head -n 5

# Locate all SUID binaries on system
find / -type f -perm -4000 2>/dev/null
```
</details>

<details>
<summary><b>🗄️ SQL Security Investigation Snippet</b></summary>

```sql
-- Identify accounts with more than 50 failed logins (Brute Force Detection)
SELECT source_ip, COUNT(*) AS failed_attempts
FROM user_logins
WHERE login_status = 'FAILED'
GROUP BY source_ip
HAVING COUNT(*) > 50
ORDER BY failed_attempts DESC;
```
</details>

<details>
<summary><b>🐍 Python IoC Regex Extractor</b></summary>

```python
import re

log_data = "Outbound connection to 198.51.100.24 with hash e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"

ips = re.findall(r"\b(?:\d{1,3}\.){3}\d{1,3}\b", log_data)
hashes = re.findall(r"\b[a-fA-F0-9]{64}\b", log_data)

print(f"Extracted IPs: {ips}")
print(f"Extracted SHA-256: {hashes}")
```
</details>

---

## Disclaimer

These notes are independently compiled study materials designed for educational purposes, examination preparation, and continuous skill revision. They are not officially published by Google, Coursera, or CompTIA. Always refer to official course materials and primary documentation when completing certification assessments.

---

<div align="center">
  <sub>Maintained for cybersecurity learners & aspiring SOC analysts. Star ⭐ this repository if you find it helpful!</sub>
</div>