# Module 3: Connect and Protect - Networks and Network Security

## How to Use This Module

This module provides the comprehensive networking foundations required for security analysis, alert investigation, packet inspection, and defensive network hardening. Network telemetry (IP addresses, port numbers, protocol headers, flow logs, packet captures) forms the primary evidence base during security operations and threat hunting.

Follow the 4-week progression:
1. **Week 1:** Network Architecture, OSI & TCP/IP Models, Encapsulation, Subnetting, and DMZ Segmentation.
2. **Week 2:** Protocols, Core Ports, Address Resolution, DHCP/DNS Lifecycles, and the TCP 3-Way Handshake.
3. **Week 3:** Attack Vectors (DDoS, MitM, ARP/DNS Poisoning, Lateral Movement, Tunneling/Exfiltration).
4. **Week 4:** Defensive Architecture (Stateful/NGFW Firewalls, WAF, IDS/IPS, VPNs, Zero Trust, and Hardening).

---

## Week 1: Networks, Devices, and Communication Basics

### What You Should Understand

A computer network enables disparate computational nodes to exchange data and access centralized services. In modern hybrid enterprises, network architecture spans local area networks (LANs), wide area networks (WANs), software-defined networks (SDN), and virtual private clouds (VPCs).

Defenders rely on **network segmentation** and **micro-segmentation** to isolate sensitive database clusters and prevent attackers from freely traversing the environment.

---

### The OSI Model vs. The TCP/IP Model

Data transmission relies on standardized layered communication models. Each layer provides specific abstraction and services:

```
    OSI 7-LAYER MODEL             TCP/IP 4-LAYER MODEL      DATA UNIT (PDU)    EXAMPLE PROTOCOLS
+-------------------------+     +----------------------+  +-----------------+ +--------------------+
| 7. Application Layer    |     |                      |  |                 | | HTTP, HTTPS, SSH,  |
| 6. Presentation Layer   | --> | 4. Application Layer |  | Data (Payload)  | | DNS, DHCP, FTP,    |
| 5. Session Layer        |     |                      |  |                 | | SMTP, RDP, TLS/SSL |
+-------------------------+     +----------------------+  +-----------------+ +--------------------+
| 4. Transport Layer      | --> | 3. Transport Layer   |  | Segment (TCP) / | | TCP, UDP           |
|                         |     |                      |  | Datagram (UDP)  |                    |
+-------------------------+     +----------------------+  +-----------------+ +--------------------+
| 3. Network Layer        | --> | 2. Internet Layer    |  | Packet          | | IPv4, IPv6, ICMP,  |
|                         |     |                      |  |                 | IPsec, ARP         |
+-------------------------+     +----------------------+  +-----------------+ +--------------------+
| 2. Data Link Layer      | --> |                      |  | Frame           | | Ethernet (802.3),  |
+-------------------------+     | 1. Network Access /  |  +-----------------+ | Wi-Fi (802.11)     |
| 1. Physical Layer       | --> |    Link Layer        |  | Bits            | | Copper, Fiber, RF  |
+-------------------------+     +----------------------+  +-----------------+ +--------------------+
```

#### Encapsulation and Decapsulation (PDU Flow)
- **Encapsulation (Sender):** Application Data is wrapped with a Transport Header (ports) $\rightarrow$ wrapped with a Network Header (IP addresses) $\rightarrow$ wrapped with a Data Link Header/Trailer (MAC addresses & CRC) $\rightarrow$ transmitted as electrical/optical Bits.
- **Decapsulation (Receiver):** Hardware strips the Data Link frame $\rightarrow$ Network layer strips IP packet $\rightarrow$ Transport layer reassembles segments $\rightarrow$ Application receives pure data payload.

---

### Key Network Devices & Topology

```
 [ INTERNET ] <---> [ BORDER FIREWALL ] <---> [ ROUTER ] <---> [ CORE SWITCH ] <---> [ ENDPOINTS / SERVERS ]
```

