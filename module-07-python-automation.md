# Module 7: Automate Cybersecurity Tasks with Python

## How to Use This Module

This module provides practical, analyst-focused **Python scripting and automation skills**. In modern security operations, analysts automate repetitive triage tasks: parsing gigabytes of firewall logs, querying Threat Intelligence APIs (e.g., VirusTotal, AbuseIPDB), cross-referencing IP blocklists, updating firewall access control lists, and extracting Indicators of Compromise (IoCs) with Regular Expressions (Regex).

Follow the 4-week progression:
1. **Week 1:** Python Foundations, Data Types, Conditionals, Loops, and Collections.
2. **Week 2:** Modular Functions, Threat Intelligence API Modules (`requests`, `json`, `ipaddress`), and PEP 8 Standards.
3. **Week 3:** String Manipulation, List/Dictionary Comprehensions, and Security Regex Mastery (`re` module).
4. **Week 4:** Defensive File I/O (`with open`), Robust Exception Handling (`try/except`), and Automated Log Parser Pipelines.

---

## Week 1: Python Basics, Data Types, Conditionals, and Loops

### What You Should Understand

Python is an interpreted, high-level programming language prized in cybersecurity for its readability, rapid development velocity, and extensive ecosystem of security libraries. Analysts write Python scripts to automate tasks that are too slow or error-prone to perform manually.

---

### Core Data Types & Data Structures

```
+-----------------------------------+-----------------------------------------------------------------+
| Data Type / Structure             | Python Syntax Example & Security Relevance                      |
+-----------------------------------+-----------------------------------------------------------------+
| **Integer (`int`)**               | `failed_logins = 12` (Tracking login thresholds)                |
| **String (`str`)**                | `src_ip = "192.168.1.50"` (IPs, hashes, usernames, domain names)|
| **Boolean (`bool`)**              | `is_admin = True` (Conditional access checks)                   |
| **List (`list`)**                 | `approved_ports = [80, 443, 22]` (Ordered, mutable sequences)   |
| **Dictionary (`dict`)**           | `user = {"name": "alice", "role": "admin", "failed": 3}` (JSON)|
| **Set (`set`)**                   | `unique_ips = {"10.0.0.1", "10.0.0.2"}` (De-duplicating IoCs)   |
+-----------------------------------+-----------------------------------------------------------------+
```

---

### Conditionals & Alert Severity Logic

```python
def evaluate_alert(failed_attempts, is_mfa_enabled, is_admin_account):
    """Classifies authentication anomaly severity."""
    if failed_attempts >= 10 and not is_mfa_enabled:
        return "CRITICAL: Potential Credential Stuffing Attack"
    elif failed_attempts >= 5 and is_admin_account:
        return "HIGH: Brute Force Attempt on Privileged Account"
    elif failed_attempts >= 3:
        return "MEDIUM: Multiple Failed Logins Detected"
    else:
        return "LOW: Normal User Activity"

print(evaluate_alert(failed_attempts=8, is_mfa_enabled=False, is_admin_account=True))
```

---

### Loops & Automated List Filtering

#### Iterating Over IP Blocklists
```python
network_traffic = ["192.168.1.10", "198.51.100.23", "10.0.0.5", "203.0.113.88"]
malicious_blacklist = {"198.51.100.23", "203.0.113.88", "192.0.2.1"}

print("[*] Initiating Network Traffic Scan against Threat Intelligence Feeds...")

for ip in network_traffic:
    if ip in malicious_blacklist:
        print(f"[!] ALERT: Connection detected to known malicious C2 IP: {ip}")
    else:
        print(f"[+] Traffic permitted: {ip}")
```

---

### Quick Check & Answers

#### Questions
1. *What is the difference between a Python List and a Python Set, and why are Sets preferred for de-duplicating IoCs?*
2. *What does the membership operator `in` do in Python?*
3. *Why should analysts use a `for` loop instead of an unbounded `while` loop when parsing fixed log files?*
4. *What data type is returned by the expression `10 > 5`?*
5. *How do you access the value of the key `"role"` in the dictionary `user_info = {"name": "alice", "role": "admin"}`?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** A **List** is ordered and allows duplicate elements; a **Set** is unordered and mathematically guarantees that all elements are unique. Converting a list of 10,000 IP logs to a Set instantly strips all duplicates in $O(N)$ time.
2. **Answer:** The `in` operator checks whether a specified value exists within a sequence or collection (such as a string, list, or set), returning `True` or `False`.
3. **Answer:** A `for` loop automatically iterates through each item in the sequence and terminates cleanly at the end of the file, whereas an improperly bounded `while` loop risks entering an infinite loop that crashes the script or consumes 100% CPU.
4. **Answer:** A **Boolean (`bool`)** value: `True`.
5. **Answer:** `user_info["role"]` or `user_info.get("role")`.
</details>

