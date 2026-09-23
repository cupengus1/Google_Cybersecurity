# Module 1: Foundations of Cybersecurity

## How to Use This Module

This module establishes the foundational vocabulary, core principles, operational frameworks, and critical mindset essential for every cybersecurity professional. Rather than merely memorizing terminology, focus on understanding **how security enables and protects organizations, individuals, data, and critical systems** in modern threat landscapes.

Follow the 4-week progression sequentially. For each week:
1. Master the **core concepts** and their real-world context.
2. Review the **key terminology** with concrete examples.
3. Analyze the **real-world case scenarios**.
4. Complete the **hands-on practice tasks**.
5. Test your knowledge with the **in-depth quick checks and solutions**.

---

## Week 1: Introduction to Cybersecurity

### What You Should Understand

**Cybersecurity** is the practice of protecting digital systems, networks, hardware devices, software applications, and data from unauthorized access, exploitation, disruption, theft, or destruction. 

In an enterprise setting, an **Entry-Level Security Analyst** serves as a frontline defender. Analysts monitor operational telemetry, investigate alerts, identify vulnerabilities, contain threats, and support incident response.

#### The Core Security Mindset: Risk Reduction vs. Perfection
> [!IMPORTANT]
> **Absolute security does not exist.** 
> The objective of cybersecurity is not to eliminate 100% of risk (which is impossible without shutting down operations), but to **manage and reduce risk to an acceptable level (risk appetite)** using a balanced combination of **People, Processes, and Technology**.

```
+-------------------------------------------------------------+
|                      RISK REDUCTION TRIAD                   |
+-------------------------------------------------------------+
|  PEOPLE      -> Security awareness training, skilled analysts|
|  PROCESSES   -> Incident response playbooks, policies, SOPs |
|  TECHNOLOGY  -> Firewalls, SIEM, EDR, MFA, Encryption       |
+-------------------------------------------------------------+
```

---

### Key Concepts & Deep Dive

1. **Cybersecurity vs. Information Security (InfoSec):**
   - **Information Security (InfoSec):** The broad practice of protecting information in *all* formats (digital, physical paperwork, intellectual property, verbal communications).
   - **Cybersecurity:** A subset of InfoSec specifically focused on protecting electronic systems, digital infrastructure, networks, and data in cyberspace.

2. **The Security Analyst Lifecycle (PICERL / SOC Workflow):**
   - **Prevention:** Hardening systems, applying patches, configuring Multi-Factor Authentication (MFA).
   - **Detection:** Monitoring SIEM dashboards, triaging security alerts, identifying anomalies.
   - **Investigation:** Analyzing log artifacts, network packets, and malware indicators to determine scope.
   - **Response & Containment:** Isolating compromised endpoints, revoking credentials, blocking malicious IPs.
   - **Recovery & Lessons Learned:** Restoring clean backups and updating defense rules to prevent recurrence.

3. **Core Formula of Security Risk:**
   $$\text{Risk} = \text{Threat} \times \text{Vulnerability} \times \text{Impact (Asset Value)}$$
   - If there is no asset of value, risk is low.
   - If a vulnerability has no known exploit/threat, risk is mitigated.
   - If a threat exists but a strong control removes the vulnerability, risk is controlled.

---

### Important Terms with Real-World Examples

| Term | Definition | Real-World Example |
| :--- | :--- | :--- |
| **Asset** | Any valuable item (tangible or intangible) requiring protection. | Customer database, proprietary source code, Active Directory domain controller, employee laptops. |
| **Threat** | Any potential event or entity capable of causing harm. | Ransomware group (LockBit), unpatched zero-day exploit, malicious insider, natural flood in data center. |
| **Vulnerability** | A flaw or weakness in software, hardware, policy, or human behavior. | Unpatched CVE-2021-44228 (Log4j), default router passwords, employee susceptible to phishing. |
| **Risk** | The likelihood and consequence of a threat exploiting a vulnerability. | The financial and reputational loss resulting from customer credit card theft via an unpatched web portal. |
| **Security Posture** | The holistic security readiness, policy adherence, and defensive capability of an organization. | A posture assessed as "Mature" due to automated patching, 24/7 SOC monitoring, and zero-trust network access. |
| **SOC (Security Operations Center)** | A centralized team and facility responsible for continuously monitoring, detecting, analyzing, and responding to cyber incidents. | Tier 1 analysts triaging alerts on Splunk, escalating suspicious behavior to Tier 2 incident responders. |
| **SIEM (Security Information & Event Management)** | A platform that aggregates, correlates, and analyzes log data across an entire enterprise. | Splunk, Microsoft Sentinel, IBM QRadar, Google Chronicle. |

