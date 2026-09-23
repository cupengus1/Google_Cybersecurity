# Module 4: Tools of the Trade - Linux and SQL

## How to Use This Module

This module focuses on the practical, hands-on technical skills required for security analysts: **Linux command-line mastery** and **SQL database querying**. In real-world security operations, analysts spend significant time navigating headless Linux servers, parsing large log files with Bash utilities, auditing file permissions, and querying relational databases to track unauthorized user activities or correlate security incidents.

Follow the 4-week progression:
1. **Week 1:** OS Architecture, Kernel vs. User Space, Process Management, and the Linux Filesystem Hierarchy.
2. **Week 2:** Bash Shell Utilities, Input/Output Redirection, Text Wrangling (`grep`, `awk`, `sed`), and Log Parsing.
3. **Week 3:** Linux Security Permissions (Symbolic & Octal), SUID/SGID Privileges, and User Account Auditing.
4. **Week 4:** Relational Databases, SQL Filtering, Aggregations, Joins, Security Queries, and SQL Injection Defense.

---

## Week 1: Operating Systems and Command-Line Basics

### What You Should Understand

An **Operating System (OS)** manages hardware resources, controls execution of processes, enforces access permissions, and provides a software abstraction layer for applications. In security operations, analysts must know how the OS isolates processes, handles memory, and organizes critical files.

---

### OS Architecture: Kernel vs. User Space

```
+-------------------------------------------------------------------------+
|                               USER SPACE                                |
|   [ Web Browser ]    [ Bash Shell ]    [ Python Script ]    [ SIEM Agent]|
|                             | (System Calls / API)                      |
+-----------------------------v-------------------------------------------+
|                              KERNEL SPACE                               |
|   +-----------------------------------------------------------------+   |
|   | Process Scheduler | Memory Manager | VFS (File System) | Network|   |
|   | Device Drivers    | Security Module (SELinux / AppArmor)        |   |
|   +-----------------------------------------------------------------+   |
+-------------------------------------------------------------------------+
|                                HARDWARE                                 |
|          [ CPU / Registers ]     [ RAM ]     [ Storage ]     [ NIC ]    |
+-------------------------------------------------------------------------+
```

- **Kernel Space:** Privileged memory layer (Ring 0) where the core kernel executes with direct access to hardware.
- **User Space:** Restricted memory layer (Ring 3) where user applications and non-root services run. Applications request kernel services via **System Calls** (`sys_open`, `sys_read`, `sys_fork`).

---

### Linux Filesystem Hierarchy Standard (FHS)

