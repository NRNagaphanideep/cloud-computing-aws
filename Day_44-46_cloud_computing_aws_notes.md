Dual-Cloud Master Notes: AWS & Azure Architecture (Days 44 - 46)

---

## Section 1: Comprehensive Full Forms Glossary (AWS vs. Azure)

| Concept / Acronym | AWS Full Form | Azure Equivalent / Full Form | Description / Purpose |
| :--- | :--- | :--- | :--- |
| **AWS / Azure** | Amazon Web Services / Microsoft Azure | Microsoft Azure | Cloud Computing Service Providers |
| **VPC / VNet** | Virtual Private Cloud | Virtual Network (VNet) | Isolated virtual network space in the cloud |
| **IGW** | Internet Gateway | Internet Gateway / VPN Gateway | Component allowing communication between VPC and the internet |
| **NAT** | Network Address Translation | NAT Gateway | Allows private instances to access the internet without exposing them |
| **ENI** | Elastic Network Interface | Network Interface Card (NIC) | Virtual network card attached to a server instance |
| **NACL** | Network Access Control List | *No direct equivalent* (Subnet level firewall) | Stateless subnet-level firewall layer |
| **SG** | Security Group | Network Security Group (NSG) | Stateful instance/NIC-level firewall |
| **EC2** | Elastic Compute Cloud | Virtual Machines (VMs) | Scalable compute capacity / servers |
| **AMI** | Amazon Machine Image | VM Image / Template | Pre-configured OS and software template for servers |
| **EBS** | Elastic Block Store | Managed Disks | Block storage volumes attached to EC2 instances |
| **ASG** | Auto Scaling Group | Virtual Machine Scale Sets (VMSS) | Automatically scales compute capacity up or down |
| **IAM** | Identity and Access Management | Role-Based Access Control (RBAC) & Managed Identities | Manages secure access to services and resources |

---

## Section 2: Day 44 - AWS Networking & VPC Architecture

### Core Concepts & Definitions
* **VPC (Virtual Private Cloud):** An isolated, private virtual network dedicated to your AWS account. Think of it as a **"Gated Community"** or a **"Corporate Office Building"** where you control all entrances, exits, and internal room divisions.
* **Subnets (Public & Private):** Subdivisions of a VPC network range. 
  * *Public Subnet:* Connected directly to the Internet Gateway. Used for user-facing applications (Web servers).
  * *Private Subnet:* Isolated from direct internet access. Used for secure databases and backend components.
* **Route Tables:** A set of rules (routes) that determines where network traffic is directed from subnets or gateways.
* **Internet Gateway (IGW):** The main entry/exit gate of the VPC that connects it to the public internet.
* **NAT Gateway:** A managed service that allows instances in a private subnet to initiate outbound connections to the internet (e.g., for software patches) while blocking inbound connections from the outside.

### Architectural Analogy
* **VPC:** The corporate campus fence.
* **Subnets:** Individual departments inside the building (Frontend team floor vs. Secure vault server room).
* **Route Tables:** The signboards and traffic police directing which corridor leads to the exit.

---

## Section 3: Day 45 - Traffic Control & EC2 Compute Foundations

### 1. Security Groups (SG) vs. NACLs
* **Security Groups (Stateful):** Operates at the **Instance/NIC level**. It remembers incoming requests, automatically allowing returning traffic without requiring explicit outbound rules. Analogy: **A personal security guard standing directly at an office room door.**
* **NACLs (Stateless):** Operates at the **Subnet level**. It evaluates rules sequentially based on rule numbers and is *stateless*—meaning separate inbound and outbound rules (including explicit allowances for **Ephemeral Ports 1024-65535**) must be maintained. Analogy: **The main building security gate checking every single person entering or leaving the floor.**

### 2. EC2 (Elastic Compute Cloud) Core Components
* **AMI (Amazon Machine Image):** The master template containing the Operating System and initial software configuration.
* **Instance Types:** Pre-configured mixes of CPU, memory, and networking capacity (e.g., `t2.micro`).
* **Key Pairs:** Cryptographic keys (.pem) used for secure remote login.
* **EBS (Elastic Block Store):** Persistent block storage volumes providing virtual hard disks.
* **User Data:** Automation script executed once during the first boot to install packages (e.g., Apache, Docker).
* **Lifecycle States:** Pending ──► Running ──► Stopping ──► Stopped ──► Shutting-down ──► Terminated.

---

## Section 4: Day 46 - Advanced Compute (Auto Scaling) & Identity (IAM)