---

### Hands-on Practice & Application

1. **Asset-Threat-Vulnerability Mapping:**
   - Select an organization (e.g., E-commerce retail website).
   - **Asset:** Customer payment transaction database.
   - **Vulnerability:** Outdated SQL database server missing recent security patches.
   - **Threat:** External cybercriminal deploying automated SQL injection tools.
   - **Risk:** Complete exfiltration of 50,000 credit card records leading to regulatory fines (PCI-DSS) and brand damage.
2. **Everyday Defensive Controls:**
   - Identify 4 controls you use daily: Multi-Factor Authentication (MFA/2FA), Password Managers with 16+ character complex passphrases, Automatic OS Updates, and Email Phishing spam filters.

---

### Quick Check & Answers

#### Questions
1. *What is the primary objective of a security program?*
2. *How does cybersecurity differ from information security?*
3. *Why is risk management preferred over aiming for "100% impenetrable security"?*
4. *What role does a SIEM play inside a SOC?*
5. *Explain how a vulnerability differs from a threat.*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** To protect critical assets and reduce business risk to an acceptable level using people, processes, and technology, ensuring continuous operations and data confidentiality, integrity, and availability.
2. **Answer:** Information Security protects data across all media (paper documents, physical files, verbal secrets, digital data). Cybersecurity focuses specifically on digital assets, cyberspace, networks, endpoints, and cloud systems.
3. **Answer:** 100% security is technically impossible and economically impractical. Disconnecting all computers from the internet would stop cyberattacks but destroy business operations. Risk management balances security controls with operational usability.
4. **Answer:** A SIEM centralizes log telemetry from thousands of servers, firewalls, and endpoints into one platform, correlating events in real-time to generate alerts for SOC analysts.
5. **Answer:** A **vulnerability** is an internal weakness or flaw in your system (e.g., an unlocked door), whereas a **threat** is an external or internal actor/event with the potential to exploit that weakness (e.g., a burglar).
</details>

---

## Week 2: Security History and Security Domains

### What You Should Understand

Cybersecurity has evolved dramatically over several distinct eras:
1. **Mainframe Era (1970s–1980s):** Physical perimeter security was paramount; standalone systems had minimal network interconnectivity (e.g., Creeper worm, Morris worm in 1988).
2. **Client-Server & Early Internet (1990s–2000s):** Rise of web browsing, signature-based antivirus, and network firewalls. Threats shifted to mass-mailing worms (ILOVEYOU, Code Red).
3. **Targeted Attacks & APTs (2010s):** Nation-state espionage, Advanced Persistent Threats (APTs), targeted spear-phishing, and massive corporate breaches (Target, Equifax).
4. **Modern Cloud, Zero Trust & AI Era (Present):** Dispersed remote workforce, hybrid cloud (AWS/Azure/GCP), identity-centric security, ransomware-as-a-service, and AI-driven attacks.

---

### Threat Actor Taxonomy

Understanding **who** is attacking helps analysts determine motive, tactics, techniques, and procedures (TTPs):