- **Hub (Layer 1):** Obsolete repeater; broadcasts all incoming traffic to every connected port (major security risk for sniffing).
- **Switch (Layer 2 / Layer 3):** Forwards traffic intelligently based on **MAC address tables** (Layer 2) or VLAN routing (Layer 3).
- **Router (Layer 3):** Directs packets between distinct networks using **IP routing tables**.
- **Firewall (Layer 3/4/7):** Enforces stateful access control policies, filtering traffic based on rules, ports, and protocols.
- **Wireless Access Point - WAP (Layer 2):** Bridges wireless 802.11 RF frames into wired 802.3 Ethernet traffic.

---

### Network Segmentation & DMZ Architecture

A flat network (where all workstations and databases share a single subnet) allows a single compromised laptop to compromise the entire enterprise. 

```
                                  [ INTERNET ]
                                       |
                                [ OUTER FIREWALL ]
                                       |
                   +-------------------+-------------------+
                   |                                       |
             [ PUBLIC DMZ ]                       [ INTERNAL NETWORK ]
       (Web Server, Mail Gateway)                 [ INNER FIREWALL ]
                   |                                       |
         (Untrusted / Isolated)                +-----------+-----------+
                                               |                       |
                                       [ USER SUBNET ]       [ DB / AD SUBNET ]
                                       (Workstations)        (High Security Core)
```

- **DMZ (Demilitarized Zone):** A perimeter network housing external-facing services (e.g., public web servers). If the web server is compromised, the inner firewall prevents direct access into the internal user or database network.
- **VLANs (Virtual Local Area Networks):** Logical segmentation on switches dividing physical broadcast domains (e.g., VLAN 10 = HR, VLAN 20 = Engineering, VLAN 30 = Guests).

---

### Quick Check & Answers

#### Questions
1. *At which OSI layer does a standard router operate, and what is its Protocol Data Unit (PDU)?*
2. *Why is a flat network considered a severe architectural vulnerability?*
3. *What is the function of a DMZ in corporate network design?*
4. *How does data encapsulation work as a packet traverses down the OSI model?*
5. *Why are network hubs insecure compared to switches?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** Layer 3 (Network Layer); its PDU is the **Packet**.
2. **Answer:** In a flat network, there are no internal boundaries or firewalls. If an attacker gains access to one low-privilege endpoint, they can sniff broadcast traffic and move laterally to critical domain controllers and databases unimpeded.
3. **Answer:** A DMZ acts as a buffer zone that exposes public-facing servers (web, mail) to the internet while strictly preventing inbound traffic from the DMZ into the private internal corporate network.
4. **Answer:** As data moves down the stack, each layer appends its own protocol control header (and trailer at Layer 2), packaging the upper-layer payload into segments, packets, and frames.
5. **Answer:** Hubs broadcast every packet to all connected physical ports, allowing any device with a packet sniffer running in promiscuous mode to capture everyone's unencrypted network traffic.
</details>

---

## Week 2: Protocols, Ports, and System Identification

### What You Should Understand

Security analysts constantly inspect logs showing IP addresses, port numbers, MAC addresses, and DNS queries. Recognizing standard protocols and understanding their normal behavioral patterns is critical for spotting malicious anomalies.

---

### Addressing: IP vs. MAC & The ARP Protocol

```
+------------------+------------------------------------+--------------------------------------+
| Property         | MAC Address (Layer 2)              | IP Address (Layer 3)                 |
+------------------+------------------------------------+--------------------------------------+
| Format           | 48-bit Hexadecimal                 | 32-bit Decimal (IPv4) or 128-bit (v6)|
| Example          | `00:1A:2B:3C:4D:5E`                | `192.168.1.50`                       |
| Scope            | Local subnet / physical link only  | Globally routable (or across subnets)|
| Assignment       | Burned into NIC hardware (OUI+NIC) | Dynamically assigned (DHCP or static)|
+------------------+------------------------------------+--------------------------------------+
```

