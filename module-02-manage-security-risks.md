# Module 2: Play It Safe - Manage Security Risks

## How to Use This Module

This module focuses on **Cybersecurity Risk Management**—the operational backbone of all strategic security decisions. Organizations cannot eliminate all vulnerabilities or defend against every threat simultaneously; risk management provides the analytical framework to **identify, evaluate, prioritize, treat, and monitor** risks based on business impact and threat probability.

Follow the 4-week progression:
1. **Week 1:** Threat, Vulnerability, and Risk Modeling & Assessment (Quantitative vs. Qualitative).
2. **Week 2:** Security Frameworks, Control Taxonomies, and Audit Gaps.
3. **Week 3:** SIEM Telemetry, Log Correlation, and Alert Triage.
4. **Week 4:** Incident Response Lifecycles, Playbooks, and Post-Incident Reviews.

---

## Week 1: Security Domains, Threats, Risks, and Vulnerabilities

### What You Should Understand

Security risk exists at the intersection of **valuable assets**, **exploitable vulnerabilities**, and **active threat actors**. A security analyst must translate technical findings into actionable risk assessments so stakeholders can make informed financial, operational, and architectural decisions.

```
                     +-----------------------------------+
                     |       THE RISK INTERSECTION       |
                     +-----------------------------------+
                     |                                   |
                     |     [ THREAT ]     [ VULNERABILITY ]
                     |          \             /          |
                     |           \           /           |
                     |             v       v             |
                     |          [ SECURITY RISK ]        |
                     |                 |                 |
                     |                 v                 |
                     |          [ ASSET IMPACT ]         |
                     +-----------------------------------+
```

---

### Risk Calculation & Methodologies

Risk assessment is conducted using two primary methodologies:

#### 1. Qualitative Risk Analysis
- Uses subjective rating scales (e.g., **Low, Medium, High, Critical** or a $5 \times 5$ matrix) based on likelihood and organizational impact.
- **Likelihood:** Probability that a threat will exploit a vulnerability (Rare $\rightarrow$ Almost Certain).
- **Impact:** Severity of damage (Insignificant $\rightarrow$ Catastrophic).

```
                      QUALITATIVE RISK MATRIX (5x5)
          +---------------+-----+-----+-----+-----+-----+
          | Catastrophic  | Med | Med | High| Crit| Crit|
          | Major         | Low | Med | High| High| Crit|
   IMPACT | Moderate      | Low | Med | Med | High| High|
          | Minor         | Low | Low | Med | Med | High|
          | Insignificant | Low | Low | Low | Low | Med |
          +---------------+-----+-----+-----+-----+-----+
                          | Rare| Unlk| Poss| Prob| Alms|
                          +-----------------------------+
                                     LIKELIHOOD
```

#### 2. Quantitative Risk Analysis
- Assigns specific numerical and monetary values to risk:
  - **Asset Value (AV):** Total monetary worth of the asset ($).
  - **Exposure Factor (EF):** Percentage of asset lost if an incident occurs (0% to 100%).
  - **Single Loss Expectancy (SLE):** $\text{SLE} = \text{AV} \times \text{EF}$
  - **Annualized Rate of Occurrence (ARO):** Estimated frequency of the incident per year.
  - **Annualized Loss Expectancy (ALE):** $\text{ALE} = \text{SLE} \times \text{ARO}$

> [!TIP]
> **Example Calculation:**
> An e-commerce database is valued at \$500,000 ($\text{AV}$). A ransomware incident is estimated to cause a 40% loss ($\text{EF} = 0.40$).
> $$\text{SLE} = \$500,000 \times 0.40 = \$200,000$$
> If this incident is expected to happen once every two years ($\text{ARO} = 0.5$):
> $$\text{ALE} = \$200,000 \times 0.5 = \$100,000 \text{ per year}$$
> **Decision Rule:** A security control costing \$30,000/year to prevent this is economically justified (\$30,000 < \$100,000).

---

### The 4 Core Risk Treatment Strategies