```
+-------------------+-----------------------------+------------------------------------+
| Threat Actor      | Primary Motivation          | Typical Capabilities & TTPs        |
+-------------------+-----------------------------+------------------------------------+
| Cybercriminals    | Financial gain, extortion   | Ransomware, phishing, banking trojans|
| Nation-State/APTs | Geopolitical, espionage     | Custom zero-days, stealth, supply  |
|                   | intellectual property theft | chain attacks (e.g., SolarWinds)   |
| Insiders (Malicious| Revenge, sabotage, bribery  | Abusing legitimate credentials,    |
| / Accidental)     | / negligence, lack of care  | misconfiguring S3 cloud buckets    |
| Hacktivists       | Ideological/political cause | DDoS, web defacement, leak portals |
| Script Kiddies    | Clout, thrill-seeking       | Pre-packaged automated exploit tools|
+-------------------+-----------------------------+------------------------------------+
```

---

### The 8 Core Security Domains (CISSP Framework)

Organizations structure their defensive programs across standard domains of expertise:

```
                      +---------------------------------------+
                      |       THE 8 SECURITY DOMAINS          |
                      +---------------------------------------+
                      | 1. Security & Risk Management         |
                      | 2. Asset Security                     |
                      | 3. Security Architecture & Engineering|
                      | 4. Communication & Network Security   |
                      | 5. Identity & Access Management (IAM) |
                      | 6. Security Assessment & Testing      |
                      | 7. Security Operations (SecOps)       |
                      | 8. Software Development Security      |
                      +---------------------------------------+
```

| Domain | Focus Area | Real-World Application |
| :--- | :--- | :--- |
| **1. Security & Risk Management** | Governance, compliance, risk assessment, policies, ethics. | Establishing corporate acceptable use policies and conducting HIPAA risk audits. |
| **2. Asset Security** | Data classification, information lifecycle, encryption at rest/in transit. | Encrypting sensitive customer PII on SSDs using AES-256 and defining data retention limits. |
| **3. Security Architecture & Eng.** | Secure system design, zero trust, cryptography, threat modeling. | Implementing micro-segmentation and hardware-based root of trust (TPM). |
| **4. Communication & Network Sec.** | Firewalls, VPNs, IDS/IPS, network segmentation, protocols. | Configuring Next-Gen Firewalls (NGFW) to isolate guest Wi-Fi from internal production servers. |
| **5. Identity & Access Management** | Authentication, authorization, SSO, MFA, Least Privilege. | Enforcing Okta SSO + FIDO2 hardware keys and role-based access control (RBAC). |
| **6. Security Assessment & Testing** | Vulnerability scanning, penetration testing, red teaming. | Running weekly Nessus scans and hiring external penetration testers to audit web apps. |
| **7. Security Operations** | Incident response, SOC monitoring, digital forensics, patch management. | Triaging SIEM alerts, isolating infected endpoints using EDR, analyzing memory dumps. |
| **8. Software Development Sec.** | DevSecOps, code reviews, SAST/DAST, OWASP Top 10 mitigation. | Integrating static code analysis (SonarQube) into CI/CD pipelines to catch SQLi before release. |

---

### Core Attack Vectors

- **Malware (Malicious Software):**
  - *Ransomware:* Encrypts victim data and demands cryptocurrency payment (e.g., WannaCry, DarkSide).
  - *Trojan:* Disguises itself as legitimate software to install backdoors.
  - *Spyware/Keyloggers:* Silently records keystrokes to harvest passwords and session tokens.
  - *Rootkits:* Embeds deep in OS kernel or firmware to evade antivirus detection.
- **Social Engineering:**
  - *Phishing:* Broad, bulk deceptive emails imitating trusted institutions.
  - *Spear-Phishing:* Highly targeted emails tailored with personal research against specific employees.
  - *Whaling:* Spear-phishing specifically targeting C-level executives (CEO, CFO).
  - *Vishing / Smishing:* Voice call phishing / SMS text message phishing.
  - *Baiting / Quid Pro Quo:* Leaving infected USB drives in parking lots or offering fake IT support.

---

### Quick Check & Answers

