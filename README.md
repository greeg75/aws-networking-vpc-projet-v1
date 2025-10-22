# AWS Networking VPC Project v1  
### Secure AWS VPC architecture with a public bastion host, private backend server, and NAT Gateway for controlled Internet access

---

##  Project Objective

Design and deploy a secure and isolated AWS network architecture following best cloud security practices:

- Create a **custom VPC** with public and private subnets  
- Deploy a **Bastion Host** in the public subnet (SSH access point)  
- Deploy a **Backend Server** in the private subnet (no direct Internet access)  
- Configure a **NAT Gateway** for controlled outbound Internet access from the private subnet  
- Configure an **Internet Gateway** for the public subnet  
- Apply **Security Groups** to restrict inbound and outbound traffic  

---

## Architecture Overview

**AWS Services Used**

- **Amazon VPC** — Custom VPC with CIDR `10.0.0.0/16`  
- **Subnets:**  
  - `public-subnet-1` → `10.0.1.0/24`  
  - `private-subnet-1` → `10.0.2.0/24`  
- **Internet Gateway (IGW)** — Enables Internet access for the public subnet  
- **NAT Gateway (NAT)** — Allows private instances to reach the Internet (outbound only)  
- **Route Tables:**  
  - `rtb-public` → `0.0.0.0/0` → Internet Gateway  
  - `rtb-private` → `0.0.0.0/0` → NAT Gateway  
- **Security Groups:**  
  - `SG-Bastion` → Allows SSH (port 22) only from my public IP  
  - `SG-Backend` → Allows SSH (port 22) only from the Bastion SG  
- **EC2 Instances:**  
  - `Bastion Host` (Amazon Linux 2023, public subnet)  
  - `Backend Server` (Amazon Linux 2023, private subnet)

---

## Security Implementation

| Component | Type | Description |
|------------|------|-------------|
| **SSH Access Control** | Security Group | Only my local IP can connect to the Bastion |
| **Private Access Restriction** | Security Group | Only Bastion can access the Backend |
| **Outbound Internet (Private Subnet)** | NAT Gateway | Backend can reach the Internet outbound |
| **Inbound Internet (Public Subnet)** | Internet Gateway | Allows Internet access only for Bastion |
| **Network Isolation** | Routing | Private subnet has no direct route to IGW |

---

## Validation Tests

| Test | Command | Expected Result | Actual Result |
|------|----------|------------------|----------------|
| Bastion → Internet | `ping google.com` | ✅ Success | ✅ OK |
| Backend → Internet (via NAT) | `curl https://aws.amazon.com` | ✅ Success | ✅ OK |
| Bastion ↔ Backend | `ssh` / `ping` | ✅ Internal communication | ✅ OK |
| Internet → Backend | SSH attempt | ❌ Blocked | ✅ Timeout |
| Backend Public IP | Console check | ❌ No public IP | ✅ Confirmed |

✅ **All connectivity and isolation tests successfully passed.**

---

## Network Diagram

<img width="1052" height="617" alt="image" src="https://github.com/user-attachments/assets/f71de0d6-8f4c-48bd-945a-e9538b54306c" />


---

## Project Results & Learnings

**Outcome:**  
Successfully designed and deployed a secure AWS network architecture with proper segmentation, routing, and access control.

**Key Learnings:**
- Building and configuring custom VPC networks  
- Managing routing tables (IGW, NAT)  
- Securing access with key pairs and Bastion Hosts  
- Using Security Groups for isolation  
- Testing and validating network connectivity and security

---

##  Future Improvements

- Add **Network ACLs (NACLs)** for an additional subnet-level security layer  
- Enable **AWS CloudTrail** and **GuardDuty** for security monitoring and anomaly detection  
- Implement **AWS Systems Manager Session Manager** for keyless SSH access  
- Deploy a **private RDS database** accessible only from the backend  
- Configure **IAM roles and policies** for least privilege access management  

---

## Author of project

**JACQUET Grégory**  
AWS Cloud Practitioner (in progress)  
Passionate about Cloud Security  

---

## 💬 Repository Info

- **Project:** AWS Networking VPC Project v1  
- **Language:** French, English 
- **Goal:** Demonstrate foundational AWS networking and security architecture skills

---

## 🇫🇷 French Summary

**AWS Networking VPC Project v1 :** Création d’un réseau AWS sécurisé avec un Bastion et un serveur Backend privé.  
Ce projet démontre la conception d’une architecture AWS respectant les bonnes pratiques de sécurité :  
un **subnet public** pour le bastion (accès SSH) et un **subnet privé** isolé, accessible uniquement via le bastion.  
Le backend peut accéder à Internet via la **NAT Gateway**, sans être exposé.  
Toutes les connexions directes depuis Internet sont **bloquées**, assurant une isolation complète du réseau.

---
