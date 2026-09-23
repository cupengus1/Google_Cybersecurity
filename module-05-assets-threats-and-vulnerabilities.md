# Module 5: Assets, Threats, and Vulnerabilities

## How to Use This Module

This module connects the foundational triumvirate of cybersecurity: **what we protect (Assets)**, **the weaknesses that expose them (Vulnerabilities)**, and **the adversaries and attack vectors seeking to exploit them (Threats)**. Modern security analysts do not simply run scans—they contextualize risk, model threat actor behavior using standard frameworks (STRIDE, MITRE ATT&CK), and orchestrate vulnerability remediation lifecycles.

Follow the 4-week progression:
1. **Week 1:** Asset Management, Data Classification, Asset Lifecycles, and Data Sanitization (NIST SP 800-88).
2. **Week 2:** Cryptographic Controls (Symmetric, Asymmetric, Hashing), Data States, and the AAA Identity Framework.
3. **Week 3:** Vulnerability Management Lifecycles, CVSS Scoring, EPSS, CISA KEV, and Remediation Prioritization.
4. **Week 4:** Malware Taxonomies, Social Engineering Psychology, the Pyramid of Pain, and STRIDE Threat Modeling.

---

## Week 1: Assets and Asset Security

### What You Should Understand

You cannot protect what you do not know exists. An accurate, real-time **Asset Inventory** is the prerequisite for all vulnerability management, threat detection, and compliance auditing. 

Unmanaged hardware, unapproved cloud subscriptions, and forgotten virtual machines represent **Shadow IT**, which expands an organization's attack surface without security oversight.

---

### Asset Categories & The CMDB

Organizations maintain a **Configuration Management Database (CMDB)** to track relationships between assets and business processes:

```
                      +---------------------------------------+
                      |       ENTERPRISE ASSET SPECTRUM       |
                      +---------------------------------------+
                      | 1. Information / Data Assets          |
                      | 2. Software / Application Assets      |
                      | 3. Hardware / Physical Infrastructure |
                      | 4. Cloud & Virtual Infrastructure     |
                      | 5. Human & Operational Capital        |
                      +---------------------------------------+
```

---

### Data Classification Schemes

Organizations classify data to ensure proportionate security controls are applied:

| Classification Level | Definition & Sensitivity | Access Restrictions & Controls | Real-World Examples |
| :--- | :--- | :--- | :--- |
| **1. Public** | Information freely shareable with the general public. No business impact if disclosed. | No restrictions; public website hosting. | Marketing brochures, published price lists, press releases. |
| **2. Internal** | Information intended for internal employee use only. Minor operational disruption if leaked. | Role-based internal intranet access; standard corporate login. | Internal employee directories, corporate policies, internal wikis. |
| **3. Confidential** | Sensitive business data. Significant financial, legal, or competitive damage if disclosed. | Strict need-to-know, encryption at rest/transit, signed NDAs. | Source code repositories, financial forecasts, vendor contracts. |
| **4. Restricted / Secret** | Highly regulated data. Catastrophic legal, regulatory, or existential harm if breached. | MFA, hardware tokens, audited access, isolated vaults, DLP enforcement. | Customer PII/Credit card numbers, patient health records (PHI), encryption private keys. |

---

### Asset Lifecycle & Secure Data Sanitization (NIST SP 800-88)

Assets must be tracked through all five lifecycle phases:

```
  [1. Acquisition] ---> [2. Deployment] ---> [3. Maintenance] ---> [4. Decommission] ---> [5. Disposal]
   (Procurement &         (Hardening &          (Patching &           (Retirement &         (Data Sanitization
    Risk Review)           Inventory Tag)        Monitoring)           Isolation)            & Recycling)
```

#### NIST SP 800-88 Media Sanitization Standards:
1. **Clear:** Logical overwrite using software tools (e.g., writing zeroes/random bits across all sectors) to prevent simple keyboard recovery.
2. **Purge:** Cryptographic erasure (destroying encryption keys) or degaussing (magnetic field disruption) preventing lab-grade recovery.
3. **Destroy:** Physical destruction making data recovery impossible (shredding, incineration, pulverization).