### 1. Auto Scaling & Capacity Management
* **Launch Template:** The blueprint template defining AMI, instance type, and security settings for new servers.
* **Auto Scaling Group (ASG):** Manages a cluster of EC2 instances automatically.
  * *Minimum Capacity:* The floor limit of active servers.
  * *Desired Capacity:* The target operational number of servers.
  * *Maximum Capacity:* The ceiling limit during traffic spikes.
* **Health Checks & Instance Replacement:** ASG continuously monitors instance health; unhealthy instances are automatically terminated and replaced with fresh instances.

### 2. IAM (Identity and Access Management)
* **Users & Groups:** Human identities and collections for console access.
* **Policies:** JSON documents defining explicit permissions (what actions are allowed on which resources).
* **IAM Roles:** Temporary permission profiles assigned to AWS services (like EC2) so they can securely access other services without hardcoded credentials.
* **Least Privilege Principle:** Granting only the bare minimum permissions necessary to perform a task—nothing more.
* **Trust Relationship:** A policy defining which services or accounts are permitted to assume a specific role.

---

## Section 5: The 6 Practical Labs with UI Navigation Paths

### Lab 1: Creating a VPC and Subnets
* **UI Path:** AWS Console ──► Search **`VPC`** ──► Left menu **Your VPCs** ──► **Create VPC**.
  * Settings: Select *VPC only*, Name: `vpc-prod`, CIDR: `10.0.0.0/16`.
* **Subnets Path:** Left menu **Subnets** ──► **Create subnet**.
  * Public Subnet: `subnet-public`, CIDR `10.0.1.0/24`.
  * Private Subnet: `subnet-private`, CIDR `10.0.2.0/24`.

### Lab 2: Setting up Internet Gateways and NAT Gateways
* **IGW Path:** VPC Dashboard ──► **Internet Gateways** ──► **Create internet gateway** (`igw-prod`) ──► *Actions* ──► *Attach to VPC*.
* **NAT Path:** VPC Dashboard ──► **NAT gateways** ──► **Create NAT gateway** (`nat-gw-prod`) ──► Select `subnet-public` ──► *Allocate Elastic IP* ──► *Create*.

### Lab 3: Launching Public and Private EC2 Instances
* **UI Path:** AWS Console ──► Search **`EC2`** ──► **Launch instance**.
  * Name: `web-server-public`, Select AMI & `t2.micro`.
  * Network: Choose `vpc-prod`, select `subnet-public`, **Auto-assign public IP: Enable**.
  * Launch second instance (`db-server-private`) in `subnet-private` with **Auto-assign public IP: Disable**.

### Lab 4: Configuring NACLs with Ephemeral Port Rules & Security Groups
* **NACL Path:** VPC Dashboard ──► **Network ACLs** ──► Select `nacl-prod` ──► **Inbound rules** ──► *Edit inbound rules*.
  * Add custom rule: Type *Custom TCP*, Port Range `1024-65535`, Source `0.0.0.0/0`, Action **ALLOW**.
* **SG Path:** EC2 Dashboard ──► **Security Groups** ──► Modify inbound/outbound rules directly tied to instance NICs.

### Lab 5: Creating an Auto Scaling Group (ASG) and Testing Instance Replacement
* **Launch Template Path:** EC2 Dashboard ──► **Launch Templates** ──► **Create launch template** (`asg-web-template`).
* **ASG Path:** EC2 Dashboard ──► **Auto Scaling Groups** ──► **Create Auto Scaling group** (`prod-asg-group`).
  * Set Capacity: Min `2`, Desired `2`, Max `5`.
* **Test:** Terminate one running instance manually from the console and observe ASG automatically launch a replacement instance via health checks.

### Lab 6: Creating an IAM Role for EC2 (Avoiding Hardcoded Keys)
* **UI Path:** AWS Console ──► Search **`IAM`** ──► Left menu **Roles** ──► **Create role**.
  * Trusted entity: *AWS service* ──► *EC2*.
  * Permissions: Select `AmazonS3ReadOnlyAccess`. Name: `EC2-S3-Reader-Role`.
* **Attachment Path:** EC2 Dashboard ──► Select instance ──► *Actions* ──► *Security* ──► *Modify IAM role* ──► Attach `EC2-S3-Reader-Role`.

---

## Section 6: Top 25 Interview Questions & Detailed Answers

### Q1: What is a VPC and why is it used?
**Answer:** A Virtual Private Cloud (VPC) is an isolated virtual network dedicated to your AWS account. It lets you launch AWS resources in a defined virtual network that you control, offering advanced security and custom network topologies.

### Q2: What is the difference between a Public Subnet and a Private Subnet?
**Answer:** A Public Subnet has a route table entry pointing to an Internet Gateway (`0.0.0.0/0`), allowing resources inside to communicate directly with the public internet. A Private Subnet has no direct internet route; instances inside communicate outbound via a NAT Gateway.