Unlike Windows (which uses drive letters like `C:\`, `D:\`), Linux organizes all directories and mounted devices into a single unified hierarchical tree starting at the root directory (`/`).

```
                                      / (Root)
                                      |
     +---------+---------+------------+------------+---------+---------+
     |         |         |            |            |         |         |
   /bin      /etc      /var         /home        /proc     /dev      /tmp
(Binaries) (Config)   (Logs)       (Users)     (Processes)(Devices) (Temp)
                        |
                    /var/log
                 (Security Logs)
```

| Directory | Purpose | Security Significance |
| :--- | :--- | :--- |
| `/` | Root directory; the top of the entire filesystem tree. | Everything stems from here. |
| `/bin` / `/sbin` | Essential user binaries and system administrator binaries (`ls`, `ps`, `iptables`). | Target for trojanized replacement binaries. |
| `/etc` | System-wide configuration files (`/etc/passwd`, `/etc/shadow`, `/etc/ssh/sshd_config`). | Critical audit target for privilege escalation and misconfigurations. |
| `/var/log` | System and service log files (`auth.log`, `syslog`, `nginx/access.log`). | Primary evidence location for incident investigations. |
| `/home` | Personal home directories for regular user accounts (`/home/alice`). | Holds user bash histories (`.bash_history`) and SSH keys (`~/.ssh/authorized_keys`). |
| `/root` | Home directory for the superuser (root). | Access restricted exclusively to root. |
| `/tmp` | Temporary world-writable storage directory. | Frequent staging location for malware payloads and exploit scripts. |
| `/proc` | Virtual pseudo-filesystem exposing real-time kernel and process information. | `/proc/[PID]/` shows memory maps, file descriptors, and command strings. |
| `/dev` | Device nodes representing physical and virtual hardware (`/dev/sda`, `/dev/null`). | Raw disk and hardware interface access. |

---

### Process Management & Process States

A **process** is an active instance of a running program with its own allocated PID (Process ID), memory space, and environment variables:
- **`ps aux` / `ps -ef`:** Displays all currently executing processes with user, PID, CPU/Memory utilization, and full launch arguments.
- **`top` / `htop`:** Real-time interactive process viewer.
- **`kill -9 [PID]`:** Sends `SIGKILL` signal to forcefully terminate a rogue process.

---

### Quick Check & Answers

#### Questions
1. *Why is the `/tmp` directory commonly targeted by attackers executing web exploits?*
2. *What is the difference between Kernel Space and User Space?*
3. *Where are system authentication logs stored on Debian/Ubuntu systems?*
4. *What information does the `/proc` directory contain?*
5. *What command displays all running processes with user ownership and command arguments?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** The `/tmp` directory has world-writable permissions (`777` with sticky bit), allowing any low-privileged user or compromised web daemon (e.g., `www-data`) to write and execute scripts without permission errors.
2. **Answer:** Kernel Space executes privileged system code with unrestricted hardware access (Ring 0), while User Space runs user applications with restricted privileges (Ring 3) and must use system calls to request kernel actions.
3. **Answer:** `/var/log/auth.log` (or `/var/log/secure` on RHEL/CentOS systems).
4. **Answer:** It is a dynamic virtual pseudo-filesystem containing real-time kernel parameters and runtime process details organized by PID.
5. **Answer:** `ps aux` or `ps -ef`.
</details>

---

## Week 2: Linux, Shells, and Bash

### What You Should Understand

The **Bash (Bourne Again Shell)** is a command-line interpreter that executes instructions, manipulates text streams, and automates administrative routines. Analysts leverage standard streams, piping, and text utilities to search gigabytes of logs quickly.

---

### Standard Streams, Redirection, and Pipes

Every Linux process automatically opens three standard I/O streams:
- **Standard Input (stdin - File Descriptor 0):** Input from keyboard or pipe (`0<`).
- **Standard Output (stdout - File Descriptor 1):** Normal output stream (`1>` or `>`).
- **Standard Error (stderr - File Descriptor 2):** Error messages stream (`2>`).

```
                           +-------------------+
                           |                   | ---> stdout (1) -> Terminal / File
     stdin (0) ----------> |   LINUX PROCESS   |
   (Keyboard / Pipe)       |                   | ---> stderr (2) -> Terminal / File
                           +-------------------+
```

#### Stream Operators:
- `command > file.txt`: Overwrites `file.txt` with stdout.
- `command >> file.txt`: Appends stdout to the end of `file.txt`.
- `command 2> errors.log`: Redirects stderr error messages to `errors.log`.
- `command &> all_output.log`: Redirects both stdout and stderr to the same file.
- `command1 | command2`: **Pipe** operator; redirects stdout of `command1` into stdin of `command2`.

---

### Essential Linux Commands for Security Analysts

```bash
# Directory Navigation & Inspection
pwd                             # Print working directory
ls -la                          # List all files including hidden (.) with detailed permissions
cd /var/log                     # Change directory

# File Content Inspection
cat /etc/hosts                  # Output entire file to screen
less /var/log/syslog            # Scrollable, searchable paginated viewer (q to quit)
head -n 20 access.log           # View first 20 lines of a file
tail -n 50 access.log           # View last 50 lines of a file
tail -f /var/log/auth.log       # Follow log in REAL-TIME as new events are appended

# File Search & Locating
find / -name "*.conf" 2>/dev/null              # Search filesystem for .conf files, hiding errors
find / -perm -4000 -type f 2>/dev/null         # Hunt for SUID binaries (privilege escalation)
which nmap                                     # Locate executable binary path
```

---

### Text Processing & Log Wrangling (`grep`, `awk`, `sed`, `sort`, `uniq`)

Security analysts frequently extract actionable intelligence from web and authentication logs:

#### 1. `grep` (Global Regular Expression Print)
- `grep "Failed password" /var/log/auth.log`: Find all failed SSH authentication attempts.
- `grep -i "admin" users.txt`: Case-insensitive search.
- `grep -v "127.0.0.1" access.log`: Invert match (exclude localhost entries).
- `grep -E "[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}" logs.txt`: Extended regex for IPv4.

#### 2. Pipeline Chaining Example: Identifying Top Attacking IPs
```bash
grep "Failed password" /var/log/auth.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -nr | head -n 5
```
*Breakdown of the Pipeline:*
1. `grep "Failed password"`: Filters log lines for failed SSH logins.
2. `awk '{print $(NF-3)}'`: Extracts the field containing the attacking source IP address.
3. `sort`: Sorts IP addresses so duplicates are adjacent.
4. `uniq -c`: Counts the frequency of each unique IP address.
5. `sort -nr`: Sorts numerically in descending order.
6. `head -n 5`: Outputs the top 5 most aggressive brute-forcing IP addresses.

---

### Quick Check & Answers

#### Questions
1. *What does the `tail -f` command do and why is it valuable to a SOC analyst?*
2. *What is the difference between `>` and `>>` in Bash redirection?*
3. *What does the command `find / -type f -perm -4000 2>/dev/null` search for?*
4. *How do you suppress error messages from cluttering terminal output in Linux?*
5. *What does the `uniq -c` command require before it can accurately count duplicates?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** `tail -f` continuously monitors and displays new lines appended to a log file in real time as events occur, allowing live monitoring of active attacks.
2. **Answer:** `>` overwrites the destination file with new output, destroying existing contents. `>>` appends new output to the end of the existing file without overwriting.
3. **Answer:** It searches the entire root filesystem for executable files with the **SUID (Set User ID)** permission enabled, while discarding permission-denied error messages to `/dev/null`.
4. **Answer:** By redirecting Standard Error (file descriptor 2) to `/dev/null` using `2>/dev/null`.
5. **Answer:** It requires the input stream to be sorted first (`sort`) because `uniq` only detects adjacent duplicate lines.
</details>

---

## Week 3: Files, Permissions, Users, and Help

### What You Should Understand

Linux is a multi-user operating system. Access control is enforced through **discretionary file permissions** assigned to three entity scopes: the **Owner**, the **Group**, and **Others**. Misconfigured permissions can allow unprivileged accounts to read sensitive data or execute unauthorized privilege escalation exploits.

---

### Anatomy of Linux Permissions

When running `ls -l`, each file displays a 10-character permission string:

```
        -  r w x  r - x  r - -    1 alice security  4096 Sep 23 10:00 script.sh
        |  \___/  \___/  \___/
     Type  Owner  Group  Others
```

1. **File Type Character (Position 1):**
   - `-`: Regular file
   - `d`: Directory
   - `l`: Symbolic link
2. **Permission Triads (Positions 2-10):**
   - **User / Owner (`u`):** Permissions for the user who owns the file.
   - **Group (`g`):** Permissions for members of the file's assigned group.
   - **Others / World (`o`):** Permissions for all other accounts on the system.

---

### Symbolic vs. Octal (Numeric) Permissions

```
+------------------+------------------+---------------+-----------------------------------------------+
| Permission       | Symbolic Code    | Octal Value   | Meaning on File / Meaning on Directory        |
+------------------+------------------+---------------+-----------------------------------------------+
| Read             | `r`              | **4**         | View file contents / List files in directory  |
| Write            | `w`              | **2**         | Modify/delete file / Create/delete files in dir|
| Execute          | `x`              | **1**         | Run program/script / Enter (`cd`) directory   |
| No Permission    | `-`              | **0**         | No access permitted                           |
+------------------+------------------+---------------+-----------------------------------------------+
```

```
Calculation:
Owner:  r w x = 4 + 2 + 1 = 7
Group:  r - x = 4 + 0 + 1 = 5  ===> Permission Mode: 754
Others: r - - = 4 + 0 + 0 = 4
```

#### Modifying Permissions & Ownership:
- `chmod 750 secure_script.sh`: Owner gets `rwx` (7), Group gets `r-x` (5), Others get `---` (0).
- `chmod u+x,g-w file.txt`: Adds execute to owner, removes write from group.
- `chown root:security audit.log`: Changes owner to `root` and group to `security`.
- `chown -R alice:alice /home/alice/project`: Recursively changes ownership of all child files.

---

### Special Permissions: SUID, SGID, and Sticky Bit

| Special Permission | Octal Prefix | Symbolic | Behavior & Security Risk |
| :--- | :--- | :--- | :--- |
| **SUID (Set UID)** | `4xxx` | `rwsr-xr-x` (s on Owner) | File executes with the permissions of the file **owner** (e.g., `root`), not the user running it. If a SUID binary has vulnerabilities, unprivileged users can escalate to root. |
| **SGID (Set GID)** | `2xxx` | `rwxr-sr-x` (s on Group) | Files created inside directory inherit the directory's group ownership. |
| **Sticky Bit** | `1xxx` | `rwxrwxrwt` (t on Others)| In a shared directory (`/tmp`), users can only delete/rename files they own, preventing users from deleting each other's files. |

---

### User & Group Administration Files

Security analysts inspect these files to detect unauthorized accounts or backdoors:
- **`/etc/passwd`:** Publicly readable list of user accounts, UIDs, GIDs, home directories, and default shells (`alice:x:1001:1001:Alice Smith:/home/alice:/bin/bash`).
- **`/etc/shadow`:** Restricted file (`000` or `400` root-only) storing salted cryptographic password hashes and password expiration policies.
- **`/etc/sudoers`:** Configures which users/groups can execute commands with elevated root privileges using `sudo` (edited strictly with `visudo`).

---

### Quick Check & Answers

#### Questions
1. *Convert the symbolic permission `-rwxr-x---` into its 3-digit octal equivalent.*
2. *What does the letter `s` in `-rwsr-xr-x` represent, and why is it important in security?*
3. *Why are password hashes stored in `/etc/shadow` rather than `/etc/passwd`?*
4. *What permission is required on a Linux directory to allow a user to `cd` into it?*
5. *Why should administrators use `visudo` instead of editing `/etc/sudoers` with a standard text editor?*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** **750** (Owner `rwx` = 4+2+1 = 7; Group `r-x` = 4+0+1 = 5; Others `---` = 0).
2. **Answer:** It represents the **SUID (Set User ID)** bit. When executed, the program runs with the privileges of the file owner (typically `root`). Vulnerable or misconfigured SUID binaries are a primary vector for local privilege escalation.
3. **Answer:** `/etc/passwd` must be readable by all system utilities to resolve usernames and UIDs. Storing password hashes in `/etc/shadow` (accessible only by `root`) prevents unprivileged users from extracting hashes for offline dictionary and brute-force cracking.
4. **Answer:** The **Execute (`x`)** permission.
5. **Answer:** `visudo` performs strict syntax checking before saving changes, preventing syntax errors that could permanently lock all users out of administrative `sudo` access.
</details>

---

## Week 4: SQL and Database Queries

### What You Should Understand

**Structured Query Language (SQL)** is the standard language for interacting with Relational Database Management Systems (RDBMS) like MySQL, PostgreSQL, Microsoft SQL Server, and SQLite. Security analysts use SQL to search log repositories, inventory assets, audit employee access rights, and investigate SQL injection (SQLi) attacks.

---

### RDBMS Architecture: Tables, Keys, and Schema

```
                               TABLE: employees
+----+-----------+---------------+---------------+---------------+
| id | username  | department    | role          | account_status|
+----+-----------+---------------+---------------+---------------+
| 1  | jdoe      | Engineering   | Developer     | Active        |
| 2  | asmith    | Finance       | Accountant    | Active        |
| 3  | mjohnson  | Security      | SOC Analyst   | Active        |
| 4  | badams    | HR            | HR Manager    | Suspended     |
+----+-----------+---------------+---------------+---------------+
  ^        ^
  |        +--- Column / Field (Attribute)
  +------------ Primary Key (Unique record identifier)
```

- **Primary Key (PK):** A column containing uniquely identifying values for each row.
- **Foreign Key (FK):** A column in one table that references the Primary Key of another table, establishing a relational link.

---

### Fundamental SQL Syntax for Security Analysts

#### 1. Basic Data Retrieval & Deduplication
```sql
-- Retrieve specific columns
SELECT username, role, department FROM employees;

-- Retrieve all unique source IP addresses from network logs
SELECT DISTINCT source_ip FROM network_traffic;
```

#### 2. Filtering with `WHERE`, Comparison & Logical Operators
```sql
-- Filter failed logins from external IP space
SELECT username, source_ip, timestamp 
FROM user_logins 
WHERE login_status = 'FAILED' 
  AND source_ip NOT LIKE '192.168.%'
  AND timestamp >= '2026-09-20 00:00:00';
```
- **Operators:** `=`, `!=` (or `<>`), `>`, `<`, `>=`, `<=`, `BETWEEN`, `IN ('val1', 'val2')`.
- **Wildcard Pattern Matching (`LIKE`):**
  - `%` represents zero, one, or multiple characters (`'%malicious%'`).
  - `_` represents exactly one single character (`'user_'`).

#### 3. Sorting & Result Limiting
```sql
-- Top 10 most recent critical vulnerability findings
SELECT cve_id, cvss_score, affected_host, discovered_date
FROM vulnerability_scans
WHERE cvss_score >= 9.0
ORDER BY cvss_score DESC, discovered_date DESC
LIMIT 10;
```

---

### Aggregation & Grouping (`COUNT`, `GROUP BY`, `HAVING`)

Analysts aggregate telemetry data to identify anomalous spikes:

```sql
-- Identify IP addresses with more than 50 failed login attempts (Brute Force Detection)
SELECT source_ip, COUNT(*) AS failed_attempts
FROM user_logins
WHERE login_status = 'FAILED'
GROUP BY source_ip
HAVING COUNT(*) > 50
ORDER BY failed_attempts DESC;
```
> [!NOTE]
> `WHERE` filters individual rows **before** aggregation; `HAVING` filters aggregated groups **after** `GROUP BY`.

---

### Relational Table Joins (`INNER JOIN`, `LEFT JOIN`)

```
   TABLE: users                                  TABLE: access_logs
+---------+----------+                        +---------+---------+------------+
| user_id | username |                        | log_id  | user_id | server_ip  |
+---------+----------+                        +---------+---------+------------+
| 101     | alice    | <----\                 | 1       | 101     | 10.0.0.5   |
| 102     | bob      |       \-- Matched on --| 2       | 101     | 10.0.0.12  |
| 103     | charlie  |           user_id      | 3       | 102     | 10.0.0.5   |
+---------+----------+                        +---------+---------+------------+
```

```sql
-- Correlate log entries with user identities and email addresses
SELECT 
    users.username,
    users.email,
    access_logs.server_ip,
    access_logs.access_time
FROM access_logs
INNER JOIN users 
    ON access_logs.user_id = users.user_id
WHERE access_logs.action = 'UNAUTHORIZED_ACCESS';
```

---

### SQL Injection (SQLi) Overview & Remediation

SQL Injection occurs when untrusted user input is concatenated directly into a dynamic database query:

```sql
-- Vulnerable Dynamic Query:
SELECT * FROM users WHERE username = 'USER_INPUT' AND password = 'PASSWORD_INPUT';

-- Attacker Input in Username Field:
admin' OR '1'='1' --

-- Executed Query in Backend Database:
SELECT * FROM users WHERE username = 'admin' OR '1'='1' -- ' AND password = '...';
-- (Bypasses authentication because '1'='1' is always TRUE, and '--' comments out the password check)
```

#### Defensive Mitigation:
- **Prepared Statements / Parameterized Queries:** Ensures user input is treated strictly as data, never as executable SQL code.
- **Input Validation & ORM Frameworks:** Whitelist expected input formats.
- **Principle of Least Privilege:** Ensure the database user account used by web applications lacks `DROP TABLE`, `GRANT`, or `SHUTDOWN` permissions.

---

### Quick Check & Answers

#### Questions
1. *What is the difference between the `WHERE` clause and the `HAVING` clause in SQL?*
2. *How does an `INNER JOIN` differ from a `LEFT JOIN`?*
3. *What does the SQL wildcard `%` match when used with the `LIKE` operator?*
4. *How do Parameterized Queries (Prepared Statements) prevent SQL Injection attacks?*
5. *Write a SQL snippet that counts total records in a table named `alerts` where `severity = 'High'`.*

#### Detailed Answers
<details>
<summary><b>Click to reveal answers</b></summary>

1. **Answer:** `WHERE` filters individual records before grouping and aggregation occur. `HAVING` filters grouped data sets *after* the `GROUP BY` clause has been evaluated.
2. **Answer:** `INNER JOIN` returns only rows that have matching values in both tables. `LEFT JOIN` returns all rows from the left table, plus matched rows from the right table (filling with `NULL` where no match exists).
3. **Answer:** `%` matches any string of zero, one, or multiple characters (e.g., `LIKE '%admin%'` matches `admin`, `system_admin`, or `administrator`).
4. **Answer:** Parameterized queries pre-compile the SQL statement structure on the database engine and pass user inputs separately as literal parameter values, preventing malicious SQL control characters from altering query logic.
5. **Answer:** 
   ```sql
   SELECT COUNT(*) FROM alerts WHERE severity = 'High';
   ```
</details>

---

## Module 4 Comprehensive Review Checklist

- [ ] **OS Architecture:** I understand Kernel Space vs. User Space, System Calls, and Process states.
- [ ] **FHS Hierarchy:** I know the operational and security roles of `/`, `/bin`, `/etc`, `/var/log`, `/proc`, and `/tmp`.
- [ ] **Bash Navigation & Search:** I can comfortably use `pwd`, `ls -la`, `cd`, `head`, `tail -f`, `less`, and `find`.
- [ ] **Text Wrangling:** I can construct pipelines combining `grep`, `awk`, `sed`, `sort`, `uniq -c`, and standard I/O redirection (`>`, `>>`, `2>`, `|`).
- [ ] **Linux Permissions:** I can calculate and assign symbolic and octal permissions (`chmod 750`, `chmod u+x`), and manage ownership (`chown`).
- [ ] **Special Bits:** I understand the security risks of **SUID (4000)** and the purpose of the **Sticky Bit (1000)**.
- [ ] **Account Files:** I understand the structure of `/etc/passwd`, `/etc/shadow`, and `/etc/sudoers`.
- [ ] **SQL Mastery:** I can write queries using `SELECT`, `WHERE`, `ORDER BY`, `LIKE`, `GROUP BY`, `HAVING`, and `INNER JOIN`.
- [ ] **SQLi Awareness:** I understand the mechanics of SQL Injection and know how Parameterized Queries mitigate the threat.

---

## Quick Revision Summary

```
========================================================================================
                                 LINUX AND SQL TOOLS
========================================================================================
1. FHS KEY PATHS : /var/log (Logs), /etc (Config), /tmp (World-writable), /proc (Processes)
2. STREAMS       : 0 (stdin), 1 (stdout), 2 (stderr) | > (Overwrite), >> (Append), | (Pipe)
3. LOG PIPELINE  : grep "Failed" /var/log/auth.log | awk '{print $NF}' | sort | uniq -c | sort -nr
4. PERMISSIONS   : r=4, w=2, x=1 | chmod 755 (rwxr-xr-x) | SUID (4xxx), SGID (2xxx), Sticky (1xxx)
5. USERS/SHADOW  : /etc/passwd (World-readable accounts) | /etc/shadow (Root-only password hashes)
6. SQL QUERIES   : SELECT cols FROM table WHERE cond GROUP BY col HAVING agg_cond ORDER BY col LIMIT n
7. SQL JOINS     : INNER JOIN (Exact matches both sides) | LEFT JOIN (All left + matched right)
8. SQLi DEFENSE  : Always implement Prepared Statements (Parameterized Queries) and Least Privilege
========================================================================================
```