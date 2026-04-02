# Security Incident Report – SSH Brute Force Attack

## 1. Executive Summary

An SSH brute-force attack was detected against a Linux host through automated monitoring in Splunk. Multiple authentication failures were observed from a single external IP address targeting the root account. A scheduled alert was configured to enable continuous detection of similar activity.

---

## 2. Detection Method

The incident was detected using a scheduled Splunk alert designed to identify excessive SSH authentication failures from a single source IP within a short time window.

### Detection Query

    index=auth_logs sshd "authentication failure"
    | stats count as failures by rhost
    | where failures >= 10

---

## 3. Indicators of Compromise (IOCs)

| Indicator Type | Value |
|---------------|-------|
| Source IP | 150.183.249.110 |
| Target Service | SSH |
| Target Account | root |
| Log Source | auth.log |

---

## 4. Timeline Analysis

Multiple authentication failures were observed within seconds, indicating automated brute-force behaviour. The attacker repeatedly attempted to authenticate against the SSH service using the root account. No successful authentication events were identified for the attacker IP during the investigation period.

---

## 5. Impact Assessment

- **Confidentiality:** No confirmed compromise  
- **Integrity:** No changes detected  
- **Availability:** No service disruption  

**Impact Level:** Medium (attempted intrusion)

---

## 6. Response & Mitigation

Recommended response actions include:

- Blocking the malicious source IP at the firewall
- Disabling direct SSH root login
- Enforcing SSH key-based authentication
- Deploying Fail2Ban to automatically block repeated failures
- Continuing monitoring through Splunk alerting

---

## 7. Lessons Learned

- SSH services exposed to the internet are frequent targets of brute-force attacks
- Automated alerting significantly reduces detection and response time
- Effective log analysis and alerting are core SOC analyst capabilities