#### Questions
1. *What distinguishes an Advanced Persistent Threat (APT) from a typical cybercriminal?*
2. *How can an insider threat occur without any malicious intent?*
3. *Which security domain directly encompasses the day-to-day operations of a SOC analyst?*
4. *What is the difference between phishing and spear-phishing?*
5. *Why is the principle of Least Privilege critical in Identity and Access Management (IAM)?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** APTs are usually nation-state backed, possess extensive resources, use stealthy custom exploits, and remain undetected inside networks for months/years for espionage, whereas typical cybercriminals seek quick financial extortion.
2. **Answer:** Through negligence or human error, such as clicking a phishing link, accidentally emailing sensitive spreadsheets to the wrong recipient, or misconfiguring a public cloud storage bucket.
3. **Answer:** **Domain 7: Security Operations (SecOps)**.
4. **Answer:** Phishing sends generic lure emails to thousands of indiscriminate targets, while spear-phishing is customized with specific names, job titles, and context targeting a single individual.
5. **Answer:** Least Privilege ensures users receive only the minimum permissions necessary to perform their job duties, preventing lateral movement and limiting blast radius if an account is compromised.
</details>

---

## Week 3: Frameworks, Controls, and Ethics

### What You Should Understand

Security does not rely on guesswork; it is governed by **standardized frameworks**, enforced through **layered controls (Defense in Depth)**, and bound by **professional ethics and privacy regulations**.

```
                +-------------------------------------------------------+
                |                    DEFENSE IN DEPTH                   |
                +-------------------------------------------------------+
                | [Policies & Awareness] -> Acceptable Use Policy       |
                |   [Perimeter Defenses] -> Firewalls, DDoS Mitigation  |
                |     [Network Controls] -> Subnetting, IDS/IPS, VPN   |
                |       [Host Defenses]  -> EDR, OS Patching, Hardening |
                |         [Application]  -> WAF, Input Sanitization     |
                |           [Data Core]  -> Encryption, DLP, Backups    |
                +-------------------------------------------------------+
```

---

### The CIA Triad: The Cornerstone of Information Security

The CIA Triad balances the three fundamental properties of secure data and systems:

```
                                  [CONFIDENTIALITY]
                                    /           \
                                   /             \
                                  /               \
                       [INTEGRITY] ---------------- [AVAILABILITY]
```

1. **Confidentiality:**
   - *Goal:* Information is accessible ONLY to authorized entities.
   - *Threats:* Data breaches, eavesdropping, shoulder surfing, privilege escalation.
   - *Controls:* End-to-end encryption (TLS 1.3, AES-256), strict Access Control Lists (ACLs), MFA.
2. **Integrity:**
   - *Goal:* Data remains accurate, complete, unaltered, and trustworthy throughout its lifecycle.
   - *Threats:* Unauthorized modifications, Man-in-the-Middle (MitM) tampering, database injection.
   - *Controls:* Cryptographic hashing (SHA-256), digital signatures, file integrity monitoring (FIM).
3. **Availability:**
   - *Goal:* Systems, networks, and applications are reliably accessible to authorized users when needed.
   - *Threats:* Distributed Denial of Service (DDoS), ransomware locking servers, hardware failures, power outages.
   - *Controls:* Redundant load balancers, RAID arrays, offsite immutable backups, disaster recovery sites.

---

### Industry Frameworks & Standards

| Framework / Standard | Governing Body | Primary Purpose & Structure |
| :--- | :--- | :--- |
| **NIST CSF 2.0** | National Institute of Standards & Technology | Flexible framework structured into 6 core functions: **Govern, Identify, Protect, Detect, Respond, Recover**. |
| **NIST SP 800-53 / 800-61** | US NIST | SP 800-53 provides catalog of security controls; SP 800-61 provides computer security incident handling guides. |
| **ISO/IEC 27001** | International Organization for Standardization | Global standard for establishing, implementing, maintaining, and certifying an Information Security Management System (ISMS). |
| **CIS Controls (v8)** | Center for Internet Security | 18 prioritized, prescriptive, and practical technical safeguards categorized into Implementation Groups (IG1, IG2, IG3). |
| **OWASP Top 10** | Open Web Application Security Project | Regularly updated awareness report ranking the top 10 most critical security risks in web applications (e.g., Broken Access Control, Injection). |