---

### Quick Check & Answers

#### Questions
1. *What is Shadow IT and why does it represent a major security risk?*
2. *Under NIST SP 800-88, what is the difference between "Clearing" and "Purging" storage media?*
3. *Why should customer credit card data be classified as "Restricted" rather than "Internal"?*
4. *What role does a Configuration Management Database (CMDB) play in security operations?*
5. *What is Cryptographic Erasure (Crypto-Shredding)?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** Shadow IT refers to hardware, software, cloud services, or SaaS applications used within an organization without explicit IT/Security approval. It bypasses security monitoring, vulnerability patching, and compliance controls.
2. **Answer:** **Clearing** overwrites data with standard read/write commands (protects against simple software recovery); **Purging** applies advanced physical/logical techniques (like degaussing or crypto-erasure) that prevent recovery even using specialized forensic laboratory tools.
3. **Answer:** Credit card data is strictly governed by PCI-DSS and legal mandates; a breach leads to severe regulatory fines, lawsuits, and catastrophic reputational damage, requiring maximum isolation and encryption.
4. **Answer:** A CMDB maintains an authoritative map of all hardware, software, and cloud assets, detailing their dependencies, software versions, assigned owners, and business criticality.
5. **Answer:** It is a data sanitization technique where the encryption keys used to encrypt data are securely deleted, rendering the underlying ciphertext mathematically impossible to decrypt.
</details>

---

## Week 2: Data Protection, Encryption, and Access Control

### What You Should Understand

Data must be protected across all operational states using a combination of **cryptographic mechanisms** and robust **Identity and Access Management (IAM)** architectures.

---

### The Three States of Data

```
+-----------------------------------+-----------------------------------+-----------------------------------+
| 1. DATA AT REST                   | 2. DATA IN TRANSIT                | 3. DATA IN USE                    |
+-----------------------------------+-----------------------------------+-----------------------------------+
| Data stored on physical or cloud  | Data traversing local or public   | Data residing in volatile system  |
| storage (HDDs, SSDs, SANs, S3).   | networks across wire/RF.          | memory (RAM, CPU registers/cache).|
| *Controls:* Full Disk Encryption  | *Controls:* TLS 1.3, IPsec VPN,   | *Controls:* Confidential Computing|
| (BitLocker, LUKS), AES-256.       | SSH, HTTPS, SFTP.                 | (SGX enclaves), memory isolation. |
+-----------------------------------+-----------------------------------+-----------------------------------+
```

---

### Cryptography: Encryption vs. Hashing vs. Encoding

```
                      +---------------------------------------+
                      |         CRYPTOGRAPHIC SPECTRUM        |
                      +---------------------------------------+
                      | 1. Symmetric Encryption (Shared Key)  |
                      | 2. Asymmetric Encryption (Key Pair)   |
                      | 3. Cryptographic Hashing (One-Way)    |
                      | 4. Data Encoding (Representation)     |
                      +---------------------------------------+
```

| Mechanism | Purpose | Reversible? | Key(s) Used | Common Algorithms |
| :--- | :--- | :--- | :--- | :--- |
| **Symmetric Encryption** | Confidentiality of bulk data | **Yes** (with key) | Single shared secret key | **AES-256**, ChaCha20, 3DES (legacy) |
| **Asymmetric Encryption** | Key exchange, digital signatures, identity | **Yes** (with key) | Public Key (encrypt) + Private Key (decrypt) | **RSA (2048/4096-bit)**, ECC, Diffie-Hellman |
| **Cryptographic Hashing** | Data integrity verification & password storage | **No** (One-way mathematical digest)| None (Salted for passwords) | **SHA-256**, SHA-3, bcrypt, Argon2 |
| **Encoding** | Formatting data for transport | **Yes** (Trivial) | No keys used (Public algorithm) | **Base64**, ASCII, Hexadecimal |

