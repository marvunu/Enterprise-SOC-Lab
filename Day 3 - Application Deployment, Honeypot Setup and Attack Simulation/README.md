
---

# 📅 Day 3 — Application Deployment, Honeypot Setup & Attack Simulation

Day 3 transitioned the lab from infrastructure deployment into operational security testing.

This phase focused on deploying vulnerable services, implementing centralized logging, and executing structured attack simulations to validate detection capabilities.

---

## 🎯 Objective

The objective of Day 3 was to:

* Deploy a vulnerable web application (DVWA)
* Install and configure Apache + MySQL
* Deploy Cowrie SSH honeypot
* Install and configure Wazuh SIEM
* Centralize log ingestion
* Execute structured attack simulation
* Build custom detection rules

This phase validates that the SOC architecture is not only deployed — but operational.

---

## 🧠 Skills Developed

* Apache + PHP installation
* MySQL database setup
* DVWA configuration
* SSH honeypot deployment
* Wazuh agent & manager configuration
* JSON log ingestion
* Custom rule development
* Detection chain debugging
* Brute-force correlation logic

---

## 🛠 Tools & Services Used

* Apache2
* MySQL
* DVWA
* Cowrie Honeypot
* Wazuh Manager
* Wazuh Agent
* OpenSearch Dashboard
* Kali Linux

---

# 🌐 Step 1 — Apache & DVWA Deployment (Web Server)

---