#### The NIST CSF 2.0 Functions
- **Govern (GV):** Establish cybersecurity risk management strategy, expectations, and policy oversight.
- **Identify (ID):** Inventory physical/software assets, identify vulnerabilities, assess risks.
- **Protect (PR):** Implement safeguards (IAM, awareness training, data security, platform maintenance).
- **Detect (DE):** Continuous monitoring to discover cyber events and anomalies in real time.
- **Respond (RS):** Execute incident response playbooks, mitigate threats, manage communications.
- **Recover (RC):** Restore impaired services, recover systems from backups, integrate lessons learned.

---

### Taxonomy of Security Controls

Security controls are categorized by **function (what they do)** and **implementation (how they are applied)**:

#### 1. By Function / Timing
- **Preventive:** Stops an attack before it succeeds (*e.g., firewall blocking port 23, MFA, security guards*).
- **Detective:** Identifies and alerts on active or past incidents (*e.g., SIEM alerts, IDS, audit logs, CCTV*).
- **Corrective:** Reverses damage and restores normal operations (*e.g., restoring clean backup images, terminating malware processes*).
- **Deterrent:** Discourages potential attackers (*e.g., warning banners, visible security cameras*).
- **Compensating:** Alternative measures implemented when primary controls are not feasible (*e.g., isolating a legacy Windows XP machine on a segmented VLAN*).

#### 2. By Implementation Type
- **Technical / Logical:** Hardware or software solutions (*e.g., encryption algorithms, ACLs, antivirus*).
- **Administrative / Managerial:** Organizational policies, procedures, background checks, training.
- **Physical:** Real-world barriers protecting facilities (*e.g., badge readers, server room locks, biometric gates*).

---

### Professional Ethics & Regulatory Compliance

Security analysts possess high levels of access (network traffic, private emails, user passwords, system logs). Strict ethical adherence is non-negotiable:
- **Confidentiality of Evidence:** Never disclose internal vulnerability reports or customer data.
- **Privacy Protection:**
  - **PII (Personally Identifiable Information):** SSN, home address, biometric data, phone numbers. Protected under laws like GDPR (EU) and CCPA (California).
  - **PHI (Protected Health Information):** Medical history, lab results, prescriptions. Protected under HIPAA (US).
  - **Payment Data:** Card numbers, CVVs. Governed by PCI-DSS.
- **Authorization (Rules of Engagement):** Never run scans or penetration tools against systems without explicit, written permission from the owner.

---

### Quick Check & Answers

#### Questions
1. *A company experiences a ransomware attack that encrypts its primary financial records. Which pillar of the CIA Triad is broken first, and which secondary pillar is affected?*
2. *What is the difference between a preventive control and a detective control? Give one example of each.*
3. *What are the 6 core functions of the NIST Cybersecurity Framework (CSF) 2.0?*
4. *Why must vulnerability scans only be performed with written authorization?*
5. *How does PII differ from PHI?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** **Availability** is directly broken because legitimate staff cannot access the locked data; **Integrity** is also compromised because files were maliciously altered/encrypted by an unauthorized actor.
2. **Answer:** A **preventive control** stops an incident before it occurs (e.g., Multi-Factor Authentication stopping credential stuffing), while a **detective control** identifies that an incident has occurred or is occurring (e.g., a SIEM alert flagging 50 failed login attempts).
3. **Answer:** **Govern, Identify, Protect, Detect, Respond, and Recover**.
4. **Answer:** Unauthorized scanning can trigger alerts, cause system instability/crashes, and is classified as illegal unauthorized computer access under laws such as the CFAA (Computer Fraud and Abuse Act).
5. **Answer:** **PII** is any data that identifies an individual (SSN, name, passport), whereas **PHI** specifically refers to medical/health records linked to an individual.
</details>

---

## Week 4: Common Security Tools and Analyst Skills

### What You Should Understand

A successful Security Analyst combines **technical tools** (for telemetry, correlation, and containment) with **analytical rigor, communication skills, and methodical documentation**.