#### ARP (Address Resolution Protocol)
- **Purpose:** Resolves a known Layer 3 IP address to a physical Layer 2 MAC address on the local subnet.
- **Workflow:** Device sends an `ARP Request` broadcast ("Who has IP 192.168.1.1? Tell 192.168.1.50"). The gateway responds with an `ARP Reply` unicast ("192.168.1.1 is at 00:1A:2B:3C:4D:5E").

---

### Private vs. Public IPv4 (RFC 1918)

Private IP addresses are non-routable on the public internet and reserved for internal corporate/home LANs:
- **Class A:** `10.0.0.0` to `10.255.255.255` (`10.0.0.0/8`)
- **Class B:** `172.16.0.0` to `172.31.255.255` (`172.16.0.0/12`)
- **Class C:** `192.168.0.0` to `192.168.255.255` (`192.168.0.0/16`)
- **Loopback Address:** `127.0.0.1` (localhost)
- **APIPA (Automatic Private IP Addressing):** `169.254.0.0/16` (indicates failed DHCP resolution).

---

### DNS & DHCP Operational Lifecycles

#### 1. DHCP DORA Process
```
[Client] --- DHCP Discover (Broadcast: "I need an IP") ---> [DHCP Server]
[Client] <--- DHCP Offer (Unicast: "Here is 192.168.1.50") --- [DHCP Server]
[Client] --- DHCP Request (Broadcast: "I accept that IP") --> [DHCP Server]
[Client] <--- DHCP Acknowledge (Unicast: "Confirmed leased") - [DHCP Server]
```

#### 2. DNS (Domain Name System) Records
- **A Record:** Maps domain name $\rightarrow$ IPv4 address (`example.com -> 93.184.216.34`).
- **AAAA Record:** Maps domain name $\rightarrow$ IPv6 address.
- **CNAME:** Canonical name / alias (`www.example.com -> example.com`).
- **MX Record:** Mail exchange server responsible for receiving emails.
- **TXT Record:** Holds verification strings, SPF, and DKIM security records.

---

### Transport Protocols: TCP vs. UDP

```
+-----------------------------------+-----------------------------------+
| TCP (Transmission Control Protocol)| UDP (User Datagram Protocol)      |
+-----------------------------------+-----------------------------------+
| Connection-oriented (Handshake)   | Connectionless (No handshake)     |
| Reliable (Guaranteed delivery/ACK)| Unreliable (Best-effort delivery) |
| Flow control & Packet ordering    | Low overhead, minimal latency     |
| Examples: HTTP/S, SSH, FTP, SMTP  | Examples: DNS queries, VoIP, DHCP |
+-----------------------------------+-----------------------------------+
```

#### The TCP 3-Way Handshake & 4-Way Teardown
```
ESTABLISHING CONNECTION (3-Way):
[Client] --- SYN (Seq=100) ------------------------> [Server]
[Client] <--- SYN-ACK (Seq=300, Ack=101) ----------- [Server]
[Client] --- ACK (Seq=101, Ack=301) ---------------> [Server]

CLOSING CONNECTION (4-Way):
[Client] --- FIN ----------------------------------> [Server]
[Client] <--- ACK ---------------------------------- [Server]
[Client] <--- FIN ---------------------------------- [Server]
[Client] --- ACK ----------------------------------> [Server]
```

---

### Essential Ports Cheat Sheet for Security Analysts

