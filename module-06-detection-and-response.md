# Module 6: Sound the Alarm - Detection and Response

## How to Use This Module

This module forms the operational core of **Security Operations Center (SOC) Tier 1 and Tier 2 workflows**. It details how security teams monitor massive telemetry streams, detect security anomalies, inspect network packet captures, verify Indicators of Compromise (IoCs), execute containment playbooks, preserve digital forensic evidence, and construct rigorous incident post-mortems.

Follow the 4-week progression:
1. **Week 1:** Incident Response Frameworks, CSIRT Roles, Order of Volatility, and Incident Classification.
2. **Week 2:** Deep Packet Inspection (Wireshark & Tcpdump), TCP Flag Analysis, and Network Scan Signatures.
3. **Week 3:** Alert Triage Methodologies, IoCs vs. IoAs, Forensic Timelines, and Root Cause Analysis (RCA).
4. **Week 4:** IDS Rule Engineering (Snort/Suricata), Log Forensics across Hybrid Sources, and SIEM Hunting Queries.

---

## Week 1: Incident Response Lifecycle and Security Operations

### What You Should Understand

Not every observable system event represents a crisis. A frontline analyst must rapidly distinguish between **Events**, **Alerts**, and confirmed **Security Incidents** to prevent operational bottlenecks and ensure critical threats receive immediate containment.

```
+--------------------------------------------------------------------------------+
|                        THE INCIDENT CLASSIFICATION FUNNEL                      |
+--------------------------------------------------------------------------------+
| [ EVENTS ]   -> Millions of benign operational occurrences (Logons, DNS queries)|
|      |                                                                         |
|      v (Filtered by SIEM Correlation Rules)                                    |
| [ ALERTS ]   -> Hundreds of notifications flagged for human analyst triage      |
|      |                                                                         |
|      v (Validated as Malicious Activity with Business Impact)                   |
| [ INCIDENT ] -> Confirmed breach or unauthorized activity (CSIRT Activated)    |
+--------------------------------------------------------------------------------+
```

---

### Incident Response Frameworks: NIST SP 800-61 vs. SANS

```
+-----------------------------------+-----------------------------------+
| NIST SP 800-61 (4 Phases)         | SANS Institute (6 Steps)          |
+-----------------------------------+-----------------------------------+
| 1. Preparation                    | 1. Preparation                    |
| 2. Detection and Analysis         | 2. Identification                 |
| 3. Containment, Eradication,      | 3. Containment                    |
|    and Recovery                   | 4. Eradication                    |
| 4. Post-Incident Activity         | 5. Recovery                       |
|    (Lessons Learned)              | 6. Lessons Learned                |
+-----------------------------------+-----------------------------------+
```

---

### Digital Forensics: Order of Volatility (RFC 3227)

When seizing digital evidence during an active incident, investigators must collect evidence in order from the **most volatile (lost upon power down)** to the **least volatile (persists on disk)**:

```
  [1. CPU Registers & Cache]          (Changes in nanoseconds)
            |
            v
  [2. Routing Table, ARP, Process Table, Kernel Stats, RAM]  (Lost immediately if powered off)
            |
            v
  [3. Temporary Filesystems / Swap / Pagefile]
            |
            v
  [4. Non-Volatile Disk Storage (HDDs, SSDs, Flash)]
            |
            v
  [5. Remote Log Telemetry (SIEM, Syslog Servers)]
            |
            v
  [6. Network Physical Topology & Archival Backup Tapes]
```

> [!CAUTION]
> **Never pull the power plug on an active machine undergoing investigation.**
> Doing so wipes the volatile system memory (RAM), destroying live malware code, active network connections, injected DLLs, and in-memory cryptographic keys.

---

### Incident Severity & CSIRT Structure

```
+--------------------------------------------------------------------------------+
|                     CSIRT (Computer Security IR Team) ROLES                    |
+-------------------+------------------------------------------------------------+
| Incident Commander| Directs overall strategy, delegates tasks, controls budget |
| Technical Lead    | Oversees technical investigation, packet & log forensics   |
| Lead Investigator | Coordinates containment actions with system administrators |
| Legal Counsel     | Advises on regulatory disclosure mandates (GDPR/HIPAA/SEC) |
| Communications/PR | Handles press releases and customer communications         |
+-------------------+------------------------------------------------------------+
```