When a risk is identified and rated, management must select an official treatment strategy:

```
+------------------+--------------------------------------------------------------+-------------------------------------------+
| Strategy         | Definition                                                   | Real-World Example                        |
+------------------+--------------------------------------------------------------+-------------------------------------------+
| 1. Avoidance     | Eliminating the risk entirely by stopping the activity.      | Deciding not to store customer credit     |
|                  |                                                              | cards on-premise; outsourcing to Stripe.  |
| 2. Mitigation    | Implementing security controls to reduce likelihood/impact.  | Enforcing MFA, patch management, and EDR. |
| 3. Transfer      | Shifting the financial burden/liability to a third party.    | Purchasing cyber insurance or outsourcing |
|                  |                                                              | datacenter hosting to AWS under SLA.      |
| 4. Acceptance    | Acknowledging the risk without controls (within appetite).   | Accepting the risk of minor website spam  |
|                  |                                                              | because fixing it costs more than impact. |
+------------------+--------------------------------------------------------------+-------------------------------------------+
```

---

### Sample Risk Register

A **Risk Register** is an authoritative central repository tracking all identified security risks across an organization:

| Risk ID | Asset Affected | Threat & Vulnerability Description | Likelihood | Impact | Inherent Risk | Treatment Strategy | Proposed Control | Residual Risk | Control Owner |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **RSK-001** | Employee Laptops | Phishing email steals credentials due to lack of MFA | High | High | **Critical** | Mitigate | Deploy FIDO2 hardware MFA & Phishing training | **Low** | IAM Team |
| **RSK-002** | Customer Portal | DDoS attack takes web server down due to lack of CDN | Medium | High | **High** | Transfer / Mitigate | Deploy Cloudflare DDoS scrubbing & WAF | **Low** | NetOps |
| **RSK-003** | Legacy Payroll App | SQL injection vulnerability in unmaintainable legacy code | Low | High | **Medium** | Mitigate / Accept | Place behind ModSecurity WAF, budget replacement | **Low** | SecOps / HR |

---

### Quick Check & Answers

#### Questions
1. *What is the difference between Inherent Risk and Residual Risk?*
2. *If an organization purchases a \$5,000,000 cyber insurance policy, which risk treatment strategy are they applying?*
3. *What is "Risk Appetite" and how does it influence executive security decisions?*
4. *Calculate the ALE: Asset Value = \$1,000,000, Exposure Factor = 20%, Estimated frequency = once every 4 years.*
5. *Why is a Risk Register considered a living document rather than a one-time project?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** **Inherent Risk** is the raw level of risk present before any security controls or safeguards are applied. **Residual Risk** is the remaining risk that exists *after* security controls have been implemented.
2. **Answer:** **Risk Transfer (or Transference)**.
3. **Answer:** **Risk Appetite** is the amount and type of risk an organization is willing to accept in pursuit of its business objectives. It sets the boundary for which risks must be mitigated versus accepted.
4. **Answer:** 
   $$\text{SLE} = \$1,000,000 \times 0.20 = \$200,000$$
   $$\text{ARO} = \frac{1}{4} = 0.25$$
   $$\text{ALE} = \$200,000 \times 0.25 = \$50,000 \text{ per year}$$
5. **Answer:** Threats, vulnerabilities, business operations, and software architectures constantly evolve. A risk register must be continuously updated to reflect newly discovered vulnerabilities, decommissioned assets, and changing regulatory demands.
</details>

---

## Week 2: Frameworks, Controls, and Security Audits

### What You Should Understand

Frameworks provide the structured blueprint for defensive programs. **Security Audits** provide independent, evidence-based verification that policies, controls, and configurations actually exist and operate effectively as designed.

```
  [SECURITY FRAMEWORK]  ---> Defines what security outcomes are required (NIST, ISO)
           |
           v
  [SECURITY CONTROLS]    ---> Concrete technical & procedural safeguards implemented
           |
           v
  [SECURITY AUDIT]       ---> Independent evaluation to verify controls via evidence
           |
           v
  [GAP ANALYSIS / POA&M] ---> Plan of Action & Milestones to remediate missing controls
```