| Port | Protocol | Layer | Transport | Security Relevance / Default Usage |
| :--- | :--- | :--- | :--- | :--- |
| **20/21**| **FTP** | App | TCP | Unencrypted file transfer; transmits credentials in plaintext. |
| **22** | **SSH / SFTP**| App | TCP | Encrypted remote terminal administration and secure file transfer. |
| **23** | **Telnet** | App | TCP | Legacy unencrypted remote shell (**High Risk: Replace with SSH**). |
| **25** | **SMTP** | App | TCP | Mail transfer between servers (Inspect for spam/relay abuse). |
| **53** | **DNS** | App | UDP/TCP | Domain name queries (UDP); Zone transfers & large payloads (TCP). |
| **67/68**| **DHCP** | App | UDP | Network IP configuration assignment. |
| **80** | **HTTP** | App | TCP | Unencrypted web browsing. |
| **88** | **Kerberos**| App | UDP/TCP | Active Directory ticket authentication service. |
| **110**| **POP3** | App | TCP | Unencrypted email client retrieval. |
| **123**| **NTP** | App | UDP | Network Time Protocol (Crucial for log correlation timestamping). |
| **143**| **IMAP** | App | TCP | Unencrypted mail retrieval. |
| **389**| **LDAP** | App | TCP | Lightweight Directory Access Protocol (Active Directory queries). |
| **443**| **HTTPS** | App | TCP | Encrypted web traffic using TLS 1.2/1.3. |
| **445**| **SMB** | App | TCP | Server Message Block (File/printer sharing; attacked by EternalBlue/WannaCry). |
| **636**| **LDAPS** | App | TCP | Secure LDAP over TLS. |
| **3389**| **RDP** | App | TCP | Windows Remote Desktop Protocol (**Target for brute-force attacks**). |

---

### Quick Check & Answers

#### Questions
1. *Which RFC 1918 address block does the IP `172.20.15.4` belong to?*
2. *Describe the sequence of flags exchanged during a TCP 3-Way Handshake.*
3. *Why is port 23 (Telnet) banned in secure environments?*
4. *What protocol resolves an IP address to a physical MAC address on a local network?*
5. *Why does DNS use UDP port 53 for normal lookups but TCP port 53 for zone transfers?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** RFC 1918 Class B private address space (`172.16.0.0/12`, spanning `172.16.0.0` to `172.31.255.255`).
2. **Answer:** 1. Client sends **SYN** $\rightarrow$ 2. Server responds with **SYN-ACK** $\rightarrow$ 3. Client replies with **ACK**.
3. **Answer:** Telnet transmits all data, including usernames and administrative passwords, in unencrypted plaintext across the wire, making it trivial to intercept via packet sniffing.
4. **Answer:** **ARP (Address Resolution Protocol)**.
5. **Answer:** Standard DNS queries are small and require rapid response with low overhead (UDP); DNS zone transfers contain large database tables that require guaranteed, connection-oriented delivery (TCP).
</details>

---

## Week 3: Network Attacks and Intrusion Tactics

### What You Should Understand

Threat actors target network protocols, architectures, and services to intercept confidential communications, disrupt availability, bypass controls, and move laterally toward core databases.

---

### Taxonomy of Network Attack Vectors

```
                      +---------------------------------------+
                      |         NETWORK ATTACK VECTORS        |
                      +---------------------------------------+
                      | 1. Denial of Service (DoS/DDoS)       |
                      | 2. Eavesdropping & Packet Sniffing    |
                      | 3. Spoofing & Poisoning (ARP/DNS)     |
                      | 4. Man-in-the-Middle (MitM)           |
                      | 5. Lateral Movement & Pivoting        |
                      | 6. Covert Tunneling & Exfiltration    |
                      +---------------------------------------+
```

---

### Deep Dive: Common Network Attacks

#### 1. Denial of Service (DoS & DDoS)
- **SYN Flood Attack:** Attacker sends thousands of spoofed TCP SYN requests without sending the final ACK, exhausting the victim server's connection state table (backlog queue).
- **UDP Amplification (NTP/DNS/SSDP):** Attacker sends small spoofed requests to open servers with victim's source IP; the servers reply with huge responses directed at the victim, saturating bandwidth.
- **Application Layer Flood (HTTP GET/POST Flood):** Overwhelms web applications by repeatedly requesting resource-heavy database queries.