| Severity | Impact Criteria | SLA Response Time |
| :--- | :--- | :--- |
| **Critical (P1)** | Widespread ransomware; unauthorized root access to domain controller; active customer PII data leak. | **< 15 minutes** (CSIRT fully activated) |
| **High (P2)** | Single production server infected with trojan; lateral movement blocked; service partially impaired. | **< 1 hour** (Senior IR response) |
| **Medium (P3)**| Single endpoint infected with adware; isolated phishing email click without credential entry. | **< 4 hours** (Tier 1/2 SOC Analyst) |
| **Low (P4)** | Port scan blocked at perimeter firewall; spam email caught by filter. | **< 24 hours** (Automated / Batch review)|

---

### Quick Check & Answers

#### Questions
1. *What is the difference between an Alert and an Incident?*
2. *According to the Order of Volatility, why must RAM be captured before acquiring a disk image?*
3. *What is the role of Legal Counsel on an enterprise CSIRT?*
4. *What are the four phases of the NIST SP 800-61 incident response lifecycle?*
5. *Why should you network-isolate a compromised host rather than shutting it down?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** An **alert** is an automated warning indicating that a specific rule or threshold has been triggered and requires investigation. An **incident** is a confirmed security event that compromises the confidentiality, integrity, or availability of an organization's systems or data.
2. **Answer:** RAM is volatile memory that is completely wiped when the computer loses power or reboots, whereas physical disks retain their data permanently without electricity.
3. **Answer:** Legal Counsel advises on compliance obligations, determines statutory breach notification deadlines to regulators/customers, and ensures forensic evidence collection complies with legal standards.
4. **Answer:** 1. **Preparation**, 2. **Detection and Analysis**, 3. **Containment, Eradication, and Recovery**, 4. **Post-Incident Activity (Lessons Learned)**.
5. **Answer:** Isolating the network interface cuts off attacker command-and-control (C2) and prevents lateral spread while keeping system RAM intact for forensic analysis.
</details>

---

## Week 2: Network Traffic and Packet Analysis

### What You Should Understand

Network packets are the ground truth of digital communications. Analysts use packet capture tools (**Wireshark** for GUI analysis, **tcpdump** for headless CLI captures) to reconstruct conversations, inspect protocol flags, and identify malicious traffic patterns.

---

### TCP Flags & Packet Inspection

TCP communication is governed by 6 standard 1-bit control flags in the TCP header:
- **SYN (Synchronize):** Initiates a connection handshake.
- **ACK (Acknowledgment):** Confirms receipt of packets.
- **PSH (Push):** Forces immediate delivery of data to application without buffering.
- **URG (Urgent):** Indicates data within segment should be processed immediately.
- **FIN (Finish):** Gracefully requests connection termination.
- **RST (Reset):** Abruptly tears down and terminates an invalid connection.

```
                      +---------------------------------------+
                      |         TCP PORT SCAN PATTERNS        |
                      +---------------------------------------+
                      | 1. SYN Stealth Scan (Half-Open)       |
                      | 2. Connect Scan (Full 3-Way Handshake)|
                      | 3. XMAS Scan (FIN + PSH + URG set)    |
                      | 4. NULL Scan (No flags set)           |
                      +---------------------------------------+
```

---

### CLI Packet Capture: `tcpdump`

`tcpdump` is the premier Linux command-line tool for capturing and filtering network traffic:

```bash
# Capture 100 packets on interface eth0, show IP addresses without DNS resolution
sudo tcpdump -i eth0 -n -c 100

# Capture only HTTP traffic on port 80 and write to a PCAP file
sudo tcpdump -i eth0 -nn -v "port 80" -w http_traffic.pcap

# Read and inspect packets from an existing PCAP file
sudo tcpdump -r http_traffic.pcap -n "src host 192.168.1.100 and dst port 443"
```

---

### Wireshark Display Filters for SOC Investigations

Wireshark allows analysts to drill into raw packet bytes using granular display filters:

```
+-----------------------------------+-----------------------------------------------------------------+
| Wireshark Filter                  | Analytical Purpose                                              |
+-----------------------------------+-----------------------------------------------------------------+
| `ip.addr == 192.168.1.50`         | Shows all traffic where source OR destination is 192.168.1.50   |
| `tcp.flags.syn == 1 &&            | Filters for TCP SYN connection requests without ACK             |
|  tcp.flags.ack == 0`              | (Identifies inbound connection attempts or SYN port scans)      |
| `http.request.method == "POST"`   | Isolates web form submissions, credential postings, or uploads  |
| `dns.flags.response == 0`         | Displays only outgoing DNS queries (detects DNS C2 beaconing)   |
| `tcp.flags.reset == 1`            | Shows closed/rejected connections (identifies closed ports)     |
| `tls.handshake.type == 1`         | Filters Client Hello messages to inspect TLS SNI domain names   |
+-----------------------------------+-----------------------------------------------------------------+
```

---

### Quick Check & Answers

#### Questions
1. *What combination of TCP flags is enabled in an XMAS port scan?*
2. *How does a TCP SYN scan determine that a destination port is OPEN?*
3. *What Wireshark display filter isolates outgoing DNS query requests?*
4. *What command in `tcpdump` writes captured packets to a file named `capture.pcap`?*
5. *Why is the SNI (Server Name Indication) field in TLS Client Hello packets useful when inspecting encrypted HTTPS traffic?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** **FIN, PSH, and URG** (lighting the packet up "like a Christmas tree").
2. **Answer:** The scanner sends a **SYN** packet. If the target responds with a **SYN-ACK**, the port is open (the scanner then immediately sends a **RST** to terminate without completing the handshake).
3. **Answer:** `dns.flags.response == 0`.
4. **Answer:** `tcpdump -w capture.pcap`.
5. **Answer:** SNI sends the hostname of the destination server in plaintext during the initial TLS handshake before encryption starts, allowing analysts to see what domain name the user is connecting to.
</details>

---

## Week 3: Detection, Verification, Documentation, and Recovery

### What You Should Understand

A detection alert is merely a hypothesis. Security analysts must verify the hypothesis against multiple log sources, distinguish between **Indicators of Compromise (IoCs)** and **Indicators of Attack (IoAs)**, construct a factual timeline, and establish root cause.

---

### IoCs vs. IoAs

```
+-----------------------------------+-----------------------------------+
| INDICATORS OF COMPROMISE (IoCs)   | INDICATORS OF ATTACK (IoAs)       |
+-----------------------------------+-----------------------------------+
| Focuses on **WHAT** happened      | Focuses on **HOW & WHY** it occurs|
| (Forensic artifacts left behind). | (Adversary behavior and intent).  |
| - Specific SHA-256 file hashes    | - Execution of encoded PowerShell |
| - Malicious external IP addresses | - Disabling security event logs   |
| - Malicious domain URLs           | - Dumping LSASS memory for hashes |
| - Known registry persistence keys | - Lateral movement via SMB/PsExec |
+-----------------------------------+-----------------------------------+
```

---

### Alert Verification & Triage Methodology (The 5 W's)

When triaging an alert, an analyst gathers evidence across endpoints, networks, and identities:

```
[ ALERT: "Mimikatz Process Detected on Host-04" ]
       |
       +---> 1. WHO?     -> User account: 'jsmith' (Helpdesk Tech)
       |
       +---> 2. WHAT?    -> Execution of `mimikatz.exe` targeting LSASS memory
       |
       +---> 3. WHEN?    -> 2026-09-23 03:14:22 UTC (Outside regular work hours)
       |
       +---> 4. WHERE?   -> Host: `WS-HELP-04.corp.local` (IP: 10.0.4.15)
       |
       +---> 5. WHY/HOW? -> Ingress via RDP from external VPN session
```

---

### Root Cause Analysis (RCA) & The "Five Whys"

Finding the root cause prevents recurring breaches:

```
Problem: Database server was infected with ransomware.
  1. Why? Attacker gained administrative RDP access.
  2. Why? The administrator password was compromised via brute-force.
  3. Why? Port 3389 was exposed directly to the public internet.
  4. Why? A developer temporarily opened the firewall rule for troubleshooting and forgot to remove it.
  5. Why? (ROOT CAUSE) The company lacked automated configuration auditing and change management enforcement.
```

---

### Incident Timeline Construction Template