---

### Key Security Frameworks & Regulatory Mandates

| Framework / Regulation | Scope & Authority | Primary Target & Mechanism |
| :--- | :--- | :--- |
| **NIST SP 800-30** | US Federal / Global Best Practice | *Risk Assessment Guide:* Framework specifically guiding how to conduct formal risk assessments. |
| **NIST SP 800-53** | US Federal Information Systems | Comprehensive catalog of over 1,000 security and privacy controls organized into 20 control families (AC, AU, IA, SC, etc.). |
| **ISO/IEC 27001 / 27002** | International Standard | Global certification standard for establishing, running, and auditing an Information Security Management System (ISMS). |
| **PCI-DSS (v4.0)** | Payment Card Industry Security Standards Council | 12 mandatory technical and operational requirements for entities handling credit/debit cardholder data. |
| **HIPAA Security Rule** | US Healthcare Regulation | Administrative, physical, and technical safeguards to protect electronic Protected Health Information (ePHI). |
| **GDPR** | European Union Regulation | Data privacy directive granting users data sovereignty; imposes strict breach notification timelines (within 72 hours). |

---

### The Anatomy of a Security Audit

An audit is not a test of memory—it is an **evidence-driven examination**.

```
+--------------------------------------------------------------------------------+
|                            THE AUDIT LIFECYCLE                                 |
+--------------------------------------------------------------------------------+
| 1. Scoping & Planning  -> Define target systems, networks, and compliance goals |
| 2. Evidence Collection -> Gather screenshots, logs, configs, policies, tickets |
| 3. Control Testing     -> Verify if controls work in practice (sample testing)  |
| 4. Gap Analysis        -> Compare actual state against framework requirements  |
| 5. Reporting & POA&M   -> Issue formal audit report and remediation roadmap     |
+--------------------------------------------------------------------------------+
```

#### Types of Audit Evidence:
- **Direct Inspection:** Reviewing active firewall rules, Active Directory password policies, AWS IAM policies.
- **Artifact Examination:** Reviewing signed employee NDA forms, change management approval tickets, vulnerability scan reports.
- **Interviews:** Inquiring with SOC analysts and system administrators about standard operational procedures.
- **Technical Observation:** Witnessing a disaster recovery tabletop exercise or simulated backup restoration test.

---

### Quick Check & Answers

#### Questions
1. *What is a "Control Gap" and why is it significant during an audit?*
2. *How does an internal audit differ from an external regulatory audit?*
3. *Give an example of how an administrative control and a technical control work together.*
4. *Under PCI-DSS, what is the primary asset that must be protected?*
5. *What is a Plan of Action and Milestones (POA&M)?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** A **control gap** is a missing, improperly configured, or ineffective control required by policy or compliance standards. It represents an unmitigated vulnerability that could be exploited.
2. **Answer:** An **internal audit** is conducted by an organization's own internal audit/security team to proactively identify weaknesses before official reviews. An **external audit** is conducted by an independent third-party auditor to issue certifications (e.g., SOC 2, ISO 27001) or regulatory compliance validations.
3. **Answer:** An **administrative policy** mandates that all employees must lock their screens when leaving their desks; a **technical control** automatically locks the Windows workstation after 5 minutes of inactivity via Group Policy (GPO).
4. **Answer:** **Cardholder Data (CHD)** and **Sensitive Authentication Data (SAD)** (e.g., PAN, expiration dates, CVVs).
5. **Answer:** A **POA&M** is a structured document that tracks corrective actions, assigned owners, resource requirements, and completion deadlines for fixing identified security weaknesses.
</details>

---

## Week 3: SIEM Tools and Security Monitoring

### What You Should Understand

Modern organizations generate gigabytes of log telemetry every hour. Without a **SIEM (Security Information and Event Management)**, locating indicators of compromise across dispersed systems is impossible. A SIEM acts as the central brain of a SOC.

