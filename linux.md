# Linux Logs Analysis Report (SOC Fundamentals)

## Overview
This report documents hands-on Linux log analysis performed as part of cybersecurity and SOC skill development. The objective was to understand where Linux logs are stored, how to safely read them, and how to detect authentication-related security events such as SSH logins, failed login attempts, and privilege escalation.

The analysis focused on **authentication logs**, **binary login records**, and **timeline reconstruction**, using standard Linux command-line tools commonly used in SOC and incident response environments.

---

## Environment
- **Operating System:** Ubuntu Linux
- **Kernel Version:** 6.8.0-60-generic
- **Log Directory:** `/var/log/`
- **User Account:** shishir

---

## Log Locations Identified
The following log files and directories were identified and analysed:

- `/var/log/auth.log` – Authentication and authorization events  
- `/var/log/syslog` – System-wide events and service messages  
- `/var/log/kern.log` – Kernel-level messages  
- `/var/log/wtmp` – Binary log of successful logins  
- `/var/log/btmp` – Binary log of failed login attempts  
- Rotated logs such as `auth.log.1`, `auth.log.2.gz`

---

## Commands Used and Their Purpose

### 1. System Identification
```bash
uname -a
```
**Purpose:** Displays kernel version, architecture, and OS details. Used to confirm system environment during analysis and vulnerability context.

---

### 2. Listing Available Logs
```bash
ls /var/log/
```
**Purpose:** Identifies available log files. SOC analysts use this to verify log coverage and detect missing or tampered logs.

---

### 3. Viewing Authentication Logs Safely
```bash
sudo less /var/log/auth.log
```
**Purpose:** Safely reads authentication logs in read-only mode. Allows scrolling and searching without modifying log contents.

---

### 4. Searching for SSH Activity
```bash
sudo grep ssh /var/log/auth.log
```
**Purpose:** Filters authentication logs to show SSH-related activity, including login attempts and service status.

---

### 5. Detecting Failed Login Attempts
```bash
sudo grep "Failed password" /var/log/auth.log
```
**Purpose:** Identifies failed authentication attempts, a key indicator of brute-force or credential guessing attacks.

---

### 6. Detecting Successful Logins
```bash
sudo grep "Accepted" /var/log/auth.log
```
**Purpose:** Identifies successful authentication events, including username, source IP, and authentication method.

---

### 7. Monitoring Privilege Escalation (sudo usage)
```bash
sudo grep sudo /var/log/auth.log
```
**Purpose:** Detects privilege escalation attempts using `sudo`. SOC analysts correlate this with logins to identify post-compromise behaviour.

---

### 8. Viewing Compressed (Rotated) Logs
```bash
sudo zless /var/log/auth.log.2.gz
```
**Purpose:** Reads historical authentication logs without extracting compressed files. Used for threat hunting and historical investigations.

---

### 9. Real-Time Log Monitoring
```bash
sudo tail -f /var/log/auth.log
```
**Purpose:** Monitors authentication activity in real time. Used during incident response and alert validation.

---

### 10. Viewing Successful Login History (Binary Logs)
```bash
last
```
**Purpose:** Reads `/var/log/wtmp` to display successful login sessions, SSH access, session duration, and system reboots. Binary logs are highly trusted and difficult to tamper with.

---

### 11. Viewing Failed Login History (Binary Logs)
```bash
sudo lastb
```
**Purpose:** Reads `/var/log/btmp` to display failed login attempts. Used to confirm brute-force activity with high confidence.

---

## Timeline Reconstruction

### Extracting Key Fields from Login History
```bash
last | awk '{print $1, $3, $4, $5, $6, $7}' | head -15
```
**Purpose:** Creates a simplified login timeline showing user, source IP, and timestamp. Used in incident response to reconstruct attacker or user activity.

---

### Aggregating User–IP Relationships
```bash
last | awk '{print $1, $3}' | sort | uniq -c | sort -nr
```
**Purpose:** Counts how many times each user logged in from each source IP. Used to establish a baseline and detect anomalous access paths.

---

## Findings
- All successful logins were performed by user **shishir**
- The dominant source IP was **10.0.2.2** (expected local/VM host)
- No unknown users or external IP addresses were observed
- No failed login attempts were recorded in `btmp`
- No suspicious `sudo` activity was detected
- System reboots aligned with normal VM or update behaviour

This indicates a **clean authentication baseline** with no evidence of brute-force attacks, credential compromise, or unauthorized access.

---

## SOC Interpretation
The analysis demonstrates the ability to:
- Safely analyse Linux authentication logs
- Distinguish successful vs failed login attempts
- Correlate SSH access with privilege escalation
- Use binary logs (`wtmp`, `btmp`) for forensic validation
- Establish a baseline and identify deviations
- Reconstruct authentication timelines

These skills directly align with **SOC Tier 1 / Junior Security Analyst responsibilities**.

---

## Conclusion
This exercise provided hands-on experience in Linux log analysis using industry-standard tools and workflows. The ability to interpret authentication logs, validate events using binary records, and reason about security significance forms a strong foundation for SIEM-based detection and incident response activities.
