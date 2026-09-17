# Dual-Cloud Learning Path: AWS & Azure Architecture (Days 44 - 46)

Welcome to my **Dual-Cloud Learning Repository**! This repository documents my structured, project-driven journey mastering cloud infrastructure concepts across **Amazon Web Services (AWS)** and **Microsoft Azure**, complete with real-world software industry analogies, conceptual blueprints, UI navigation paths, and interview prep guides.

---

## 🚀 What's Covered in These Notes (Days 44 - 46)

### **1. Day 44: AWS Networking & VPC Architecture**
* **Virtual Private Cloud (VPC):** Isolated cloud networking, CIDR blocks, and corporate office analogies.
* **Subnets:** Public vs. Private subnet segregation and design patterns.
* **Routing & Gateways:** Internet Gateways (IGW), NAT Gateways, and Route Tables.
* **Network Interfaces:** Elastic Network Interfaces (ENI) and virtual network card management.

### **2. Day 45: Traffic Control & EC2 Compute Foundations**
* **Security Groups vs. NACLs:** Stateful instance-level firewalls vs. stateless subnet-level packet filtering.
* **Ephemeral Ports:** Managing the `1024-65535` port range in stateless NACL inbound rules.
* **AWS EC2:** AMIs, instance types, key pairs, EBS root volumes, user data automation scripts, and lifecycle states (Pending, Running, Stopped, Terminated).

### **3. Day 46: Advanced Compute & Identity Management**
* **Auto Scaling Groups (ASG):** Launch templates, capacity management (Minimum, Desired, Maximum), health checks, and automated instance replacement.
* **IAM (Identity & Access Management):** Users, groups, JSON-based policies, IAM roles for secure cross-service access, trust relationships, and the **Least Privilege Principle**.

---

## 📊 Comprehensive AWS vs. Azure Glossary

| Concept / Acronym | AWS Full Form | Azure Equivalent / Full Form | Purpose |
| :--- | :--- | :--- | :--- |
| **VPC / VNet** | Virtual Private Cloud | Virtual Network (VNet) | Isolated virtual network space |
| **IGW** | Internet Gateway | Internet Gateway / VPN Gateway | Public internet connectivity |
| **NAT** | Network Address Translation | NAT Gateway | Outbound private internet access |
| **ENI** | Elastic Network Interface | Network Interface Card (NIC) | Virtual network card for servers |
| **NACL** | Network Access Control List | *No direct equivalent* | Stateless subnet firewall layer |
| **SG** | Security Group | Network Security Group (NSG) | Stateful instance firewall |
| **EC2** | Elastic Compute Cloud | Virtual Machines (VMs) | Scalable server compute capacity |
| **AMI** | Amazon Machine Image | VM Image / Template | Operating system and app template |
| **EBS** | Elastic Block Store | Managed Disks | Persistent block storage volumes |
| **ASG** | Auto Scaling Group | Virtual Machine Scale Sets (VMSS)| Automatic cluster scaling |
| **IAM** | Identity and Access Management | RBAC & Managed Identities | Secure resource access control |

---

## 🛠️ Practical Labs & UI Navigation Guides
Included in the master notes are step-by-step console navigation paths for **6 hands-on labs**:
1. **VPC and Subnet Provisioning** (`VPC` Dashboard)
2. **Internet & NAT Gateway Setup** (`VPC` Dashboard)
3. **Public & Private EC2 Instance Launch** (`EC2` Dashboard)
4. **NACL Ephemeral Port Rule Configuration** (`VPC` Dashboard)
5. **Auto Scaling Group Implementation & Testing** (`EC2` Dashboard)
6. **IAM Role Assignment for EC2** (`IAM` Console)

---

## 💡 Top Interview Questions Covered
This repository includes **25 detailed technical interview questions and answers** covering:
* VPC design and subnets traffic routing.
* Stateful (Security Groups) vs. Stateless (NACLs) firewalls.
* Ephemeral port mechanics.
* Auto Scaling capacity thresholds and self-healing health checks.
* IAM least privilege implementation and avoiding hardcoded credentials.

---

## 📁 Repository Structure
```text
├── README.md               # Overview of the learning path
└── master_notes.md         # Comprehensive Day 44-46 study guide & interview Q&A