```
 [ FIREWALL LOGS ] ----+
 [ DOMAIN CONTROLLER ] -+
 [ EDR AGENTS ] --------+---> [ SIEM INGESTION PIPELINE ] ---> [ CORRELATION ENGINE ] ---> [ ALERTS & DASHBOARDS ]
 [ CLOUD AUDIT LOGS ] --+     (Parsing, Indexing, Normalizing)  (Detection Rules)          (SOC Analyst Triage)
 [ WEB APP ACCESS ] ----+
```

---

### Critical Log Sources & Windows Event IDs

A security analyst must recognize critical log sources and standard event identifiers:

#### 1. Windows Security Event Log IDs
| Event ID | Event Description | Security Significance |
| :--- | :--- | :--- |
| **4624** | An account was successfully logged on | Validates legitimate user access or lateral movement. |
| **4625** | An account failed to log on | Multiple occurrences indicate brute-force or credential stuffing. |
| **4672** | Special privileges assigned to new logon | Admin/elevated privilege logon (Administrator root access). |
| **4720** | A user account was created | Detects unauthorized backdoor account creation by an attacker. |
| **4726** | A user account was deleted | Tracks cleanup actions or insider sabotage. |
| **1102** | The audit log was cleared | High-priority alert: Attacker attempting to cover their tracks. |

#### 2. Linux Syslog & Auth Logs
- Located in `/var/log/auth.log` (Debian/Ubuntu) or `/var/log/secure` (RHEL/CentOS).
- Tracks `sshd` connections, `sudo` elevation commands, and PAM authentication attempts.

#### 3. Network & Cloud Logs
- **Firewall & NetFlow:** Source/destination IPs, ports, bytes transferred, accept/drop actions.
- **DNS Logs:** Queries made to external domains (detects Command & Control [C2] beaconing and DNS tunneling).
- **CloudTrail / Azure Activity Logs:** API calls made to spin up cloud infrastructure or alter IAM permissions.

---

### Correlation Rules & SIEM Queries

A SIEM transforms isolated log entries into actionable security alerts via **correlation rules**.

```
LOG 1: 50 failed logins on workstation WS-04 within 60 seconds (Event ID 4625)
LOG 2: 1 successful login on WS-04 right after (Event ID 4624)
LOG 3: Outbound network connection to an unknown IP on port 4444 (Meterpreter reverse shell)
------------------------------------------------------------------------------------------
CORRELATION ALERT GENERATED: "Brute Force Success followed by Suspicious Outbound Connection"
```

#### Example: Splunk Search Processing Language (SPL)
```spl
index=windows sourcetype="WinEventLog:Security" EventCode=4625
| stats count by user, src_ip
| where count > 10
| sort - count
```
*Explanation:* Searches Windows security logs for failed login attempts, counts occurrences per user and source IP, and displays results where failed attempts exceed 10.

---

### Alert Triage Workflow

```
+--------------------------------------------------------------------------------+
|                             SOC TRIAGE WORKFLOW                                |
+--------------------------------------------------------------------------------+
| 1. Ingestion: Alert lands in the SOC queue with a severity score (Low/Med/High)|
| 2. Verification: Analyst inspects raw logs to determine if alert is valid      |
| 3. Contextualization: Gather user identity, host IP, asset criticality, history|
| 4. Classification:                                                             |
|    - True Positive (Benign): Authorized admin activity -> Document & Close     |
|    - True Positive (Malicious): True Incident -> Trigger Playbook & Escalate   |
|    - False Positive: Normal business noise -> Recommend rule tuning to Tier 3  |
+--------------------------------------------------------------------------------+
```

---

### Quick Check & Answers