> [!WARNING]
> **Encoding is NOT security.** Base64 encoding transforms binary data into ASCII characters for transport, but provides zero confidentiality because anyone can decode it instantly without a key.

---

### The AAA Framework (Authentication, Authorization, Accounting)

The AAA framework governs all identity interactions:

```
  [ 1. IDENTIFICATION ]  ---> "I am Alice." (Username, Employee ID)
            |
            v
  [ 2. AUTHENTICATION ]  ---> "Prove it." (Password, MFA Token, Biometrics)
            |
            v
  [ 3. AUTHORIZATION ]   ---> "What are you allowed to do?" (Read database, write logs)
            |
            v
  [ 4. ACCOUNTING ]      ---> "What did you do?" (Audit logs, timestamps, actions recorded)
```

#### Access Control Models:
- **RBAC (Role-Based Access Control):** Permissions assigned based on job role (e.g., *All Tier 1 Analysts receive read access to SIEM*).
- **ABAC (Attribute-Based Access Control):** Dynamic access evaluated using context attributes (e.g., *Allow access IF user is in HR AND accessing from corporate laptop AND time is 9am-5pm*).
- **DAC (Discretionary Access Control):** Data owner decides who gets access (standard Linux file permissions).
- **MAC (Mandatory Access Control):** Operating system enforces strict security labels (e.g., *Top Secret vs. Secret* in SELinux).

---

### Quick Check & Answers

#### Questions
1. *Why should passwords always be hashed with a unique "Salt" before being stored in a database?*
2. *What is the primary difference between symmetric and asymmetric encryption?*
3. *A web server uses TLS. Which type of cryptography is used to establish the initial handshake, and which is used for bulk data transfer?*
4. *How does Authorization differ from Authentication?*
5. *Why is Base64 encoding unsuitable for protecting confidential data?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** A **Salt** is a random string added to the password before hashing. It ensures identical passwords produce completely different hash outputs, defeating pre-computed **Rainbow Table** attacks and making mass cracking infeasible.
2. **Answer:** **Symmetric encryption** uses a single shared secret key for both encryption and decryption (fast, ideal for bulk data). **Asymmetric encryption** uses a mathematically linked key pair: a public key to encrypt and a private key to decrypt (slower, solves key distribution).
3. **Answer:** **Asymmetric cryptography** (e.g., RSA or ECDHE) is used during the handshake to authenticate the server and securely exchange session keys; **Symmetric cryptography** (AES-256-GCM) is then used to encrypt the high-speed bulk web traffic.
4. **Answer:** **Authentication** validates *who* you are (identity verification); **Authorization** determines *what* resources and actions you are permitted to perform once authenticated.
5. **Answer:** Base64 is a standardized data formatting algorithm designed for transport compatibility, not security. It contains no secret key and can be decoded instantly by anyone.
</details>

---

## Week 3: Vulnerabilities and the Attacker Mindset

### What You Should Understand

Vulnerabilities represent the operational gaps through which adversaries compromise environments. Security analysts must understand how vulnerabilities are categorized (**CVE**), quantified (**CVSS**), discovered via scanning tools, and prioritized against active exploitation in the wild (**CISA KEV** and **EPSS**).

---

### The Vulnerability Management Lifecycle

```
+--------------------------------------------------------------------------------+
|                     VULNERABILITY MANAGEMENT LIFECYCLE                         |
+--------------------------------------------------------------------------------+
| 1. Discover   -> Continuous automated scanning (Nessus, Qualys, OpenVAS)       |
| 2. Prioritize -> Contextualize CVSS scores with asset criticality and EPSS     |
| 3. Assess     -> Verify if vulnerability is exploitable / eliminate false pos. |
| 4. Remediate  -> Apply patch, change configuration, or deploy compensating WAF |
| 5. Verify     -> Rescan asset to confirm vulnerability is completely resolved  |
| 6. Report     -> Document SLA metrics and present risk trends to leadership    |
+--------------------------------------------------------------------------------+
```

