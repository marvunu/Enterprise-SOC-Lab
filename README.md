# Enterprise SOC Lab – AWS Security Simulation

## 📌 Project Overview

A production-style cloud security lab implementing segmented VPC architecture, centralized log aggregation, and real-world attack simulation to develop SOC and cloud security engineering capabilities.

This repository documents the phased build, detection workflows, architectural decisions, and lessons learned throughout the project.
The objective of this project is to simulate a real-world security environment where I:

- Deploy vulnerable systems
- Simulate attacks
- Implement defensive controls
- Monitor and analyse logs
- Produce incident response documentation

This lab follows a structured, day-by-day build approach to reflect real enterprise deployment and security validation processes.

---

## 🏗️ Lab Architecture

The lab includes:

- AWS VPC with segmented networking
- Ubuntu & Kali EC2 instances
- Vulnerable web application (DVWA / PrestaShop)
- Cowrie Honeypot
- ELK Stack / Security Monitoring
- ModSecurity (WAF)
- Attack simulation using Nmap & Metasploit

---

## 🗂️ Project Timeline

Each phase of the lab is documented separately:

- [Day 1 – Architecture & VPC Setup](./Day-1-Architecture-Setup/)
- [Day 2 – Server Deployment & Configuration](./Day-2-Server-Deployment/)
- [Day 3 – Attack Simulation](./Day-3-Attack-Simulation/)
- [Day 4 – Defense Implementation (WAF / Firewall)](./Day-4-Defense-Implementation/)
- [Day 5 – Log Monitoring & Threat Detection](./Day-5-Monitoring/)
- [Day 6 – Incident Response & Documentation](./Day-6-Incident-Response/)

---

## 🛠️ Tools & Technologies Used

| Category | Tools |
|----------|-------|
| Cloud | AWS (Free Tier) |
| OS | Ubuntu Server, Kali Linux |
| Web Apps | DVWA / PrestaShop |
| Offensive Tools | Nmap, Metasploit |
| Defensive Tools | ModSecurity, UFW |
| Monitoring | ELK Stack / Security Onion |
| Honeypot | Cowrie |

---

## 🎯 Project Goals

- Build practical cloud security experience
- Understand attack methodology
- Implement layered defense strategies
- Practice log analysis & detection engineering
- Develop incident reporting skills

---

## 📊 Skills Demonstrated

- Cloud Infrastructure Deployment
- Network Segmentation
- Web Application Security Testing
- WAF Configuration
- Log Collection & Analysis
- Threat Detection
- Incident Documentation
- Troubleshooting & Root Cause Analysis

---

## 📸 Documentation Approach

Each day includes:

- Objective
- Step-by-step implementation
- Architecture explanations
- Screenshots
- Challenges encountered
- Lessons learned

This structured documentation mirrors real-world enterprise security projects.

---

## 🚀 Why This Project Matters

Security is not just about tools — it is about understanding systems, identifying weaknesses, and implementing practical defenses.

This lab represents applied cybersecurity learning beyond theory.

---

## 📬 Connect With Me

If you're interested in cloud security, SOC engineering, or collaborative security projects, feel free to connect.

LinkedIn: *(add)*  
Medium: *(add)*

---

**Author:** Marvin Unumadu  
**Focus:** Cloud Security | SOC Engineering | Defensive Security