## Install Apache & PHP
![Day 3](https://i.imgur.com/36023bO.png)
```bash
sudo apt update
sudo apt install apache2 php libapache2-mod-php php-mysqli -y

```

Verify Apache:
![Day 3](https://i.imgur.com/Kf8lz0S.png)
```bash
sudo systemctl status apache2

```

---

## Install MySQL

```bash
sudo apt install mysql-server -y
sudo mysql_secure_installation
```

Start MySQL:
![Day 3](https://i.imgur.com/5rytGPC.png)
```bash
sudo mysql_secure_installation
```

---

## Deploy DVWA
![Day 3](https://i.imgur.com/fOfiUFV.png)

```bash
cd /var/www/html
sudo git clone https://github.com/digininja/DVWA.git
sudo chown -R www-data:www-data DVWA
sudo chmod -R 755 DVWA
```

Create Database:

```bash
sudo mysql -u root -p
```

Inside MySQL:
![Day 3](https://i.imgur.com/yIAcbOE.png)
```sql
CREATE DATABASE dvwa;
exit;
```

Navigate to:
![Day 3](https://i.imgur.com/XzZv2mX.png)
```
http://<WebServerIP>/DVWA/setup.php
```

Click: **Create / Reset Database**

DVWA successfully deployed.

---

# 🐝 Step 2 — Cowrie Honeypot Deployment

---

## Install Dependencies
![Day 3](https://i.imgur.com/TxDeAlo.png)
```bash
sudo apt update
sudo apt install git python3-venv python3-pip libssl-dev libffi-dev build-essential -y
git clone https://github.com/allthingsit117/Cowrie-Installer.git
```

---

## Create Cowrie User

```bash
sudo adduser cowrie
sudo su - cowrie
```

---

## Clone & Setup Cowrie

```bash
git clone https://github.com/cowrie/cowrie.git
cd cowrie
python3 -m venv cowrie-env
source cowrie-env/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

---

## Configure Cowrie

```bash
cp etc/cowrie.cfg.dist etc/cowrie.cfg
vi etc/cowrie.cfg
```

Set:

```
listen_port = 2222
```

Start Cowrie:
![Day 3](https://i.imgur.com/QnAKqa3.png)
```bash
cowrie start
```

Verify:

```bash
cowrie status
```

---

# 📊 Step 3 — Wazuh SIEM Deployment (Private Subnet)

---

## Install Wazuh Manager (SIEM Server)
![Day 3](https://i.imgur.com/4Nikmjj.png)
```bash
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
sudo bash wazuh-install.sh -a
```

---

## Install Wazuh Agent (Web + Cowrie Servers)
![Day 3](https://i.imgur.com/25lTPO8.png)
```bash
sudo apt install wazuh-agent -y
```

Configure manager IP in:
![Day 3](https://i.imgur.com/Dt11CZ6.png)
```
/var/ossec/etc/ossec.conf
```

Restart agent:

```bash
sudo systemctl restart wazuh-agent
```

Verify connection:

```bash
sudo systemctl status wazuh-agent
```
![Day 3](https://i.imgur.com/jwGBGDA.png)
![Day 3](https://i.imgur.com/0xneYyW.png)

---

# 📥 Step 4 — Log Ingestion Configuration

![Day 3](https://i.imgur.com/SAR3sqY.png)
Rule to detect SQL Injection on the SIEM
---

## Monitor Apache Logs (Web Server)
![Day 3](https://i.imgur.com/bKcScmf.png)
Inside `/var/ossec/etc/ossec.conf`:

```xml
<localfile>
  <log_format>apache</log_format>
  <location>/var/log/apache2/access.log</location>
</localfile>

<localfile>
  <log_format>apache</log_format>
  <location>/var/log/apache2/error.log</location>
</localfile>
```

Restart agent:

```bash
sudo systemctl restart wazuh-agent
```


---

## Monitor Cowrie JSON Logs
![Day 3](https://i.imgur.com/RemmHRw.png)
![Day 3](https://i.imgur.com/KK14FzT.png)
![Day 3](https://i.imgur.com/TPbedHj.png)
Rules to detect attempted login, failed login attempt and successful login attempt into cowrie

```xml
<localfile>
  <log_format>json</log_format>
  <location>/var/log/cowrie/cowrie.json</location>
</localfile>
```

Restart agent:

```bash
sudo systemctl restart wazuh-agent
```

---

# ⚔️ Step 5 — Structured Attack Simulation

---

## Phase 1 — SSH Brute Force (Cowrie)

From Kali:
![Day 3](https://i.imgur.com/eAal7F7.png)

```bash
for i in {1..10}; do ssh root@<CowrieIP> -p 2222; done
```

Telemetry captured:

* cowrie.login.failed
* cowrie.session.closed
* src_ip
* username
* password attempts

---

## Phase 2 — Successful Login Simulation
![Day 3](https://i.imgur.com/QpELxMm.png)
```bash
ssh root@<CowrieIP> -p 2222
```

Generates:

![Day 3](https://i.imgur.com/SjrNQlu.png)

```
"eventid":"cowrie.login.success"
```

---

## Phase 3 — SQL Injection (DVWA)

Navigate to:

```
DVWA → SQL Injection
```

Payload:

```
1 OR 1=1
```
![Day 3](https://i.imgur.com/2SdPrIZ.png)

Apache logs:

```
GET /DVWA/vulnerabilities/sqli/?id=1+OR+1%3D1
```
![Day 3](https://i.imgur.com/DiwZO9a.png)

---

# 🛠 Custom Detection Engineering

---

## Detect Failed SSH Login

```xml
<rule id="100201" level="8">
  <decoded_as>json</decoded_as>
  <field name="eventid">cowrie.login.failed</field>
  <description>Cowrie failed SSH login attempt</description>
</rule>
```

---

## Brute Force Correlation Rule

```xml
<rule id="100202" level="12" frequency="5" timeframe="60">
  <if_matched_sid>100201</if_matched_sid>
  <same_field>src_ip</same_field>
  <description>Brute-force attack detected</description>
</rule>
```

---

## SQL Injection Detection Rule

Attached to production Apache parent rule:

```xml
<rule id="100300" level="10">
  <if_sid>31100</if_sid>
  <match>OR+1%3D1</match>
  <description>Possible SQL Injection attempt detected</description>
</rule>
```

Restart manager after rule creation:

```bash
sudo systemctl restart wazuh-manager
```

---

# 🔍 Validation

| Test                     | Result              |
| ------------------------ | ------------------- |
| Cowrie Failed Login      | Detected            |
| Brute Force (5 attempts) | Escalated           |
| Cowrie Successful Login  | High Severity Alert |
| SQL Injection            | Detected            |
| Log Ingestion            | Operational         |

---



# 🔐 Security Posture After Day 3

The environment now includes:

* Active honeypot telemetry
* Web application vulnerability simulation
* Centralized SIEM ingestion
* Custom detection rules
* Brute-force correlation logic
* Verified attack-to-alert pipeline

The SOC is now operational.

---
# ➡️ Next Phase (Day 4)

* AWS WAF Deployment
* Firewall Hardening
* Rate Limiting
* Geo-IP Blocking
* Active Response Automation

---

If this documentation helps replicate the environment, consider starring the repository.




---
---

## 🛠 Issue 2 — SQL Injection Detection Not Firing

**Symptom:**  
Logs were visible in `archives.log`, but no alerts were generated in `alerts.log` or the Wazuh Dashboard.

**Root Cause:**  
Apache access logs were matching rule **31100** (a level 0 grouping rule).  
The custom SQL injection rule was incorrectly chained to the wrong SID, so it never entered the correct rule inheritance chain.

**Resolution:**  
Used `wazuh-logtest` to identify the correct parent rule in production:

```bash
sudo /var/ossec/bin/wazuh-logtest
After confirming the live parent rule, the custom rule was correctly attached to:

<if_sid>31100</if_sid>
Once chained to the proper SID, detection began firing immediately.

Lesson Learned:
Always identify the actual parent SID in production before chaining custom rules.
Logtest output without full agent context can be misleading.