---

## Week 2: Functions, Libraries, Modules, and Code Style

### What You Should Understand

Writing modular, reusable functions is essential for building scalable automation pipelines. In enterprise environments, analyst scripts must follow clean code standards (**PEP 8**) and leverage standard and third-party libraries (`requests`, `json`, `ipaddress`, `csv`).

---

### Anatomy of a Secure Python Function

```python
def validate_ip_address(ip_string: str) -> bool:
    """
    Validates if an input string is a legitimate IPv4 or IPv6 address.
    Uses Python's built-in ipaddress module.
    """
    import ipaddress
    try:
        ipaddress.ip_address(ip_string.strip())
        return True
    except ValueError:
        return False

# Usage
test_ip = "192.168.1.1"
if validate_ip_address(test_ip):
    print(f"Valid IP: {test_ip}")
```

---

### Essential Python Modules for Security Operations

```
+-------------------+---------------------------------------------------------------------------------+
| Module Name       | Primary Operational Use Case in Security Automation                             |
+-------------------+---------------------------------------------------------------------------------+
| **`os` & `sys`**  | Interacting with underlying OS filesystems, environment variables, and CLI args.|
| **`re`**          | Parsing unstructured logs using Regular Expressions.                            |
| **`csv` & `json`**| Ingesting exported SIEM reports, asset databases, and API payloads.             |
| **`datetime`**    | Parsing timestamps, calculating time deltas, and standardizing to UTC.          |
| **`ipaddress`**   | Subnet math, checking if an IP is private (RFC 1918) or public.                 |
| **`requests`**    | Querying external Threat Intelligence REST APIs (VirusTotal, AbuseIPDB, Shodan).|
| **`hashlib`**     | Computing SHA-256 / MD5 hashes of suspected malware binaries.                   |
+-------------------+---------------------------------------------------------------------------------+
```

---

### PEP 8 Style Standards & Documentation Best Practices

- **Variable & Function Naming:** Use `snake_case` (e.g., `parse_firewall_logs()`, `suspicious_ip_list`).
- **Constant Naming:** Use `UPPER_SNAKE_CASE` (e.g., `API_TIMEOUT_SECONDS = 30`).
- **Indentation:** Exactly 4 spaces per indentation level (never tabs).
- **Docstrings:** Enclose function documentation inside triple quotes `"""Docstring"""` detailing parameters, return types, and exceptions.

---

### Quick Check & Answers

#### Questions
1. *What is the difference between a Parameter and an Argument in Python?*
2. *What does the `ipaddress` module accomplish that simple string splitting cannot?*
3. *Why is `hashlib.sha256()` preferred over `hashlib.md5()` when calculating malware hashes?*
4. *What PEP 8 convention is used for constant configuration values?*
5. *What is the purpose of the `return` statement inside a function?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** A **parameter** is the variable defined inside the function signature (e.g., `def scan(target_ip):`); an **argument** is the actual value passed into the function when called (e.g., `scan("10.0.0.1")`).
2. **Answer:** The `ipaddress` module performs rigorous mathematical validation against IPv4/IPv6 standards, validates CIDR subnet membership, and identifies private, loopback, and multicast address ranges.
3. **Answer:** MD5 is cryptographically broken and vulnerable to collision attacks (where two different files produce the same hash); SHA-256 provides a collision-resistant 256-bit cryptographic digest.
4. **Answer:** **UPPER_SNAKE_CASE** (e.g., `MAX_RETRIES = 5`).
5. **Answer:** `return` exits the function immediately and passes the resulting computed value back to the caller.
</details>

---

## Week 3: Strings, Lists, Algorithms, and Regular Expressions

### What You Should Understand

The vast majority of cybersecurity telemetry arrives as unstructured text strings. Analysts must master string transformation methods, list manipulation algorithms, and **Regular Expressions (`re`)** to extract target artifacts automatically.

---

### String Manipulation Methods

```python
log_entry = "  2026-09-23,ALERT,192.168.1.50,MALWARE_BEACON  "

# 1. Strip whitespace:
clean_entry = log_entry.strip()

# 2. Split by delimiter into a list:
fields = clean_entry.split(",")
# Result: ['2026-09-23', 'ALERT', '192.168.1.50', 'MALWARE_BEACON']

# 3. String normalization for case-insensitive matching:
status = "FaILeD".lower()  # "failed"

# 4. Joining list elements back into a string:
reconstructed = " | ".join(fields)
```

---

### Regular Expressions (`re`) for Security Analysts

Regular expressions allow analysts to define complex search patterns to extract indicators from unstructured text:

```
+--------------------------------------------------------------------------------+
|                         REGEX METASYMBOLS & SYNTAX                             |
+-------------------+------------------------------------------------------------+
| `\d`              | Matches any single decimal digit [0-9]                     |
| `\w`              | Matches any alphanumeric word character [a-zA-Z0-9_]       |
| `\s`              | Matches any whitespace character (space, tab, newline)     |
| `+`               | Quantifier: 1 or more occurrences                          |
| `*`               | Quantifier: 0 or more occurrences                          |
| `{n,m}`           | Quantifier: Between n and m occurrences                    |
| `\b`              | Word boundary anchor                                       |
| `^` / `$`         | Start of line anchor / End of line anchor                  |
+-------------------+------------------------------------------------------------+
```

#### Essential Cybersecurity Regex Patterns:
- **IPv4 Address:** `r"\b(?:\d{1,3}\.){3}\d{1,3}\b"`
- **SHA-256 Hash:** `r"\b[a-fA-F0-9]{64}\b"`
- **Standard Email:** `r"\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b"`
- **CVE Identifier:** `r"CVE-\d{4}-\d{4,7}"`

---

### Practical Script: Extracting Suspicious Indicators from Raw Logs

```python
import re

raw_log_dump = """
Sep 23 10:14:02 host-01 sshd[1204]: Failed password from 203.0.113.19 port 5422
Sep 23 10:14:15 host-01 kernel: Outbound to 198.51.100.44:443 blocked.
Sep 23 10:15:00 host-01 sec_tool: Found hash e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
"""

# Extract all IPv4 addresses:
ip_pattern = r"\b(?:\d{1,3}\.){3}\d{1,3}\b"
extracted_ips = re.findall(ip_pattern, raw_log_dump)
print(f"Extracted IPs: {set(extracted_ips)}")

# Extract all SHA-256 Hashes:
hash_pattern = r"\b[a-fA-F0-9]{64}\b"
extracted_hashes = re.findall(hash_pattern, raw_log_dump)
print(f"Extracted SHA-256 Hashes: {extracted_hashes}")
```

---

### Quick Check & Answers

#### Questions
1. *What does the regex pattern `r"CVE-\d{4}-\d{4,7}"` match?*
2. *What is the difference between `re.search()` and `re.findall()` in Python?*
3. *Why is `.strip()` commonly called on lines read from a file?*
4. *What does the regex quantifier `+` mean?*
5. *How do you combine a list of strings `['10.0.0.1', '10.0.0.2']` into a comma-separated string?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** It matches standardized Common Vulnerabilities and Exposures identifiers (e.g., `CVE-2021-44228`), consisting of the literal string `CVE-`, a 4-digit year, a hyphen, and a 4- to 7-digit vulnerability number.
2. **Answer:** `re.search()` scans through the string and returns only the **first** match object found (or `None`); `re.findall()` scans the entire string and returns **all** non-overlapping matches as a Python list of strings.
3. **Answer:** File lines read from disk contain invisible trailing newline characters (`\n` or `\r\n`) and leading/trailing whitespace. `.strip()` removes these artifacts to prevent formatting errors during comparisons.
4. **Answer:** It specifies that the preceding token must appear **one or more times** consecutively.
5. **Answer:** `", ".join(['10.0.0.1', '10.0.0.2'])`.
</details>

---

## Week 4: Files, Errors, Debugging, and Automation Workflows

### What You Should Understand

Security automation scripts must safely read files, handle unexpected corrupted inputs without crashing (**defensive exception handling**), and write clean audit logs and reports.

---

### Safe File Handling with Context Managers (`with open`)

Using the `with open()` statement ensures that file descriptors are automatically closed and memory is freed, even if an unhandled error occurs midway through processing:

```python
# Safe, memory-efficient line-by-line reading
def parse_firewall_denials(input_filepath: str, output_filepath: str):
    """Parses raw firewall logs and exports DENIED IP records."""
    denied_entries = []
    
    with open(input_filepath, mode="r", encoding="utf-8") as infile:
        for line in infile:
            if "ACTION=DENY" in line:
                denied_entries.append(line.strip())
                
    with open(output_filepath, mode="w", encoding="utf-8") as outfile:
        for entry in denied_entries:
            outfile.write(entry + "\n")
            
    print(f"[+] Successfully exported {len(denied_entries)} denied records to {output_filepath}")
```

---

### Robust Exception Handling (`try`, `except`, `finally`)

In production environments, a missing file or malformed network packet should never crash an automation daemon:

```python
import sys

def secure_file_loader(filepath: str):
    """Safely loads file contents with granular error handling."""
    try:
        with open(filepath, "r", encoding="utf-8") as f:
            data = f.read()
            return data
    except FileNotFoundError:
        print(f"[!] ERROR: Target file '{filepath}' does not exist on disk.", file=sys.stderr)
        return None
    except PermissionError:
        print(f"[!] ERROR: Insufficient read permissions for file '{filepath}'.", file=sys.stderr)
        return None
    except Exception as e:
        print(f"[!] UNEXPECTED ERROR: {str(e)}", file=sys.stderr)
        return None
```

---

### Complete Automation Pipeline: IP Allowlist / Blocklist Synchronizer

This script reads an enterprise blocklist and removes decommissioned IPs from an active firewall configuration:

```python
def update_firewall_allowlist(allowlist_file: str, remove_list: list):
    """Removes obsolete or rogue IPs from the active firewall allowlist file."""
    try:
        # 1. Read existing allowlist
        with open(allowlist_file, "r") as file:
            current_ips = file.read().split()
            
        # 2. Filter out decommissioned IPs
        updated_ips = [ip for ip in current_ips if ip not in remove_list]
        
        # 3. Rewrite updated allowlist
        with open(allowlist_file, "w") as file:
            file.write("\n".join(updated_ips) + "\n")
            
        print(f"[+] Allowlist synchronized. Total IPs active: {len(updated_ips)}")
        
    except FileNotFoundError:
        print(f"[!] Error: {allowlist_file} not found.")

# Execution
decommissioned = ["192.168.1.105", "10.0.0.88"]
update_firewall_allowlist("firewall_allowlist.txt", decommissioned)
```

---

### Quick Check & Answers

#### Questions
1. *Why is `with open(...)` preferred over manually calling `open()` and `close()`?*
2. *What happens if you open a file with mode `"w"` if the file already exists with data?*
3. *Why should you avoid using a bare `except:` clause without specifying exception types?*
4. *What is the difference between `.read()`, `.readline()`, and iterating over the file object directly?*
5. *What mode should you specify in `open()` if you want to add new log lines to an existing file without deleting old data?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** `with open(...)` implements a context manager that guarantees the file is automatically closed and system resources are freed, even if an exception or crash occurs inside the block.
2. **Answer:** Mode `"w"` immediately truncates and overwrites the existing file, permanently wiping all previous contents.
3. **Answer:** A bare `except:` catches all exceptions, including critical system signals like `KeyboardInterrupt` (Ctrl+C) and `SystemExit`, making it difficult to stop the script and masking unintended syntax or logic bugs.
4. **Answer:** `.read()` loads the entire file into memory as one massive string (risks running out of RAM on large multi-gigabyte logs); `.readline()` reads a single line; iterating directly (`for line in file:`) buffers lines lazily, making it fast and memory-safe.
5. **Answer:** Append mode: `mode="a"`.
</details>

---

## Module 7 Comprehensive Review Checklist

- [ ] **Python Data Types:** I understand `int`, `float`, `str`, `bool`, `list`, `dict`, and `set`.
- [ ] **Control Flow:** I can write multi-branch conditional statements (`if/elif/else`) and `for` loops.
- [ ] **Functions & Modularity:** I can declare functions with type hints, default parameters, and docstrings.
- [ ] **Security Modules:** I know the purpose of `os`, `sys`, `re`, `csv`, `json`, `datetime`, `ipaddress`, and `requests`.
- [ ] **String Methods:** I can parse logs using `.strip()`, `.split()`, `.lower()`, and `.join()`.
- [ ] **Security Regex:** I can construct regex patterns to match IPv4 addresses, SHA-256 hashes, emails, and CVE IDs using `re.findall()`.
- [ ] **File Operations:** I understand how to read and write files using `with open(..., mode)`.
- [ ] **Exception Handling:** I can implement structured `try/except FileNotFoundError` blocks to build resilient automation pipelines.

---

## Quick Revision Summary

```
========================================================================================
                                PYTHON AUTOMATION
========================================================================================
1. DATA TYPES    : Lists (Ordered), Sets (Unique IoCs), Dicts (Key-Value/JSON telemetry)
2. CONTROL FLOW  : if/elif/else for severity ranking; for loops for processing log lines
3. MODULES       : re (Regex), ipaddress (Subnets), requests (APIs), hashlib (SHA-256)
4. REGEX PATTERNS: IPv4: r"\b(?:\d{1,3}\.){3}\d{1,3}\b" | SHA-256: r"\b[a-fA-F0-9]{64}\b"
5. FILE I/O      : with open("logs.txt", "r") as f: for line in f: (Memory efficient)
6. MODES         : "r" (Read), "w" (Overwrite), "a" (Append log entries)
7. EXCEPTIONS    : try ... except (FileNotFoundError, PermissionError) as e:
========================================================================================
```