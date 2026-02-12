# 📅 Day 2 — EC2 Deployment & Secure Access Architecture

Day 2 focused on deploying compute infrastructure within the segmented VPC created on Day 1 and implementing controlled access through structured Security Groups.

This phase transitioned the architecture from theoretical network segmentation into operational infrastructure.

---

## 🎯 Objective

The objective of Day 2 was to:

- Launch EC2 instances in the correct subnets
- Apply disciplined Security Group configurations
- Validate private subnet isolation
- Implement a secure jump-host model
- Prepare the environment for SOC tooling and attack simulation

This phase reinforces workload placement awareness and access control best practices.

---

## 🧠 Skills Developed

- Subnet-aware EC2 deployment
- Security Group architecture design
- Controlled exposure modelling
- SSH agent forwarding configuration
- Bastion (jump host) access implementation
- Troubleshooting AWS instance configuration issues
- Understanding EBS snapshot volume constraints

---

## 🛠 Tools & Services Used

- Amazon EC2
- Security Groups
- Key Pairs
- VPC Subnet Routing
- SSH Agent Forwarding

---

# 🏗 Infrastructure Overview (End of Day 2)

### Public Subnet (10.0.1.0/24)
- Web-Server (Jump Host)
- Cowrie-Server
- Kali-Attacker

### Private Subnet (10.0.2.0/24)
- SIEM-Server

The SIEM server remains isolated from direct internet access.

---

## 📌 Ref 1: Day 2 Infrastructure Layout

![Day 2 Infrastructure Diagram](https://i.imgur.com/GtlXR8w.png)

---

# 🔐 Step 1 — Security Group Configuration

Four Security Groups were created inside `SOC-Lab-VPC`.

---

## 1️⃣ Kali-SG

Inbound:
- SSH (22) → My IP only

Outbound:
- Allow all

Purpose:
Restrict attacker machine access to authorized user only.

---

## 2️⃣ WebApp-SG

Inbound:
- HTTP (80) → 0.0.0.0/0
- SSH (22) → My IP only

Outbound:
- Allow all

Purpose:
Expose web application intentionally while protecting administrative access.

---

## 3️⃣ Cowrie-SG

Inbound:
- SSH (22) → 0.0.0.0/0

Outbound:
- Allow all

Purpose:
Allow brute-force attempts for honeypot logging.

---

## 4️⃣ SIEM-SG

Inbound:
- SSH (22) → 10.0.0.0/16

Outbound:
- Allow all

Purpose:
Restrict SIEM access to internal VPC traffic only.

---

## 📌 Ref 2: Security Group Rules

![Security Groups](https://i.imgur.com/KEUTSqK.png)
![Security Groups](https://i.imgur.com/e9dXwB3.png)
![Security Groups](https://i.imgur.com/28wSCVx.png)
![Security Groups](https://i.imgur.com/5Tl7jZh.png)


---

# 🚀 Step 2 — EC2 Deployment

---

## Web-Server

- Subnet: Public-Subnet
- Public IP: Enabled
- Security Group: WebApp-SG
- Instance Type: t3.micro

This server acts as both web host and jump host.

---

## Cowrie-Server

- Subnet: Public-Subnet
- Public IP: Enabled
- Security Group: Cowrie-SG
- Instance Type: t3.micro

Will later host SSH honeypot service.

---

## Kali-Attacker

- Subnet: Public-Subnet
- Public IP: Enabled
- Security Group: Kali-SG
- Instance Type: t3.micro

Used for controlled attack simulation.

---

## SIEM-Server

- Subnet: Private-Subnet
- Public IP: Disabled
- Security Group: SIEM-SG
- Instance Type: t3.small
- Storage: 20GB

This instance will host Wazuh Manager and centralized logging.

---

## 📌 Ref 3: EC2 Instances

![EC2 Instance Overview](https://i.imgur.com/UdAEIRW.png)

---

# 🛠 Issues Encountered & Resolutions

---

## Issue 1 — Web Server Deployed to Wrong Subnet

**Problem:**
The Web-Server was mistakenly launched in the Private-Subnet.

**Why It Matters:**
Public-facing workloads must reside in the Public-Subnet to receive a public IP and route through the Internet Gateway.

**Resolution:**
The instance was terminated and relaunched in the correct Public-Subnet.

**Lesson Learned:**
Subnet selection during launch is critical and cannot be modified after instance creation.

---

## Issue 2 — Kali Storage Snapshot Error

**Error Message:**
“Volume size is smaller than the snapshot…”

**Cause:**
Attempted to reduce root volume below the minimum snapshot size defined by the Kali AMI.

**Resolution:**
Used default volume size instead of reducing it.

**Lesson Learned:**
EBS volumes must be equal to or larger than the snapshot backing the AMI.

---

## Issue 3 — SSH Jump Host Access Failed

**Symptom:**
`Permission denied (publickey)` when SSHing from Web-Server to SIEM-Server.

**Root Cause:**
SSH agent had no identities loaded, so key forwarding failed.

**Resolution Steps:**

On local machine:
eval "$(ssh-agent -s)"
ssh-add SOC-Lab-Key.pem

Then connect using:
ssh -A -i SOC-Lab-Key.pem ubuntu@Web-Public-IP

From Web-Server:
ssh ubuntu@<SIEM-Private-IP>


Connection successful.

**Lesson Learned:**
SSH agent forwarding requires the local SSH agent to have the private key loaded.

---

# 🔐 Access Validation Results

| Test | Result |
|------|--------|
| SSH to Web-Server | Success |
| Direct SSH to SIEM | Failed (Expected) |
| Jump Host SSH to SIEM | Success |

This confirms:

- Private subnet isolation is functioning
- Security Group restrictions are correct
- Routing configuration is valid
- Bastion host model works properly

---

# 🔒 Security Architecture Confirmation

The environment now enforces:

- Controlled public exposure
- Private monitoring isolation
- Secure internal access model
- No direct internet access to SIEM
- Segmented workload design

---

# ➡️ Next Phase (Day 3)

- Install Apache + PHP
- Deploy DVWA or PrestaShop
- Install Cowrie honeypot
- Deploy Wazuh Manager
- Begin centralized log ingestion
- Simulate first attack

---

If this documentation helps replicate the environment, consider starring the repository.