#### Questions
1. *What Windows Event ID triggers when an attacker attempts to clear the audit log?*
2. *What is the difference between log aggregation and log correlation?*
3. *Why are DNS logs valuable for detecting Command and Control (C2) communication?*
4. *What causes "Alert Fatigue" in a SOC, and how is it mitigated?*
5. *Why is log normalization necessary when ingesting data from multiple vendors into a SIEM?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** **Event ID 1102** (The audit log was cleared).
2. **Answer:** **Log Aggregation** is the process of collecting logs from multiple different devices into a central server. **Log Correlation** applies logic and rules to connect related events across those different logs to uncover complex attack patterns.
3. **Answer:** Infected endpoints inside a private subnet must resolve external domain names to connect to attacker servers. High-frequency queries, newly registered domains, or encoded data strings in subdomains reveal C2 beaconing and DNS tunneling.
4. **Answer:** Alert fatigue occurs when analysts are overwhelmed by a high volume of false positives, leading to burnout and missed true incidents. It is mitigated by tuning correlation rules, whitelisting benign behaviors, and implementing SOAR automation.
5. **Answer:** Different devices format logs differently (e.g., one uses `src_ip`, another uses `SourceAddress`). Normalization converts all log fields into a unified schema (e.g., Common Information Model - CIM) so a single query can search across all sources.
</details>

---

## Week 4: Playbooks and Incident Response

### What You Should Understand

When an active security breach occurs, chaos and panic are the enemy. **Incident Response (IR)** provides the structured methodology, and **Playbooks** provide the tactical step-by-step instructions to contain and remediate security events rapidly.

```
                      +---------------------------------------+
                      |      NIST SP 800-61 IR LIFECYCLE      |
                      +---------------------------------------+
                      | 1. Preparation                        |
                      |          |                            |
                      |          v                            |
                      | 2. Detection & Analysis               |
                      |          |                            |
                      |          v                            |
                      | 3. Containment, Eradication, Recovery |
                      |          |                            |
                      |          v                            |
                      | 4. Post-Incident Activity (Lessons)   |
                      +---------------------------------------+
```

---

### The 6-Phase Incident Response Framework (NIST / SANS)

1. **Preparation:** Hardening systems, deploying tools, training CSIRT (Computer Security Incident Response Team), establishing communication chains, and building playbooks *before* an incident occurs.
2. **Detection & Analysis:** Triaging alerts, verifying indicators of compromise (IoCs), identifying attack scope, and determining the severity level.
3. **Containment:**
   - *Short-Term Containment:* Isolating infected machines from the network, disabling compromised user accounts, blocking malicious external IPs on firewalls.
   - *Long-Term Containment:* Applying temporary patches, changing root passwords, setting up clean segmented zones.
4. **Eradication:** Removing all traces of malware, deleting backdoors, closing compromised ports, and cleaning registry persistence keys.
5. **Recovery:** Restoring systems from known clean, validated backups, returning endpoints to production, and performing continuous enhanced monitoring.
6. **Post-Incident Activity (Lessons Learned):** Conducting a retrospective meeting with all stakeholders: What went well? What failed? How can detection rules be updated to prevent recurrence?

---

### Tactical Playbook: Suspicious Phishing Email Investigation

```
[Phishing Alert Received]
       |
       v
1. Inspect Email Headers -------------------> Check SPF, DKIM, DMARC validation & Sender IP
       |
       v
2. Extract Artifacts -----------------------> Extract URLs, domains, attachments, message subject
       |
       v
3. Analyze Artifacts (Sandbox/VirusTotal) --> Is the attachment malicious? Is URL a credential harvester?
       |
       +------------+
       |            |
       v (Malicious)| (Benign)
4. Execute Actions  +-----------------------> 4b. False Positive: Whitelist & Close ticket
       |
       +--> Search SIEM: Who else received this email across the organization?
       +--> Mail Server: Purge malicious email from all employee inboxes globally.
       +--> Firewall/DNS: Block malicious destination domain/IP.
       +--> Endpoint: If clicked, isolate workstation & force password reset on compromised account.
```

---

### Incident Escalation & Communication Matrix

Not all incidents are equal. Standard operational procedures define clear escalation thresholds:

| Severity Level | Criteria | Required Response & Escalation |
| :--- | :--- | :--- |
| **Low (P3/P4)** | Isolated adware, single phishing email blocked by filter, minor policy violation. | Tier 1 SOC Analyst triages and resolves within standard ticketing SLA. |
| **Medium (P2)** | Single non-critical workstation infected with trojan; lateral movement blocked. | Escalated to Tier 2 Incident Responder; host isolated; manager notified. |
| **Critical (P1)** | Active ransomware spreading; domain controller compromised; PII database exfiltrated. | CSIRT activated; CISO, Legal Counsel, Executive Board, PR, and law enforcement notified immediately. |

---

### Quick Check & Answers

#### Questions
1. *What is the difference between Containment and Eradication in incident response?*
2. *Why should an infected workstation be network-isolated rather than immediately turned off / powered down?*
3. *What are the three email authentication mechanisms checked during phishing analysis?*
4. *Why is the "Lessons Learned" phase critical for long-term risk management?*
5. *What is the role of the Chain of Custody during digital forensics investigations?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** **Containment** stops the attack from spreading further (e.g., disconnecting a machine from the network), whereas **Eradication** locates and completely destroys the root cause and artifacts of the malware from the contained systems.
2. **Answer:** Powering down a computer wipes volatile memory (RAM), destroying critical forensic evidence such as running malware processes, unencrypted memory passwords, active network sockets, and decryption keys.
3. **Answer:** **SPF (Sender Policy Framework)**, **DKIM (DomainKeys Identified Mail)**, and **DMARC (Domain-based Message Authentication, Reporting, and Conformance)**.
4. **Answer:** It identifies weaknesses in policies, tool gaps, or delayed response times during the incident, allowing the organization to update playbooks, refine detection rules, and allocate budget to prevent identical attacks in the future.
5. **Answer:** **Chain of Custody** is the rigorous documentation tracking every person who collected, transferred, analyzed, and stored digital evidence, ensuring evidence remains admissible in court without tampering claims.
</details>

---

## Module 2 Comprehensive Review Checklist

- [ ] **Risk Modeling:** I can calculate Quantitative Risk metrics ($\text{SLE}, \text{ARO}, \text{ALE}$) and construct a Qualitative $5 \times 5$ Risk Matrix.
- [ ] **Risk Treatments:** I can distinguish between **Risk Mitigation, Acceptance, Transfer, and Avoidance** with realistic business examples.
- [ ] **Risk Register:** I can maintain and interpret a corporate Risk Register.
- [ ] **Frameworks & Regulations:** I understand NIST SP 800-30, NIST SP 800-53, ISO 27001, PCI-DSS, HIPAA, and GDPR compliance mandates.
- [ ] **Audits & Evidence:** I understand the audit lifecycle, gap analysis, and the distinction between technical, administrative, and physical controls.
- [ ] **Windows Security Logs:** I know critical Windows Event IDs (**4624, 4625, 4720, 1102**).
- [ ] **SIEM Operations:** I understand log aggregation, normalization, correlation logic, and alert triage.
- [ ] **IR Lifecycle:** I can walk through the 6 phases of NIST/SANS Incident Response (**Preparation $\rightarrow$ Lessons Learned**).
- [ ] **Playbook Execution:** I can articulate the step-by-step workflow for phishing investigation and host isolation.

---

## Quick Revision Summary

```
========================================================================================
                               MANAGE SECURITY RISKS
========================================================================================
1. RISK EQUATION : SLE = AV x EF  |  ALE = SLE x ARO
2. 4 STRATEGIES  : Avoid (stop), Mitigate (control), Transfer (insure), Accept (tolerate)
3. AUDIT TYPES   : Evidence-based verification; gap analysis yields POA&M action plans
4. EVENT IDs     : 4624 (Success Logon), 4625 (Fail Logon), 4720 (User Created), 1102 (Log Clear)
5. SIEM VALUE    : Ingests, normalizes, correlates logs across enterprise to fire high-fidelity alerts
6. IR PHASES     : Prep -> Detection & Analysis -> Containment -> Eradication -> Recovery -> Lessons
7. PLAYBOOKS     : Standardized, step-by-step tactical workflows ensuring consistent response
========================================================================================
```