---

### Vulnerability Standardization: CVE & CVSS v3.1 / v4.0

- **CVE (Common Vulnerabilities and Exposures):** A standardized dictionary identifier for publicly known vulnerabilities (e.g., `CVE-2021-44228` for Log4Shell).
- **CVSS (Common Vulnerability Scoring System):** Provides a numerical score from **0.0 to 10.0** indicating relative severity:

```
  [ 0.0 - 3.9 : LOW ]   [ 4.0 - 6.9 : MEDIUM ]   [ 7.0 - 8.9 : HIGH ]   [ 9.0 - 10.0 : CRITICAL ]
```

#### CVSS Metric Groups:
1. **Base Score:** Inherent qualities of the vulnerability that do not change over time or environments (Attack Vector [AV], Attack Complexity [AC], Privileges Required [PR], User Interaction [UI], Scope [S], Confidentiality [C], Integrity [I], Availability [A]).
2. **Temporal Score:** Characteristics that change over time (e.g., availability of an automated public exploit kit in Metasploit).
3. **Environmental Score:** Tailored specifically to an organization's actual implementation and mitigating controls.

---

### Real-World Prioritization: Beyond Raw CVSS

> [!IMPORTANT]
> **A CVSS 9.8 vulnerability on an isolated offline lab server is lower business risk than a CVSS 7.5 vulnerability actively exploited on an internet-facing payment gateway.**

Modern analysts use two vital threat intelligence metrics:
- **EPSS (Exploit Prediction Scoring System):** A probabilistic score (0% to 100%) estimating the likelihood that a software vulnerability will be exploited in the wild within 30 days.
- **CISA KEV (Known Exploited Vulnerabilities) Catalog:** An authoritative database of vulnerabilities confirmed to be actively weaponized by threat actors in real-world attacks.

---

### Vulnerability Scanning: Authenticated vs. Unauthenticated

```
+-----------------------------------+-----------------------------------+
| Unauthenticated / External Scan   | Authenticated / Credentialed Scan |
+-----------------------------------+-----------------------------------+
| Scans from an outsider's view     | Scanner logs into the target host |
| without login credentials.        | using administrative credentials. |
| - Finds open ports, banners,      | - Inspects installed software,    |
|   misconfigured firewalls, and    |   missing OS registry patches,    |
|   unencrypted web services.       |   insecure configs, local configs.|
| - Low depth; high false positives.| - Deep fidelity; minimal noise.   |
+-----------------------------------+-----------------------------------+
```

---

### Quick Check & Answers

#### Questions
1. *What is the difference between a vulnerability and an exploit?*
2. *Why might an analyst prioritize a CVSS 7.5 vulnerability over a CVSS 9.8 vulnerability?*
3. *What is the difference between an authenticated and unauthenticated vulnerability scan?*
4. *What does it mean if a CVE is listed on the CISA KEV Catalog?*
5. *Why is rescanning mandatory after applying a patch?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** A **vulnerability** is a flaw or weakness in software/hardware design. An **exploit** is the specific piece of code, script, or technique an attacker uses to take advantage of that vulnerability.
2. **Answer:** If the CVSS 7.5 vulnerability resides on an internet-facing production server with active weaponization in the wild (listed on CISA KEV), its real-world risk is significantly higher than an unexploited CVSS 9.8 flaw on an internal, air-gapped host.
3. **Answer:** An **unauthenticated scan** probes open ports from the outside without credentials (attacker perspective); an **authenticated scan** logs into the host with admin privileges to audit internal software packages, configurations, and registry keys.
4. **Answer:** It indicates that the vulnerability has confirmed evidence of active exploitation by cybercriminals or nation-state actors in the wild, demanding urgent remediation.
5. **Answer:** Rescanning validates that the patch was applied correctly, the service was restarted, and the vulnerability was successfully closed without introducing configuration regressions.
</details>