### Q3: How do Security Groups differ from NACLs?
**Answer:** Security Groups operate at the instance/NIC level and are **stateful** (remember traffic state). NACLs operate at the subnet level, are **stateless** (require explicit inbound and outbound rules), and evaluate rules sequentially by rule number.

### Q4: What are Ephemeral Ports and why do they matter in NACLs?
**Answer:** Ephemeral ports (typically range 1024–65535) are temporary port numbers assigned by an operating system to client connections. Because NACLs are stateless, an explicit inbound rule allowing ports 1024–65535 is required to let response traffic back into a private subnet.

### Q5: Explain Stateful vs. Stateless firewalls.
**Answer:** A stateful firewall tracks connection states and automatically permits return traffic for approved outbound sessions. A stateless firewall evaluates every packet individually without memory of prior state, requiring explicit rules for both directions.

### Q6: What is an Internet Gateway (IGW)?
**Answer:** A horizontally scaled, redundant, and highly available VPC component that enables communication between instances in your VPC and the internet.

### Q7: What is the function of a NAT Gateway?
**Answer:** A Network Address Translation (NAT) service enables instances in a private subnet to connect to the internet or other AWS services while preventing the internet from initiating connections back to those private instances.

### Q8: What is an Elastic Network Interface (ENI)?
**Answer:** A virtual network card that attaches to an EC2 instance, containing MAC addresses, private IPs, public IPs, and security groups.

### Q9: What is an AMI?
**Answer:** An Amazon Machine Image is a master template containing the operating system, root volume configuration, and pre-installed software required to launch an EC2 instance.

### Q10: What are EC2 Instance Types?
**Answer:** Groupings of CPU, memory, storage, and networking capacity classes (e.g., general-purpose `t2.micro`, compute-optimized) tailored for different workload requirements.

### Q11: What is EBS?
**Answer:** Elastic Block Store provides durable, block-level persistent storage volumes that attach to running EC2 instances, functioning like virtual hard disks.

### Q12: What is EC2 User Data?
**Answer:** A shell script or cloud-init directive passed into an EC2 instance during launch to automate initial provisioning tasks, package installations, and application deployment.

### Q13: What are the lifecycle states of an EC2 instance?
**Answer:** Pending, Running, Stopping, Stopped, Shutting-down, and Terminated.

### Q14: What is an Auto Scaling Group (ASG)?
**Answer:** An AWS compute management feature that automatically adjusts the number of running EC2 instances up or down according to defined policies, health checks, and capacity constraints.

### Q15: Explain Minimum, Desired, and Maximum capacity in ASG.
**Answer:** 
* *Minimum:* The lowest number of active instances allowed.
* *Desired:* The target operating count maintained under normal conditions.
* *Maximum:* The absolute ceiling limit on instances during traffic spikes.

### Q16: How does ASG handle unhealthy instances?
**Answer:** Through automated health checks, ASG flags unresponsive instances, terminates them, and automatically provisions fresh replacement instances matching the launch template.

### Q17: What is a Launch Template?
**Answer:** A configuration blueprint containing instance types, AMIs, security groups, and storage settings utilized by an Auto Scaling Group to spin up new instances.

### Q18: What is AWS IAM?
**Answer:** Identity and Access Management is a web service enabling secure management of access to AWS services and resources through users, groups, policies, and roles.

### Q19: What is an IAM Policy?
**Answer:** A document written in JSON format that explicitly defines permissions (Allow or Deny) for specific AWS actions and resources.

### Q20: What is an IAM Role and how does it differ from an IAM User?
**Answer:** An IAM User represents a specific human identity with permanent long-term credentials. An IAM Role is an identity with permission policies that can be temporarily assumed by authorized services (like EC2) or users.

### Q21: What is the Principle of Least Privilege?
**Answer:** A core security best practice dictating that users, applications, and services should be granted only the minimum permissions necessary to perform their required functions—nothing more.

### Q22: What is a Trust Relationship in IAM?
**Answer:** A component of an IAM role specifying which trusted entities (such as an EC2 service) are allowed to assume that role.

### Q23: Why should you avoid storing access keys directly on an EC2 server?
**Answer:** Storing long-term static access keys on a server introduces severe security risks; if the server is compromised, credentials are leaked. Using IAM roles avoids this by issuing automatic temporary security tokens.

### Q24: What is the Azure equivalent of an AWS VPC?
**Answer:** A Virtual Network (VNet).

### Q25: What is the Azure equivalent of AWS Auto Scaling Groups (ASG)?
**Answer:** Virtual Machine Scale Sets (VMSS).


