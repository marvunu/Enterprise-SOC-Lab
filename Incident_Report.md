

---

````markdown
 Enterprise Cloud SOC Lab
 Incident Response Report
 SQL Injection & SSH Attack Simulation



 1. Executive Summary

On February 23, 2026, multiple malicious activities were detected within the Enterprise Cloud SOC Lab environment.

Activities observed:

- SQL Injection attempt against DVWA
- SSH brute-force attempts against Cowrie honeypot
- Simulated successful SSH login inside honeypot

The SQL Injection attack was automatically blocked by **ModSecurity (OWASP CRS)**.

All logs were ingested into **Wazuh SIEM**, correlated, and analyzed.

Result: No production systems were compromised.  
All malicious activity was either blocked or contained within the lab.



 2. Environment Overview

 Architecture Components

Public Subnet
- Apache Web Server (DVWA)
- Cowrie SSH Honeypot

Private Subnet
- Wazuh Manager (SIEM)

WAF
- ModSecurity
- OWASP Core Rule Set (CRS)

Monitoring
- Wazuh Agent on Web Server
- Centralized log ingestion



 3. Incident Identification

 3.1 SQL Injection Attempt

Target: DVWA  
Vector: HTTP GET  

Payload
sql
1 OR 1=1
````

**Access Log Entry**

```
GET /DVWA/vulnerabilities/sqli/?id=1+OR+1%3D1
```

**Response Code:** 403 Forbidden

---

# 3.2 WAF Detection

**Apache Error Log**

```log
ModSecurity: Access denied with code 403
[id "949110"]
[msg "Inbound Anomaly Score Exceeded"]
[severity "CRITICAL"]
```

**Wazuh Alert**

* Rule ID: 30411
* Description: ModSecurity rejected a query
* Severity: 7

---

### 3.3 Custom SQL Injection Rule

* Rule ID: 100300
* Severity: 10
* Description: Possible SQL Injection attempt detected (DVWA)

---

### 3.4 SSH Brute Force (Cowrie)

**Failed Login**

```json
cowrie.login.failed
username: root
password: root
```

**Successful Honeypot Login**

```json
cowrie.login.success
username: root
password: sdfghjk
```

---

## 4. Incident Timeline

| Time (UTC) | Event                               |
| ---------- | ----------------------------------- |
| 09:51:10   | SQL Injection attempt initiated     |
| 09:51:10   | ModSecurity blocked request         |
| 09:51:11   | Custom SQLi rule triggered          |
| 09:51:11   | Built-in ModSecurity rule triggered |
| Later      | SSH brute-force attempts            |
| Later      | Honeypot login recorded             |

---

## 5. Detection & Analysis

### Log Sources

| Source            | Location                            |
| ----------------- | ----------------------------------- |
| Apache Access     | `/var/log/apache2/access.log`       |
| Apache Error      | `/var/log/apache2/error.log`        |
| ModSecurity Audit | `/var/log/apache2/modsec_audit.log` |
| Cowrie Logs       | `/var/log/cowrie/cowrie.json`       |

### Detection Layers

**Layer 1 – Application**

* Custom SQLi rule (100300)

**Layer 2 – WAF**

* OWASP CRS anomaly scoring
* Rule 949110 triggered

**Layer 3 – SIEM**

* Wazuh rule 30411
* Centralized alerting
* Dashboard visibility

---

## 6. Containment

### SQL Injection

* Blocked (HTTP 403)
* No database execution
* No data exfiltration

### SSH Brute Force

* Attacker isolated in honeypot
* No real system access
* Logged for analysis

---

## 7. Impact Assessment

| Threat              | Impact | Status        |
| ------------------- | ------ | ------------- |
| SQL Injection       | None   | Blocked       |
| Web Exploitation    | None   | Prevented     |
| SSH Brute Force     | None   | Contained     |
| Credential Exposure | None   | Honeypot only |

---

## 8. Root Cause

* Intentionally vulnerable DVWA (lab)
* Public exposure of test environment
* Automated internet scanning
* Effective layered detection controls

---

## 9. Control Performance

| Control           | Result     |
| ----------------- | ---------- |
| Custom SQLi Rule  | Detected   |
| WAF               | Blocked    |
| Wazuh Rules       | Detected   |
| Log Ingestion     | Successful |
| Alert Correlation | Functional |

---

## 10. Lessons Learned

* Application logs alone are insufficient
* WAF inspection required for POST payload visibility
* Proper log format configuration is critical
* Layered defense improves detection accuracy
* Honeypots provide valuable attacker telemetry

---

## 11. Production Recommendations

* Enforce strict WAF blocking mode
* Implement IP rate limiting
* Restrict admin panel exposure
* Enforce MFA on SSH
* Automate IP blocking via SIEM
* Centralized long-term log retention

---

## 12. Conclusion

The Enterprise Cloud SOC Lab demonstrated:

* Attack simulation
* Real-time detection
* Automated prevention
* Log correlation
* SIEM visibility
* Structured incident response

All malicious activity was detected, blocked, and contained without system compromise.

---

## Project Status

| Phase                       | Completion |
| --------------------------- | ---------- |
| Architecture & Segmentation | Complete   |
| Attack Simulation           | Complete   |
| WAF Deployment              | Complete   |
| Detection Engineering       | Complete   |
| Log Monitoring              | Complete   |
| Incident Response           | Complete   |