| Timestamp (UTC) | Source Asset | Event Description / Telemetry Evidence | Artifact / Tool |
| :--- | :--- | :--- | :--- |
| `2026-09-23 08:12:01` | Mail Gateway | Phishing email with subject "Urgent Invoice" delivered to `bwayne@corp.com`. | Email Gateway Log |
| `2026-09-23 08:15:30` | Workstation WS-12 | User clicked link `http://evil-invoice.com/doc.zip` and downloaded payload. | Web Proxy Log |
| `2026-09-23 08:16:04` | Workstation WS-12 | `powershell.exe` spawned by `WINWORD.EXE` executing base64 encoded script. | EDR Agent Log |
| `2026-09-23 08:17:15` | Firewall | Outbound C2 beacon established to `198.51.100.88:4444`. | Firewall NetFlow |
| `2026-09-23 08:22:00` | SOC Console | Tier 1 Analyst receives high-severity EDR alert; initiates host isolation. | EDR Containment |

---

### Quick Check & Answers

#### Questions
1. *What is the difference between an Indicator of Compromise (IoC) and an Indicator of Attack (IoA)?*
2. *Why is UTC (Coordinated Universal Time) mandated for all incident investigation timelines?*
3. *What is the primary goal of Root Cause Analysis (RCA)?*
4. *Why should incident documentation record only verified facts rather than analyst assumptions?*
5. *What is a Post-Incident Review (Lessons Learned) meeting and who should attend?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** An **IoC** is a static forensic artifact showing that an attack already occurred (e.g., a file hash or malicious IP). An **IoA** identifies dynamic attacker tactics and intent in real time (e.g., unauthorized credential dumping or lateral movement), regardless of what specific malware file is used.
2. **Answer:** Global organizations and cloud services span multiple local timezones. Normalizing all log evidence to UTC prevents chronological confusion when correlating events across worldwide infrastructure.
3. **Answer:** To discover the underlying fundamental failure or vulnerability that enabled the breach, rather than merely fixing the superficial symptom, ensuring controls can be created to permanently prevent recurrence.
4. **Answer:** Incident documentation serves as formal legal and regulatory evidence. Unverified assumptions can misdirect containment workflows or compromise legal integrity in court proceedings.
5. **Answer:** A retrospective meeting held after incident closure bringing together CSIRT members, IT operations, management, and legal to evaluate what went well, identify process gaps, and update playbooks.
</details>

---

## Week 4: Logs, IDS, and SIEM Analysis

### What You Should Understand

Security monitoring operations rely on the continuous aggregation, parsing, and correlation of logs within a **SIEM**, combined with signature and behavioral analysis from **Intrusion Detection Systems (IDS)**.

---

### IDS Rule Anatomy: Snort & Suricata

IDS tools inspect network payloads using structured signature rules:

```snort
alert tcp $EXTERNAL_NET any -> $HTTP_SERVERS 80 (msg:"WEB-ATTACK SQL Injection Attempt"; content:"UNION SELECT"; nocase; sid:1000523; rev:1;)
```

```
+--------------------------------------------------------------------------------+
|                             SNORT RULE BREAKDOWN                               |
+-------------------+------------------------------------------------------------+
| `alert`           | Rule Action: Generate an alert when matched               |
| `tcp`             | Protocol inspected                                         |
| `$EXTERNAL_NET`   | Source IP variable (Any external untrusted network)        |
| `any`             | Source port                                                |
| `->`              | Direction operator (Inbound traffic to server)             |
| `$HTTP_SERVERS 80`| Destination IP variable and target port 80                 |
| `msg:"..."`       | Alert message displayed on SOC console                     |
| `content:"..."`   | String pattern searched inside packet payload              |
| `nocase`          | Case-insensitive matching modifier                         |
| `sid:1000523`     | Snort Identifier (Unique signature ID)                     |
+-------------------+------------------------------------------------------------+
```

---

### HIDS vs. NIDS

- **NIDS (Network Intrusion Detection System):** Placed at network chokepoints (SPAN/TAP ports) to monitor traffic across all devices on the subnet (e.g., Suricata, Zeek). Blind to encrypted traffic payloads.
- **HIDS (Host Intrusion Detection System):** Installed locally on an endpoint (e.g., OSSEC, Wazuh). Monitors local file modifications, system calls, auth logs, and process execution.

---

### Log Ingestion, Normalization, and Retention

```
[ UNPARSED RAW LOG ] 
"Sep 23 10:14:02 srv1 sshd[412]: Failed password for invalid user admin from 192.168.1.99 port 22"
                          |
                          v (SIEM Parsing & Normalization Engine)
[ NORMALIZED DATA MODEL (CIM / ECS) ]
- timestamp     : 2026-09-23T10:14:02Z
- event_type    : authentication
- status        : failure
- user          : admin
- src_ip        : 192.168.1.99
- dest_port     : 22
- app           : sshd
```

