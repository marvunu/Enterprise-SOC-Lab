
## 📅 Day 1 — Network Architecture Foundation

Day 1 focuses entirely on **cloud network design and segmentation**.  
No security tools are deployed at this stage.

The goal is to build a solid and secure foundation before introducing vulnerable services, monitoring tools, or attack simulations.

---

## 🎯 Objective

The objective of Day 1 was to design and implement a segmented AWS network architecture that mirrors real-world enterprise environments.

Instead of relying on AWS’s default VPC, a custom Virtual Private Cloud (VPC) was created to:

- Separate internet-facing resources from internal monitoring infrastructure
- Enforce controlled routing through explicit route tables
- Reduce exposure and blast radius
- Prepare the environment for SOC tooling deployment in later phases

This phase establishes the architectural baseline for the entire lab.

---

## 🧠 Skills Developed

- Custom VPC design and CIDR planning
- Public vs Private subnet segmentation
- Route table creation and subnet association
- Internet Gateway configuration
- Understanding how routing decisions affect exposure
- Navigating AWS UI changes and configuration flow
- Applying cloud security architecture principles

---

## 🛠 Tools & Services Used

- Amazon Web Services (AWS)
  - VPC
  - Subnets
  - Route Tables
  - Internet Gateway
- AWS Management Console

---

## 🗺 Architecture Overview

### Network Addressing

| Component        | CIDR Block     |
|------------------|----------------|
| VPC              | 10.0.0.0/16    |
| Public Subnet    | 10.0.1.0/24    |
| Private Subnet   | 10.0.2.0/24    |

---

### 📌 Ref 1: Day 1 Network Architecture Diagram

![Day 1 Network Architecture](https://i.imgur.com/dTzW7Dh.png)

*Figure 1: Segmented VPC design showing public and private subnets with controlled Internet Gateway routing.*

---

## 🔧 Step-by-Step Implementation

---

### Step 1 — Create a Custom VPC

- Navigate to **VPC → Create VPC**
- Name: `SOC-Lab-VPC`
- IPv4 CIDR block: `10.0.0.0/16`
- Tenancy: Default

A `/16` CIDR block was selected to allow structured subnetting and scalability, reflecting common enterprise practices.

Using a custom VPC instead of the default VPC forces explicit architectural decisions and improves security awareness.

![Ref 2: VPC Creation](https://i.imgur.com/OIAUPdL.png)
![Ref 2: VPC Creation](https://i.imgur.com/z5ZLvMI.png)

---

### Step 2 — Create the Public Subnet

- Navigate to **VPC → Subnets → Create subnet**
- VPC: `SOC-Lab-VPC`
- Name: `Public-Subnet`
- CIDR: `10.0.1.0/24`
- Availability Zone: Any
- Enable auto-assign public IPv4 addresses

This subnet will later host intentionally exposed services such as:
- Vulnerable web applications
- Honeypot services
- Temporary attacker infrastructure

![Ref 3: Public Subnet Creation](https://i.imgur.com/QnhzghK.png)
![Ref 3: Public Subnet Creation](https://i.imgur.com/NF23d5b.png)

---

### Step 3 — Create the Private Subnet

- Navigate to **VPC → Subnets → Create subnet**
- VPC: `SOC-Lab-VPC`
- Name: `Private-Subnet`
- CIDR: `10.0.2.0/24`

After creation:

- Select `Private-Subnet`
- Go to **Actions → Edit subnet settings**
- Disable **Auto-assign public IPv4 address**

This ensures instances launched in this subnet do not receive public IP addresses.

The private subnet will later host SOC monitoring infrastructure (SIEM).

![Ref 4: Private Subnet Settings](images/ref4.png)

---

### Step 4 — Create and Attach an Internet Gateway

- Navigate to **VPC → Internet Gateways**
- Create Internet Gateway
- Name: `SOC-IGW`
- Attach to: `SOC-Lab-VPC`

An Internet Gateway enables internet connectivity, but **only for resources explicitly routed to it**.

![Ref 5: Internet Gateway Attachment](images/ref5.png)

---

### Step 5 — Create the Public Route Table

- Navigate to **VPC → Route Tables → Create route table**
- Name: `Public-Route-Table`
- VPC: `SOC-Lab-VPC`

#### Add Internet Route

- Select `Public-Route-Table`
- Routes → Edit routes
- Add:




#### Associate Public Subnet

- Subnet associations → Edit
- Select `Public-Subnet`
- Save associations

This explicitly grants internet access to the public subnet.

![Ref 6: Public Route Table Configuration](images/ref6.png)

---

### Step 6 — Create the Private Route Table

- Navigate to **VPC → Route Tables → Create route table**
- Name: `Private-Route-Table`
- VPC: `SOC-Lab-VPC`

#### Associate Private Subnet

- Subnet associations → Edit
- Select `Private-Subnet`
- Save associations

#### Verify Routes

The private route table should contain **only**:


There must be **no** route to the Internet Gateway.

![Ref 7: Private Route Table Configuration](images/ref7.png)

---

## ✅ Architecture Validation Checklist

- [x] Custom VPC created
- [x] Public and private subnets configured
- [x] Internet Gateway attached to VPC
- [x] Public subnet routed to Internet Gateway
- [x] Private subnet has no internet route
- [x] Public IP auto-assignment disabled on private subnet

All checks confirm correct segmentation.

---

## 📝 Notes & Observations

### AWS UI Behavior

During implementation, the following AWS UI behaviors were observed:

- Subnet association is not performed during route table creation
- Subnets must be explicitly associated after route table creation
- Public IPv4 auto-assignment settings may need to be modified post-creation

Understanding these behaviors helps avoid misconfiguration and reinforces architectural clarity.

---

## 🔐 Security Design Rationale

This architecture ensures:

- Internet-facing services are intentionally exposed
- Monitoring infrastructure is isolated in a private subnet
- SIEM systems are not directly reachable from the internet
- Reduced blast radius in case of compromise

Network segmentation is foundational to cloud security and SOC operations.

---

## ➡️ Next Phase (Day 2)

- Launch EC2 instances in correct subnets
- Configure security groups
- Establish controlled SSH access
- Prepare for vulnerable service deployment
- Begin SOC tooling installation

---

If this documentation helps you recreate the environment, consider starring the repository.

Further phases will cover attack simulation, detection engineering, and incident response workflows.