---

## Week 4: Threats, Social Engineering, Malware, Web Attacks, and Threat Modeling

### What You Should Understand

Security analysts must think like attackers to design effective defenses. Understanding the psychological tactics of **Social Engineering**, the technical execution of **Malware**, and standard **Threat Modeling methodologies (STRIDE)** allows teams to build resilient architectures.

---

### The Pyramid of Pain (David Bianco)

The **Pyramid of Pain** illustrates how difficult it is for an adversary when defenders deny them specific attack indicators:

```
                            /\
                           /  \
                          /TTPs\                <-- TOUGH! (Forces attacker to reinvent behavior)
                         /------\
                        / Tools  \              <-- CHALLENGING (Must re-engineer software)
                       /----------\
                      /Network/Host\            <-- ANNOYING (Reconfigure C2 proxies)
                     /--------------\
                    /  Domain Names  \          <-- SIMPLE (Attacker buys new domain)
                   /------------------\
                  /    IP Addresses    \        <-- EASY (Attacker changes VPS IP)
                 /----------------------\
                /      Hash Values       \      <-- TRIVIAL (1-byte change modifies hash)
               +--------------------------+
```

---

### Malware Taxonomies & Behaviors

```
+------------------+----------------------------------------------------------------------------------+
| Malware Type     | Characteristic Behavior & Infection Mechanism                                   |
+------------------+----------------------------------------------------------------------------------+
| **Virus**        | Attaches to clean executable files; requires human user execution to propagate. |
| **Worm**         | Self-replicating, standalone program; automatically spreads across network flaws.|
| **Trojan**       | Disguises as legitimate software (e.g., fake PDF viewer) to install backdoors.  |
| **Ransomware**   | Encrypts victim filesystem with strong cryptography; demands ransom payment.     |
| **Spyware**      | Covertly tracks keystrokes, clipboard contents, webcam feeds, and screen snaps.  |
| **Rootkit**      | Embeds in OS kernel or UEFI firmware to conceal processes from antivirus tools. |
| **Fileless**     | Executes in memory using built-in system tools (`powershell.exe`, `wmic.exe`).   |
+------------------+----------------------------------------------------------------------------------+
```

---

### Social Engineering Psychological Principles

Attackers manipulate human cognitive biases:
- **Authority:** Impersonating executives, police, or IT leadership (*"This is the CEO, wire these funds immediately"*).
- **Urgency:** Imposing false deadlines to bypass rational caution (*"Your account will be terminated in 15 minutes"*).
- **Fear / Intimidation:** Threatening legal prosecution or employment termination.
- **Scarcity / Greed:** Promising exclusive prizes, cryptocurrency giveaways, or bonuses.
- **Social Proof / Consensus:** Claiming that all other colleagues have already completed the survey.

---

### Threat Modeling: The STRIDE Framework

Developed by Microsoft, **STRIDE** evaluates threats across 6 threat categories:

```
                      +---------------------------------------+
                      |         THE STRIDE FRAMEWORK          |
                      +---------------------------------------+
                      | S - Spoofing Identity                 |
                      | T - Tampering with Data               |
                      | R - Repudiation                       |
                      | I - Information Disclosure            |
                      | D - Denial of Service                 |
                      | E - Elevation of Privilege            |
                      +---------------------------------------+
```

| STRIDE Threat | Violated Security Property | Real-World Scenario | Mitigation Control |
| :--- | :--- | :--- | :--- |
| **Spoofing** | Authentication | Attacker uses stolen session cookies to impersonate an admin. | FIDO2 Multi-Factor Authentication, TLS client certs. |
| **Tampering** | Integrity | Man-in-the-Middle changes transaction amounts in transit. | Cryptographic hashing (HMAC), digital signatures. |
| **Repudiation** | Non-Repudiation | Rogue employee deletes a customer record and denies action. | Secure, append-only centralized audit logging with NTP. |
| **Information Disclosure** | Confidentiality | Web application exposes raw database errors and API keys. | Generic error handling, AES-256 encryption at rest. |
| **Denial of Service** | Availability | Attacker floods API endpoint with invalid payloads. | Rate limiting, Cloudflare DDoS mitigation, auto-scaling. |
| **Elevation of Privilege**| Authorization | Regular user exploits kernel bug to execute commands as root. | Least privilege, AppArmor/SELinux profiles, patching. |

