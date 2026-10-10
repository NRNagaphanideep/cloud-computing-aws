AWS Fresher Interview Questions & Answers (Master Edition)

This comprehensive guide covers all AWS topics from your curriculum tailored for entry-level (fresher) DevOps and Cloud Engineer interviews, including full forms, core definitions, theoretical concepts, and practical scenarios.

---

## 1. Global Infrastructure & Networking

### Q1: What is the full form of AWS, AZ, VPC, IGW, and NAT, and what do they mean?
* **AWS:** Amazon Web Services. Cloud computing platform by Amazon.
* **AZ:** Availability Zone. One or more discrete data centers with redundant power, networking, and connectivity within an AWS Region.
* **VPC:** Virtual Private Cloud. Isolated, private virtual network dedicated to your AWS account.
* **IGW:** Internet Gateway. A horizontally scaled, redundant, and highly available VPC component that allows communication between your VPC and the internet.
* **NAT Gateway:** Network Address Translation Gateway. Enables instances in a private subnet to connect to the internet or other AWS services while preventing the internet from initiating connections with those instances.

### Q2: What is the difference between Security Groups and Network ACLs (NACLs)?
* **Security Group (SG):** Operates at the **instance (NIC) level**, is **stateful** (return traffic is automatically allowed regardless of rules), and evaluates *all* rules before deciding whether to allow traffic.
* **Network ACL (NACL):** Operates at the **subnet level**, is **stateless** (return traffic must be explicitly allowed by inbound/outbound rules), and evaluates rules in numerical order.

### Q3: Scenario: You have an EC2 instance in a private subnet that needs to download patches from the internet. How would you design this?
* **Answer:** Deploy a **NAT Gateway** in a public subnet. Configure the route table of the private subnet to direct all outbound internet traffic (`0.0.0.0/0`) to the NAT Gateway. This allows the private instance to initiate outbound requests while blocking inbound traffic from the public internet.

---

## 2. Compute & Auto Scaling

### Q4: What is EC2, and what are its core components?
* **EC2:** Elastic Compute Cloud. Provides scalable computing capacity in the cloud.
* **Core Components:**
  * **AMI (Amazon Machine Image):** Template containing the OS and software configurations.
  * **Instance Types:** Pre-configured mixes of CPU, memory, storage, and networking capacity.
  * **EBS (Elastic Block Store):** Block-level storage volumes attached to EC2.
  * **Key Pairs:** Secure login credentials for your instances.
  * **Security Groups:** Virtual firewalls controlling traffic.

### Q5: Explain the difference between an Auto Scaling Group (ASG) and a Launch Template.
* **Launch Template:** A template containing configuration parameters for EC2 instances (AMI ID, instance type, key pair, security groups, block devices).
* **Auto Scaling Group (ASG):** A collection of EC2 instances treated as a logical group for automatic scaling and management, referencing a Launch Template to spin up new instances dynamically based on desired, minimum, and maximum capacities.

---

## 3. Identity, Access Management & Secrets

### Q6: What is IAM, and what is the principle of least privilege?
* **IAM:** Identity and Access Management. Manages access to AWS services and resources securely.
* **Least Privilege:** A security best practice where users, services, or processes are given only the bare minimum permissions necessary to perform their intended functions and nothing more.

### Q7: What is an IAM Instance Profile, and why is it used instead of hardcoding credentials?
* **IAM Instance Profile:** A container that passes an IAM role to an EC2 instance at launch. 
* **Why use it:** Hardcoding AWS access keys and secret keys inside application code or config files is a major security risk. Instance profiles allow applications running on EC2 to securely inherit temporary credentials without storing keys on disk.

### Q8: What is AWS Secrets Manager, and how does it differ from environment variables?
* **AWS Secrets Manager:** A service that helps you protect database credentials, API keys, and other secrets throughout their lifecycle, supporting automated secret rotation.
* **Environment Variables:** Can be exposed via process lists or configuration dumps. Secrets Manager allows runtime retrieval, encryption at rest using AWS KMS, and automatic rotation without application redeployment.

---

## 4. Governance & Organization

### Q9: What is AWS Organizations and what is a Service Control Policy (SCP)?
* **AWS Organizations:** Account management service that enables you to consolidate multiple AWS accounts into an organization that you centrally manage.
* **SCP (Service Control Policy):** JSON policies that specify the maximum permissions for accounts within an organization or organizational unit (OU). *Note: SCPs do not grant permissions; they only act as guardrails restricting maximum permissions.*

---

## 5. Storage & Data Protection (Amazon S3)

### Q10: What is Amazon S3, and what are its core components?
* **S3:** Simple Storage Service. Highly scalable, durable object storage.
* **Components:**
  * **Buckets:** Containers for objects, requiring globally unique names.
  * **Objects:** Files consisting of data and metadata.
  * **Storage Classes:** Tiers (Standard, Intelligent-Tiering, Standard-IA, Glacier) balancing cost and access frequency.

### Q11: Explain S3 Versioning and how it protects against accidental deletion.
* **S3 Versioning:** Keeps multiple variants of an object in the same bucket.
* **Accidental Deletion Protection:** When versioning is enabled, deleting an object inserts a `Delete Marker` rather than purging it. The file appears deleted, but the underlying version is safe and can be restored by removing the marker.

### Q12: What is S3 Cross-Region Replication (CRR), and what are its limitations regarding backups?
* **CRR:** Automatically replicates objects across buckets in different AWS regions.
* **Prerequisites:** Versioning must be enabled on both source and destination buckets; requires an IAM Role for replication permissions.
* **Backup Limitation:** CRR is **not** a full backup solution. If an object is accidentally or maliciously deleted from the source bucket, the delete operation replicates to the destination bucket as well.