```
+-------------------------------------------------------------------------+
|                       THE SOC ANALYST TOOLKIT                           |
+-------------------+-----------------------------------------------------+
| SIEM & Analytics  | Splunk, Microsoft Sentinel, IBM QRadar, Chronicle   |
| Network Defense   | Wireshark, Tcpdump, Zeek, Snort, Suricata           |
| Endpoint Security | CrowdStrike Falcon, Microsoft Defender for Endpoint |
| Vulnerability Mgt | Tenable Nessus, Qualys, OpenVAS, Nmap               |
| Automation / SOAR | Cortex XSOAR, Splunk SOAR, Tines, Shuffle           |
+-------------------+-----------------------------------------------------+
```

---

### Tool Categories & Capabilities

#### 1. SIEM (Security Information & Event Management)
- Aggregates logs from firewalls, domain controllers, cloud platforms, and applications.
- Performs real-time correlation (e.g., flagging when a user logs in from New York and Tokyo within 5 minutes).

#### 2. Network Monitoring: IDS vs. IPS
```
                [Incoming Network Traffic]
                            |
           +----------------+----------------+
           |                                 |
           v                                 v
     [Passive Tap]                     [Inline Path]
           |                                 |
        [ IDS ]                           [ IPS ]
(Detects & Alerts only)             (Inspects & Drops / Blocks)
```
- **IDS (Intrusion Detection System):** Operates out-of-band; monitors traffic copies and generates alerts without slowing down traffic.
- **IPS (Intrusion Prevention System):** Operates inline; actively inspects and drops malicious packets in real time.

#### 3. EDR (Endpoint Detection and Response)
- Installed directly on endpoints (laptops, servers, VMs).
- Continuously records process executions, registry edits, network sockets, and file creations.
- Enables remote response actions: isolating host from network, killing malicious processes, extracting memory artifacts.

---

### Log Analysis Essentials

Logs are the objective factual record of digital events. Analysts inspect standard fields:
- **Timestamp:** UTC standard format (e.g., `2026-09-23T09:12:04Z`).
- **Source IP & Port:** Origin of the connection (e.g., `198.51.100.24:54321`).
- **Destination IP & Port:** Target server and service (e.g., `10.0.1.50:443`).
- **User Identity / Account Name:** Account executing the action.
- **Event ID / Action / Status:** Success (`200 OK`, Event ID `4624`) vs. Failure (`401 Unauthorized`, Event ID `4625`).

#### Example: Analyzing a Suspicious Web Server Access Log
```log
192.168.1.105 - - [23/Sep/2026:09:15:32 +0000] "GET /login.php HTTP/1.1" 200 4520
192.168.1.105 - - [23/Sep/2026:09:15:35 +0000] "POST /login.php' OR '1'='1 HTTP/1.1" 500 234
```
> [!NOTE]
> **Analysis:** The second request shows an attacker attempting a **SQL Injection (`' OR '1'='1`)** payload against the login endpoint, returning an HTTP 500 server error.

---

### Detection Accuracy: Confusion Matrix

When fine-tuning security alerts, analysts balance sensitivity and specificity:

```
                            ACTUAL REALITY
                     MALICIOUS           BENIGN
                +-------------------+-------------------+
ALERT TRIGGERED |  TRUE POSITIVE    |  FALSE POSITIVE   |
                | (Real attack,     | (Benign action,   |
                |  properly caught) |  false alarm)     |
                +-------------------+-------------------+
NO ALERT        |  FALSE NEGATIVE   |  TRUE NEGATIVE    |
                | (Real attack      | (Normal activity, |
                |  missed - DANGER!)|  no alert fired)  |
                +-------------------+-------------------+
```
- **False Positive:** Legitimate developer activity triggers an alert (causes alert fatigue).
- **False Negative:** An actual malicious attacker bypasses detection undetected (**highest security risk**).

---

### Incident Documentation & The 5 W's

When handling an alert, an analyst must document findings using the **5 W's Framework**:
1. **Who:** Which user account, asset, or threat actor was involved?
2. **What:** What specific malicious actions or tools were executed?
3. **When:** Exact timestamp (with timezone) when the activity started and stopped.
4. **Where:** Source/destination hostnames, IP addresses, internal subnets, or cloud environments.
5. **Why / How:** Root cause (e.g., unpatched vulnerability, compromised session cookie).

