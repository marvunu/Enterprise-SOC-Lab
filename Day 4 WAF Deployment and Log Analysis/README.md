

---

# Day 4 – WAF Deployment & Log Analysis

##  Objective

After successfully simulating and detecting SQL Injection and command injection attacks in DVWA, the next phase was to implement **preventive security controls**.

Day 4 focused on:

- Deploying a Web Application Firewall (WAF)
- Blocking SQL Injection attempts at the web layer
- Ingesting WAF logs into Wazuh
- Validating detection within the SIEM dashboard
- Correlating custom detection rules with WAF blocks

This phase transitioned the lab from **pure detection** to **active defense + monitoring**.

---

## 1️⃣ Installing ModSecurity (WAF)

The WAF was deployed on the Web Server (DVWA machine).

### Install ModSecurity

```bash
sudo apt update
sudo apt install libapache2-mod-security2 -y
````

### Enable Security Module

```bash
sudo a2enmod security2
sudo systemctl restart apache2
```

### Verify Module is Loaded

```bash
sudo apachectl -M | grep security
```

**Expected Output:**

```bash
security2_module (shared)
```

---

## 2️⃣ Enabling OWASP Core Rule Set (CRS)

Ubuntu 24.04 installs CRS in:

```
/usr/share/modsecurity-crs/
```

Edit Apache ModSecurity configuration:

```bash
sudo nano /etc/apache2/mods-enabled/security2.conf
```

Add:

```apache
IncludeOptional /usr/share/modsecurity-crs/owasp-crs.load
IncludeOptional /usr/share/modsecurity-crs/rules/*.conf
```

Restart Apache:

```bash
sudo systemctl restart apache2
```

---

## 3️⃣ Enabling Blocking Mode

By default, ModSecurity runs in **DetectionOnly** mode.

Edit:

```bash
sudo nano /etc/modsecurity/modsecurity.conf
```

Change:

```
SecRuleEngine DetectionOnly
```

To:

```
SecRuleEngine On
```

Restart Apache:

```bash
sudo systemctl restart apache2
```

At this point, ModSecurity was fully active and enforcing rules.

---

## 4️⃣ Testing SQL Injection Blocking

**Test Payload Used:**

```
1 OR 1=1
```

### Result

* HTTP `403 Forbidden`
* SQLi no longer executed
* WAF blocked request

This confirmed:

* CRS rule evaluation working
* Blocking mode enabled
* Anomaly score enforcement active

---

## 5️⃣ Verifying ModSecurity Logs

Audit logs located at:

```
/var/log/apache2/modsec_audit.log
```

Confirmed blocking entry:

```
Action: Intercepted (phase 2)
Engine-Mode: "ENABLED"
```

Additionally, Apache error log recorded:

```
ModSecurity: Access denied with code 403
[id "949110"]
[msg "Inbound Anomaly Score Exceeded"]
[severity "CRITICAL"]
```

---

## 6️⃣ Ingesting WAF Logs into Wazuh

Initially, ingestion failed due to incorrect log format configuration.

### Correct Configuration:

```xml
<localfile>
  <log_format>full_command</log_format>
  <location>/var/log/apache2/modsec_audit.log</location>
</localfile>
```

`full_command` was required because ModSecurity audit logs are **multi-line structured logs**, not standard Apache format.

After updating:

```bash
sudo systemctl restart wazuh-agent
```

Verification:

```bash
sudo tail -n 30 /var/ossec/logs/ossec.log
```

Expected:

```
Analyzing file: '/var/log/apache2/modsec_audit.log'
```

---

## 7️⃣ Confirming Ingestion on SIEM

On the SIEM:

```bash
sudo tail -f /var/ossec/logs/archives/archives.log
```

After triggering SQLi again, ModSecurity entries appeared in real-time.

---

## 8️⃣ Wazuh Detection Results

Two layers of detection were observed:

### 🔹 Custom SQL Injection Rule (Created Earlier)

* **Rule ID:** 100300 (Level 10)
* Triggered from `Apache access.log`
* Message: Possible SQL Injection attempt detected (DVWA)

---

### 🔹 Built-In ModSecurity Detection (Automatic)

* **Rule ID:** 30411 (Level 7)
* Message: ModSecurity: Rejected a query

Embedded CRS metadata:

```
[id "949110"]
[msg "Inbound Anomaly Score Exceeded"]
[severity "CRITICAL"]
```

This confirmed:

* WAF blocked attack
* WAF logged attack
* Wazuh parsed `error.log`
* Built-in ModSecurity rule fired
* Alert visible in Security Events dashboard

---

## 9️⃣ Final Defensive Flow Achieved

```text
Attacker
   ↓
Web Server
   ↓
ModSecurity (WAF)
   ↓ (403 Block)
Apache Logs
   ↓
Wazuh Agent
   ↓
Wazuh Manager
   ↓
Security Events Dashboard
```

The environment now supports:

* Attack simulation
* Web attack detection
* WAF prevention
* SIEM visibility
* Log correlation
* Real-time alerting

---

## 🔟 Key Lessons Learned

* Detection ≠ Prevention
* POST body attacks are not visible in `access.log`
* WAF operates before application execution
* Log ingestion format matters (`full_command` vs `syslog`)
* Layered defense produces stronger telemetry
* Built-in Wazuh rules can detect ModSecurity events without custom rule creation

---

# ✅ Day 4 Summary

| Component               | Status |
| ----------------------- | ------ |
| SQL Injection Blocking  | ✅      |
| WAF Enforcement         | ✅      |
| CRS Active              | ✅      |
| WAF Log Ingestion       | ✅      |
| SIEM Visibility         | ✅      |
| Built-In Rule Detection | ✅      |
| Custom Detection Rule   | ✅      |

---

##  Conclusion

Day 4 successfully transitioned the lab from **monitoring-only** to **active defense with correlated detection**.

```

---

If you'd like, I can also:

- Add a **professional GitHub badge header**
- Format it to match your existing Day 1–3 structure
- Add a **“Project Architecture” diagram section**
- Make it more recruiter-friendly for portfolio impact**

Just tell me the vibe — technical documentation or job-interview ready 🚀
```
