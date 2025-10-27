# AWS Networking Security – Phase 1
### Secure and Scalable Network Foundation on AWS

---

## 📘 Project Overview

This project marks **Phase 1** of the AWS Networking Security.  
It focuses on building a **secure, isolated, and scalable Virtual Private Cloud (VPC)** environment to serve as the foundation for later **security and monitoring layers**.

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

## VPC Network Diagram - Phase 1

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

## Repository Info

- **Project:** AWS Networking Security – Phase 1 
- **Language:** French, English 
- **Goal:** Demonstrate foundational AWS networking and security architecture skills

---

## French Summary

**AWS Networking Security – Phase 1 :** Création d’un réseau AWS sécurisé avec un Bastion et un serveur Backend privé.  
Ce projet démontre la conception d’une architecture AWS respectant les bonnes pratiques de sécurité :  
un **subnet public** pour le bastion (accès SSH) et un **subnet privé** isolé, accessible uniquement via le bastion.  
Le backend peut accéder à Internet via la **NAT Gateway**, sans être exposé.  
Toutes les connexions directes depuis Internet sont **bloquées**, assurant une isolation complète du réseau.

---

# AWS AWS Networking Security - Phase 2
### Building a Cloud Security and Monitoring Layer on AWS

---

## 📘 Project Overview

This project marks **Phase 2** of the AWS Networking Security.  
It extends the secure VPC created in **Phase 1** by implementing **monitoring, detection, and auditing services** to transform the network into a secure, observable, and compliant AWS environment.

The objective of this phase is to **detect security threats, audit configurations, and receive alerts** in real time using native AWS services.

---

## Project Objective

- Enable **real-time visibility** across the network and account activity  
- Detect **suspicious API actions and network anomalies**  
- Configure **automated alerts** for high-severity findings  
- Audit **IAM and S3 configurations** for external exposure  
- Simulate and resolve findings to validate the detection workflow

---

## Architecture Overview

**AWS Services Used**

- **AWS CloudTrail** — Logs all API activity across AWS services  
- **VPC Flow Logs** — Captures accepted/rejected network traffic within the VPC  
- **Amazon GuardDuty** — Detects potential security threats and anomalies  
- **Amazon EventBridge + SNS** — Sends real-time alerts for high-severity incidents  
- **IAM Access Analyzer** — Audits and identifies public or cross-account access risks

## Security Monitoring Implementation

| Service | Purpose | Key Configuration |
|----------|----------|------------------|
| **CloudTrail** | Account activity tracking | Enabled for all management & data events, logs stored in S3 |
| **VPC Flow Logs** | Network visibility | Captures inbound/outbound traffic within the VPC |
| **GuardDuty** | Threat detection | Monitors for malicious or suspicious activity |
| **EventBridge + SNS** | Alerting pipeline | Triggers email alerts when GuardDuty detects threats |
| **IAM Access Analyzer** | Access auditing | Identifies external access to S3 or IAM resources |

## Validation Tests

### CloudTrail
- Verified API call logs (e.g., Security Group creation/deletion)
- Logs stored in encrypted S3 bucket  
✅ **Result:** Full traceability of user and service activity

---

###  VPC Flow Logs
- Captured network traffic between Bastion (10.0.1.200) and Backend (10.0.2.16)
<img width="1419" height="62" alt="image" src="https://github.com/user-attachments/assets/450b0dd4-0d8f-4bf2-9974-cc8f421b7bc5" />
✅ **Result:** Internal and outbound traffic successfully logged

###  GuardDuty
- Activated and tested with sample findings
  
<img width="1317" height="119" alt="image" src="https://github.com/user-attachments/assets/b769f21d-6c15-4fe7-b514-98d99734c973" />

- Verified **SNS email alert** received in real time

<img width="1010" height="124" alt="image" src="https://github.com/user-attachments/assets/939b1d15-4122-4234-ad3a-142c4dcecb0d" />
✅ **Result:** GuardDuty successfully detected and alerted simulated threats

### IAM Access Analyzer
- Created an **Access Analyzer** in `eu-north-1` with **Zone of trust: Current account**  
- Tested detection by applying a **temporary public S3 policy** (Principal `"*"`), then removed it  
- Result: **Finding detected** → then **Withdraw** (Line deleted) after fixed

**Access Analyzer**

<img width="774" height="106" alt="image" src="https://github.com/user-attachments/assets/a0dcb001-5dd5-487b-a071-747bc9421d59" />

**Finding detected :** 

<img width="894" height="273" alt="Capture d'écran 2025-10-25 045804" src="https://github.com/user-attachments/assets/81da26d2-a729-43d1-94a5-be686573f5de" />

**After fixed :** 

<img width="770" height="117" alt="image" src="https://github.com/user-attachments/assets/b6fd8408-b7ef-4a2f-844d-663a28494d77" />



✅ **Result:** Access Analyzer correctly identified public exposure and cleared it once fixed.

---

## Findings Summary

| Service             | Finding Type                              | Description                                           | Status      |
|---------------------|-------------------------------------------|-------------------------------------------------------|-------------|
| GuardDuty           | `AttackSequence:EKS/CompromisedCluster`    | Detected a potential compromise of an EKS cluster through unauthorized access activity| ✅ Detected |
| IAM Access Analyzer | Public S3 Bucket                          | Bucket exposed via `Principal: "*"` (bucket policy)   | ✅ Resolved |
| VPC Flow Logs       | Internal Traffic                          | Bastion (10.0.1.200) ↔ Backend (10.0.2.16) SSH        | ✅ Logged   |
| CloudTrail          | API Event                                 | Security Group create/delete recorded                 | ✅ Recorded |


---

## VPC Network Diagram - Phase 2

<img width="1470" height="583" alt="image" src="https://github.com/user-attachments/assets/9db515e5-6aca-4689-9bdc-a88b5c3501d2" />

The base VPC architecture remains unchanged from Phase 1, ensuring network segmentation and controlled access through the Bastion Host and NAT Gateway.

## Project Results & Learnings

**Outcome:**
Successfully integrated AWS-native monitoring and auditing tools into the existing secure VPC.  
The environment now provides **real-time detection, alerting, and visibility** for network and account activity.

**Key Learnings:**
- How to enable and interpret AWS CloudTrail and VPC Flow Logs  
- Using GuardDuty for threat detection and understanding finding types  
- Building an automated alert pipeline (EventBridge + SNS)  
- Detecting and fixing public access via IAM Access Analyzer  
- Understanding how monitoring enhances network security posture

## Future Improvements

- Integrate **AWS Security Hub** to centralize all security findings  
- Add **AWS Config** for continuous compliance evaluation  
- Enable **AWS Systems Manager** for keyless access and patch management  
- Extend **GuardDuty Runtime Monitoring** to EC2 instances  
- Correlate security events with **CloudWatch dashboards**

---

## Author of Project

**JACQUET Grégory**  
AWS Cloud Practitioner (in progress)  
Aspiring Cloud Security Engineer  
Passionate about Cloud Security

---

## Repository Info

- **Project:** AWS Networking Security – Phase 2  
- **Language:** French, English  
- **Goal:** Demonstrate AWS security monitoring, auditing, and alerting implementation

## French Summary

Mise en place d’une couche de surveillance et de détection sur l’infrastructure AWS. Les services CloudTrail, VPC Flow Logs et GuardDuty permettent une visibilité complète du réseau et des activités API.
Les alertes via CloudWatch/SNS assurent une réaction rapide aux menaces. Enfin, IAM Access Analyzer a permis de détecter et corriger une exposition publique d’un bucket S3.