---

### Quick Check & Answers

#### Questions
1. *According to the Pyramid of Pain, why is blocking an attacker's TTPs significantly more effective than blocking an IP address?*
2. *What distinguishes a computer worm from a traditional computer virus?*
3. *Which STRIDE category addresses a scenario where an attacker modifies data stored inside a database?*
4. *What is a "Fileless Malware" attack and why is it challenging to detect?*
5. *Which social engineering principle is exploited when a phishing email claims "Your payroll direct deposit failed; update within 1 hour to receive your paycheck"?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** An attacker can change an IP address in seconds by spinning up a new cloud server, but changing their **TTPs (Tactics, Techniques, and Procedures)** forces them to completely relearn attack workflows, retool software, and redesign operations.
2. **Answer:** A **virus** requires human interaction to execute and spread (e.g., opening an infected `.exe`), whereas a **worm** is completely self-replicating and spreads automatically across networks by exploiting unpatched network vulnerabilities.
3. **Answer:** **Tampering** (violates data integrity).
4. **Answer:** Fileless malware does not drop traditional binary `.exe` files onto the hard drive; instead, it executes entirely in volatile system RAM using legitimate built-in administrative tools (Living-off-the-Land Binaries - LOLBins like PowerShell or WMI), bypassing static file-based antivirus scanners.
5. **Answer:** A combination of **Urgency** and **Fear** (financial impact).
</details>

---

## Module 5 Comprehensive Review Checklist

- [ ] **Asset Management:** I can classify assets across standard tiers (Public, Internal, Confidential, Restricted) and explain the asset lifecycle.
- [ ] **Data Sanitization:** I understand the differences between **Clear, Purge, and Destroy** under NIST SP 800-88.
- [ ] **Data States:** I can describe the 3 states of data (At Rest, In Transit, In Use) and identify appropriate controls for each.
- [ ] **Cryptography Fundamentals:** I can articulate the differences between Symmetric Encryption, Asymmetric Encryption, Hashing, and Encoding.
- [ ] **Identity & Access:** I understand the AAA model and can compare RBAC, ABAC, DAC, and MAC access control models.
- [ ] **Vulnerability Scoring:** I understand how CVEs and CVSS metrics work and how to prioritize remediation using **EPSS and CISA KEV**.
- [ ] **Pyramid of Pain:** I can list the layers of David Bianco's Pyramid of Pain from Hash Values to TTPs.
- [ ] **Threat Modeling:** I can apply the **STRIDE** framework to identify vulnerabilities and design mitigations.

---

## Quick Revision Summary

```
========================================================================================
                          ASSETS, THREATS, AND VULNERABILITIES
========================================================================================
1. DATA STATES   : At Rest (AES-256), In Transit (TLS 1.3), In Use (Enclaves/RAM)
2. CRYPTO PILLARS: Symmetric (Shared Key), Asymmetric (Key Pair), Hashing (1-Way Digest)
3. AAA MODEL     : Authentication (Verify), Authorization (Permit), Accounting (Audit Log)
4. SANITIZATION  : Clear (Overwrite), Purge (Crypto-shred/Degauss), Destroy (Shred/Melt)
5. CVSS & KEV    : Base Score (0-10) + EPSS Probability + CISA KEV Active Weaponization
6. PYRAMID PAIN  : Hash (Trivial) -> IP -> Domain -> Host -> Tools -> TTPs (Toughest)
7. STRIDE MODEL  : Spoofing, Tampering, Repudiation, Info Disclosure, DoS, Elevation of Priv
========================================================================================
```