---

### Quick Check & Answers

#### Questions
1. *What is the fundamental difference between an IDS and an IPS?*
2. *Why are False Negatives considered far more dangerous than False Positives?*
3. *What key telemetry does an EDR tool capture that a network firewall cannot see?*
4. *What is the role of an Incident Response Playbook?*
5. *Why is time synchronization (NTP) crucial when investigating logs across multiple systems?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** An IDS sits out-of-band and only detects/alerts on suspicious traffic without stopping it. An IPS sits directly inline and can automatically drop or block malicious traffic in real time.
2. **Answer:** A False Positive merely causes wasted analyst triage time, whereas a False Negative means an active intrusion is occurring inside the network completely undetected.
3. **Answer:** EDR monitors local endpoint telemetry: process creation trees, parent-child process relationships, DLL injections, registry modifications, memory dumps, and local file modifications.
4. **Answer:** A playbook provides pre-defined, standardized step-by-step procedures for investigating and containing specific attack types (e.g., Phishing Playbook, Ransomware Playbook), ensuring consistent and rapid response.
5. **Answer:** Accurate NTP synchronization ensures timestamps across firewalls, domain controllers, and servers align perfectly, allowing analysts to construct a precise, chronological timeline of an attacker's lateral movement.
</details>

---

## Module 1 Comprehensive Review Checklist

Use this self-assessment checklist before advancing to **Module 2: Manage Security Risks**:

- [ ] **Core Vocabulary:** I can clearly articulate the distinct differences between **Assets, Threats, Vulnerabilities, and Risk**.
- [ ] **Security Mindset:** I understand why 100% security is impossible and how organizations manage **acceptable risk** via people, processes, and technology.
- [ ] **CIA Triad:** I can explain Confidentiality, Integrity, and Availability and identify real-world controls mapped to each pillar.
- [ ] **Security Domains:** I can name the 8 CISSP security domains and explain how Security Operations (SecOps) interacts with them.
- [ ] **Threat Actors:** I can differentiate between Cybercriminals, Nation-State APTs, Insiders, and Hacktivists based on capability and motivation.
- [ ] **Frameworks:** I understand the purpose and 6 core functions of **NIST CSF 2.0** (Govern, Identify, Protect, Detect, Respond, Recover) as well as CIS Controls, ISO 27001, and OWASP Top 10.
- [ ] **Control Types:** I can classify controls by timing (Preventive, Detective, Corrective) and implementation (Technical, Administrative, Physical).
- [ ] **Core SOC Tools:** I know the operational purpose of **SIEM, IDS, IPS, EDR, and Firewalls**.
- [ ] **Alert Accuracy:** I can explain True Positives, False Positives, False Negatives, and True Negatives.
- [ ] **Ethics & Privacy:** I understand legal and ethical boundaries, including the handling of PII/PHI and obtaining authorization for security testing.

---

## Quick Revision Summary

```
========================================================================================
                              FOUNDATIONS OF CYBERSECURITY
========================================================================================
1. DEFINITION    : Protecting digital systems, data, and networks from unauthorized harm.
2. GOAL          : Risk Reduction (Risk = Threat x Vulnerability x Asset Impact).
3. TRIAD         : Confidentiality (Secret), Integrity (Accurate), Availability (Accessible).
4. FRAMEWORKS    : NIST CSF 2.0 (Govern, Identify, Protect, Detect, Respond, Recover), CIS, ISO 27001.
5. CONTROLS      : Preventive, Detective, Corrective | Technical, Administrative, Physical.
6. SOC TOOLKIT   : SIEM (Log analytics), IDS/IPS (Network), EDR (Endpoint telemetry), Firewalls.
7. ACCURACY      : True/False Positives vs. True/False Negatives (False Negatives are most severe).
8. ETHICS        : Protect PII & PHI; never scan or test without written authorization.
========================================================================================
```