#### 2. ARP Poisoning / ARP Spoofing
```
[ Victim Machine ]                       [ Default Gateway ]
  (192.168.1.10)                           (192.168.1.1)
        |                                        |
        +-------------------+--------------------+
                            |
                     [ Attacker ]
                    (192.168.1.50)
      Sends Gratuitous ARP: "192.168.1.1 is at ATTACKER-MAC"
      Sends Gratuitous ARP: "192.168.1.10 is at ATTACKER-MAC"
```
- *Mechanism:* Attacker floods the local network with fake ARP responses. Both the victim and gateway update their ARP caches with the attacker's MAC address, placing the attacker in the middle to sniff, modify, or drop packets.

#### 3. DNS Cache Poisoning
- Attacker injects malicious DNS records into a caching DNS resolver so users navigating to `bank.com` are redirected to a fraudulent phishing server IP.

#### 4. Lateral Movement & Network Pivoting
- **Pass-the-Hash (PtH):** Attacker steals NTLM password hashes from memory and uses them to authenticate to other machines on port 445 (SMB) without needing the plaintext password.
- **RDP / SSH Pivoting:** Using a compromised perimeter jump box to establish encrypted tunnels into internal restricted subnets.

#### 5. Covert Exfiltration & Tunneling
- **DNS Tunneling:** Encoding stolen data into the subdomain labels of outbound DNS queries (`data-chunk1.attacker-c2.com`), bypassing firewalls that allow port 53.
- **ICMP Tunneling:** Injecting payload data into the data field of echo request/reply ping packets.

---

### Reading Network & Firewall Logs

#### Example: Analyzing a Firewall Drop Log
```log
2026-09-23T14:22:10Z FW-BORDER-01 ACTION=DROP SRC=203.0.113.88 DST=10.0.1.15 PROTO=TCP SPT=49152 DPT=3389 REASON="DEFAULT_DENY_INBOUND"
```
> [!NOTE]
> **Triage Analysis:**
> - **Source:** External public IP `203.0.113.88`
> - **Destination:** Internal server `10.0.1.15`
> - **Target Port:** `3389` (Windows RDP)
> - **Verdict:** Attacker is attempting external RDP brute-force; dropped successfully by perimeter rule.

---

### Quick Check & Answers

#### Questions
1. *How does a TCP SYN flood attack exploit the 3-Way Handshake mechanism?*
2. *What network defense technique prevents ARP Spoofing on enterprise switches?*
3. *How does DNS Tunneling allow an attacker to bypass standard egress firewall restrictions?*
4. *What protocol port is targeted during SMB-based lateral movement attacks like EternalBlue?*
5. *What is the difference between DoS and DDoS?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** The attacker floods the target with SYN packets with spoofed source IPs and ignores the server's SYN-ACK responses. The server keeps hundreds of half-open connections in its backlog memory buffer until resources are exhausted, denying service to legitimate clients.
2. **Answer:** **Dynamic ARP Inspection (DAI)** coupled with **DHCP Snooping** on Layer 2 managed switches, which validates ARP packets against a trusted binding database.
3. **Answer:** Most firewalls allow internal machines to send outbound UDP port 53 DNS queries to resolve hostnames. Attackers encapsulate stolen sensitive data inside DNS query names to an attacker-controlled authoritative nameserver.
4. **Answer:** **Port 445 (SMB - Server Message Block)**.
5. **Answer:** A **DoS** originates from a single source computer, making it easy to block by IP. A **DDoS** originates from hundreds or thousands of distributed compromised machines (a Botnet), overwhelming target bandwidth and making filtering difficult.
</details>

---

## Week 4: Network Hardening and Defense

### What You Should Understand

Hardening is the systematic process of reducing a network's attack surface by eliminating unnecessary protocols, patching vulnerabilities, segmenting assets, deploying firewalls, enforcing authentication, and implementing **Zero Trust Architecture**.