#### Log Retention Tiers:
- **Hot Storage (0 - 30 days):** Fast, indexed, expensive storage for instant querying and real-time SOC alerting.
- **Warm Storage (30 - 90 days):** Searchable storage for ongoing investigations and threat hunting.
- **Cold / Archival Storage (90 - 365+ days):** Compressed, encrypted cloud storage (e.g., AWS Glacier) for regulatory and compliance audits.

---

### Quick Check & Answers

#### Questions
1. *What does the `sid` field represent in a Snort IDS rule?*
2. *Why is an encrypted HTTPS payload invisible to a standard NIDS sensor?*
3. *What is the difference between Hot and Cold log storage tiers?*
4. *What is log normalization and why is it essential for SIEM correlation?*
5. *How does a Host-based IDS (HIDS) complement a Network-based IDS (NIDS)?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** **Snort Identifier (SID)**, a unique numerical identifier assigned to each specific Snort detection rule.
2. **Answer:** TLS encryption encrypts the packet payload at the transport layer, rendering the HTTP text unreadable to a passive network sensor that does not possess the server's private key or a decryption proxy.
3. **Answer:** **Hot storage** is uncompressed, highly indexed, and optimized for fast real-time search and alerting; **Cold storage** is compressed, cheaper, and stored long-term for compliance, but requires time to restore before searching.
4. **Answer:** Normalization parses heterogeneous log formats from different vendors into a single standardized schema (e.g., mapping `src`, `src_ip`, and `ip_source` all to `source_ip`), enabling universal search queries and multi-vendor correlation rules.
5. **Answer:** NIDS monitors broad network traffic across all subnet devices but cannot see encrypted payloads; HIDS monitors host-level process creation, registry changes, and decrypted user activity directly inside the operating system.
</details>

---

## Module 6 Comprehensive Review Checklist

- [ ] **Incident Classification:** I can distinguish between Events, Alerts, and confirmed Security Incidents.
- [ ] **IR Frameworks:** I understand both NIST SP 800-61 and SANS 6-step incident response lifecycles.
- [ ] **Order of Volatility:** I can order forensic evidence collection from CPU Registers/RAM down to Archival Media (RFC 3227).
- [ ] **Packet Analysis:** I understand TCP flags (SYN, ACK, PSH, URG, FIN, RST) and port scan patterns (SYN, XMAS, NULL).
- [ ] **Wireshark & Tcpdump:** I can construct CLI `tcpdump` capture commands and apply Wireshark display filters.
- [ ] **Threat Indicators:** I can explain the difference between **IoCs** (hashes, IPs) and **IoAs** (behavior, intent).
- [ ] **Triage & RCA:** I can structure incident reports using the 5 W's and perform Root Cause Analysis via the Five Whys.
- [ ] **IDS Rule Parsing:** I can read and interpret Snort/Suricata IDS signature syntax.
- [ ] **Log Lifecycle:** I understand log ingestion, CIM normalization, and storage retention tiers.

---

## Quick Revision Summary

```
========================================================================================
                              DETECTION AND RESPONSE
========================================================================================
1. FUNNEL        : Event (Observable) -> Alert (Threshold) -> Incident (Confirmed Harm)
2. VOLATILITY    : CPU/RAM (Most volatile) -> Disk Storage -> Remote Logs -> Archive Media
3. FORENSICS     : Never reboot/unplug active host; network-isolate to preserve volatile RAM
4. TCP FLAGS     : SYN (Start), ACK (Confirm), FIN (End), RST (Abort), PSH (Push), URG (Urgent)
5. PCAP FILTERS  : Wireshark: tcp.flags.syn==1 && tcp.flags.ack==0 | tcpdump: -i eth0 -nn "port 80"
6. IoCs vs IoAs  : IoC = What happened (Static hash/IP) | IoA = How/Why (Dynamic adversary TTP)
7. SNORT RULES   : action proto src_ip src_port -> dst_ip dst_port (msg:"..."; content:"..."; sid:;)
8. RETENTION     : Hot (Active search 0-30d) -> Warm (Hunt 30-90d) -> Cold (Compliance 365d+)
========================================================================================
```