---

### Firewall Technologies & Inspection Types

```
+--------------------------+---------------------+----------------------------------------------------+
| Firewall Generation      | OSI Layer Inspected | Inspection Capabilities & Limitations              |
+--------------------------+---------------------+----------------------------------------------------+
| Packet Filtering (Legacy)| Layer 3 & 4         | Inspects headers only (IP, Port). Stateless;       |
|                          |                     | cannot track connection state.                     |
| Stateful Inspection      | Layer 3, 4, 5       | Tracks TCP connection state tables; ensures inbound|
|                          |                     | packets match an existing established session.     |
| Next-Gen Firewall (NGFW) | Layer 3 through 7   | Deep Packet Inspection (DPI), Application ID,      |
|                          |                     | TLS decryption, integrated IPS & antivirus.        |
| Web App Firewall (WAF)   | Layer 7             | Specifically filters HTTP/HTTPS payloads for       |
|                          |                     | SQLi, XSS, and OWASP Top 10 web exploits.         |
+--------------------------+---------------------+----------------------------------------------------+
```

---

### Intrusion Detection & Prevention (IDS / IPS)

```
+-----------------------------------+-----------------------------------+
| Signature-Based Detection         | Anomaly / Behavioral-Based        |
+-----------------------------------+-----------------------------------+
| Matches traffic against a database| Establishes a baseline of normal  |
| of known attack patterns/IoCs.    | network traffic; alerts on spikes.|
| - Pros: Very low false positives  | - Pros: Can detect novel zero-days|
| - Cons: Cannot detect zero-days   | - Cons: High false positive rate  |
+-----------------------------------+-----------------------------------+
```

---

### Secure Remote Access & Virtual Private Networks (VPN)

A VPN creates an encrypted, authenticated virtual tunnel across untrusted public networks:
- **IPsec VPN (Layer 3):** Encapsulates and encrypts entire IP packets using AH (Authentication Header) and ESP (Encapsulating Security Payload). Often used for **Site-to-Site** datacenter connections.
- **SSL/TLS VPN (Layer 4/7):** Uses TLS via web browsers or client agents (e.g., OpenVPN, WireGuard). Commonly used for **Remote Access** workers.

---

### The Zero Trust Network Architecture (ZTNA)

> [!IMPORTANT]
> **Zero Trust Core Motto:** *"Never Trust, Always Verify."*
> Traditional perimeter security operated like a castle-and-moat (trusting everything inside the corporate intranet). Zero Trust assumes the network is already hostile.

#### 3 Guiding Principles of Zero Trust (NIST SP 800-207):
1. **Verify Explicitly:** Always authenticate and authorize based on all available data points (identity, location, device health, service).
2. **Use Least Privilege Access:** Limit user access with Just-In-Time (JIT) and Just-Enough-Access (JEA) models.
3. **Assume Breach:** Minimize blast radius by segmenting networks, encrypting end-to-end, and continuously analyzing telemetry.

---

### Comprehensive Network Hardening Checklist

- [ ] **Disable Insecure Protocols:** Replace Telnet (23) with SSH (22), HTTP (80) with HTTPS (443), FTP (21) with SFTP (22), and SNMPv1/v2 with SNMPv3.
- [ ] **Enforce 802.1X Port Security:** Require certificate-based authentication before any physical ethernet cable can join the corporate switch network.
- [ ] **Implement Default Deny (Implicit Deny):** Configure all firewall rulebases so that any traffic not explicitly permitted is dropped automatically.
- [ ] **Enable Layer 2 Protections:** Enable DHCP Snooping, Dynamic ARP Inspection (DAI), and BPDU Guard on managed switches.
- [ ] **Implement Network Micro-Segmentation:** Isolate database tiers, management consoles, and user VLANs using internal Next-Gen Firewalls.
- [ ] **Enable Centralized Logging:** Forward all firewall, VPN, DNS, and NetFlow logs to the enterprise SIEM with synchronized NTP time.

---

### Quick Check & Answers

#### Questions
1. *What does "Implicit Deny" mean in firewall rule configuration?*
2. *How does a Next-Generation Firewall (NGFW) differ from a standard stateful firewall?*
3. *What are the three core principles of Zero Trust Architecture?*
4. *Why is SNMPv3 preferred over SNMPv1 and SNMPv2?*
5. *What is the difference between signature-based and anomaly-based IDS detection?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** It is the foundational security rule placed at the very bottom of an Access Control List (ACL) stating that any traffic that does not match an explicit allow rule is automatically blocked/dropped.
2. **Answer:** A standard stateful firewall inspects up to Layer 4 (IPs and ports); an NGFW performs Layer 7 Deep Packet Inspection (DPI), decrypts TLS traffic, identifies specific applications (e.g., distinguishing Facebook chat from standard web browsing), and includes built-in IPS.
3. **Answer:** 1. **Verify explicitly**, 2. **Use least privilege access**, 3. **Assume breach**.
4. **Answer:** SNMPv1 and SNMPv2 send community strings in plaintext across the network. SNMPv3 introduces robust cryptographic authentication and encryption for network device management.
5. **Answer:** Signature-based IDS compares traffic against known attack fingerprints (accurate for known malware, blind to zero-days); anomaly-based IDS compares traffic against a statistical baseline of normal behavior (able to detect new zero-days, but prone to false alarms).
</details>

---

## Module 3 Comprehensive Review Checklist

- [ ] **Network Models:** I understand the 7 layers of OSI and 4 layers of TCP/IP, along with their respective PDUs.
- [ ] **Device Operations:** I know the difference between Hubs, Switches (Layer 2/3), Routers, Firewalls, and WAPs.
- [ ] **Address Resolution:** I can explain ARP, MAC addressing, RFC 1918 Private IPv4 ranges, and IPv6 formatting.
- [ ] **Core Protocols:** I can map standard services to their exact port numbers (**22, 53, 80, 88, 443, 445, 3389**).
- [ ] **TCP Mechanics:** I can diagram the TCP 3-Way Handshake (SYN, SYN-ACK, ACK) and explain how TCP differs from UDP.
- [ ] **Network Attacks:** I understand SYN floods, UDP amplification, ARP poisoning, DNS cache poisoning, and DNS tunneling.
- [ ] **Firewalls & Filters:** I understand Stateless, Stateful, NGFW, and WAF architectures.
- [ ] **Zero Trust:** I understand the core tenets of Zero Trust Network Architecture (NIST SP 800-207).
- [ ] **Hardening:** I can formulate a multi-point network hardening plan.

---

## Quick Revision Summary

```
========================================================================================
                              NETWORKS & NETWORK SECURITY
========================================================================================
1. OSI LAYERS    : App(7) -> Pres(6) -> Sess(5) -> Trans(4) -> Net(3) -> Link(2) -> Phys(1)
2. PDUs          : Data (L7-5) -> Segment (L4) -> Packet (L3) -> Frame (L2) -> Bits (L1)
3. PRIVATE IPs   : 10.0.0.0/8 | 172.16.0.0/12 | 192.168.0.0/16 | Loopback: 127.0.0.1
4. CORE PORTS    : 22(SSH), 53(DNS), 80(HTTP), 88(Kerberos), 443(HTTPS), 445(SMB), 3389(RDP)
5. HANDSHAKE     : SYN -> SYN-ACK -> ACK (Connection Established)
6. ATTACKS       : DDoS, ARP/DNS Poisoning, MitM, Pass-the-Hash, DNS Tunneling Exfiltration
7. HARDENING     : Implicit Deny, NGFW Layer 7 DPI, WAF, DAI/DHCP Snooping, Zero Trust, 802.1X
========================================================================================
```