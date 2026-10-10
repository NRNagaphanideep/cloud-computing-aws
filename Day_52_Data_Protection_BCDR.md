Day 52: Data Protection & BCDR (Business Continuity & Disaster Recovery)

This document covers detailed architectural concepts, operational workflows, and interview preparation questions for **Amazon EBS Snapshots** and **BCDR Metrics (RPO & RTO)**.

---

## Part 1: AWS EBS Snapshots (Elastic Block Store)

### 1. Core Concept: Point-in-Time Snapshots
* **Amazon EBS (Elastic Block Store):** Persistent block storage volumes designed for use with **Amazon EC2 (Elastic Compute Cloud)** instances.
* **Snapshot Definition:** A point-in-time, read-only backup copy of an EBS volume stored securely in **Amazon S3 (Simple Storage Service)**. 
* **Crash-Consistent vs. Application-Consistent:**
  * **Crash-Consistent:** Captured while the EC2 instance is running. It captures data already flushed to disk, equivalent to a sudden power outage.
  * **Application-Consistent:** Achieved by pausing application writes or unmounting the volume prior to snapshot creation to ensure in-memory buffers are flushed.

---

### 2. Incremental Behavior Architecture
EBS snapshots operate on an **incremental backup model**, making them highly cost-effective and storage-efficient.

```
[Initial Snapshot 1] -> Stores all allocated blocks (e.g., 10 GB)
        │
[Data Changes] -------> Only 2 GB of data blocks are modified
        │
[Snapshot 2] ---------> Stores ONLY the 2 GB modified blocks (References Snapshot 1 for unchanged data)
```

* **Baseline (First Snapshot):** Copies the entire volume data structure to Amazon S3.
* **Subsequent Snapshots:** Only delta changes (blocks that have been written or modified since the previous snapshot) are backed up.
* **Deletion Logic:** When you delete a snapshot, AWS automatically retains any underlying data blocks required by subsequent or older snapshots. You only pay for the unique data blocks retained.

---

### 3. Restore Workflow
To recover data or restore a system from an EBS snapshot:
1. **Locate Snapshot:** Identify the required snapshot ID in the AWS Management Console or AWS CLI.
2. **Create Volume:** Instantiation a new EBS volume from the snapshot in the target **Availability Zone (AZ)**.
3. **Attach to Instance:** Attach the newly created volume as a secondary data drive (or as a new root volume) to an active EC2 instance.
4. **Mount & Verify:** Mount the file system within the operating system (`mount /dev/sdf /mnt/data`) and verify data integrity.

---

### 4. Amazon Data Lifecycle Manager (DLM)
* **Definition:** An automated policy-driven service used to automate the creation, retention, and deletion of EBS snapshots and EBS-backed AMIs (Amazon Machine Images).
* **Policy Parameters:**
  * **Schedule:** Frequency (e.g., every 12 hours, daily, weekly).
  * **Retention Rule:** Number of snapshots to keep or time-based expiration (e.g., retain for 30 days).
  * **Cross-Region Copy:** Automatically copy snapshots to another AWS region for disaster recovery.

---

## Part 2: BCDR (Business Continuity & Disaster Recovery) - RPO & RTO

### 1. Fundamental Definitions

#### **RPO (Recovery Point Objective):**
* **Definition:** The maximum acceptable age of files or data recovered from backup storage after a system failure. It defines the maximum allowable **data loss window**.
* **Formula / Metric:** Measured in units of time (e.g., seconds, minutes, hours).
* **Example:** If an organization's RPO is **1 hour**, the system must take backups at least every hour so that no more than 60 minutes worth of data is lost during a disaster.

#### **RTO (Recovery Time Objective):**
* **Definition:** The maximum acceptable length of time that a computer, system, network, or application can be down after a failure or disaster. It defines the acceptable **downtime window**.
* **Formula / Metric:** Measured in units of time (e.g., minutes, hours, days).
* **Example:** If an organization's RTO is **15 minutes**, automated failover processes must restore full operational service within 15 minutes of an outage.

---

### 2. Mapping Strategies to Business Requirements

| Requirement Level | RPO Target | RTO Target | Architecture / Implementation Strategy |
| :--- | :--- | :--- | :--- |
| **Critical (Financial/E-Commerce)** | Near Zero | Near Zero | Multi-Region Active-Active deployment, continuous synchronous replication, automated DNS failover (Route 53). |
| **High (Production Web Apps)** | < 1 Hour | < 1 Hour | Automated hourly EBS snapshots, Multi-AZ deployments with Auto Scaling, Warm Standby. |
| **Medium (Internal Tools)** | 24 Hours | 4 Hours | Daily automated snapshots via Data Lifecycle Manager (DLM), Pilot Light or Backup & Restore strategy. |
| **Low (Dev/Test Environments)** | 24-48 Hours | 24 Hours | Cold backup, manual restore scripts. |

---

## Part 3: Top Interview Questions & Technical Answers

### **Q1: How do EBS Snapshots handle deletion of intermediate snapshots without losing data?**
> **Answer:** EBS snapshots track data blocks using internal point-in-time references. When an intermediate snapshot is deleted, the AWS backend engine identifies the unique data blocks associated only with that snapshot and purges them. Any data blocks referenced by later snapshots are merged and preserved automatically, ensuring that full recovery remains possible from all remaining snapshots.

---

### **Q2: What is the difference between RPO and RTO? Give a practical scenario.**
> **Answer:** 
> * **RPO** focuses on data loss (How much data can we afford to lose?).
> * **RTO** focuses on time/downtime (How quickly must the application be back online?).
> 
> *Scenario:* For an online banking system, an RPO of 0 seconds is required to avoid losing transaction records, requiring real-time database replication. However, an RTO of 5 minutes might be acceptable to allow automated health checks and secondary database failover to complete.

---

### **Q3: How do you ensure an EBS snapshot is application-consistent when backing up an active database instance?**
> **Answer:** To achieve application consistency, you must ensure that in-memory cached data and pending transactions are written to disk before the snapshot starts. This can be done by:
> 1. Temporarily pausing I/O operations or freezing the file system using tools like `fsfreeze` in Linux.
> 2. Putting the database into backup/read-only mode.
> 3. Taking the snapshot, then unfreezing the file system or resuming database writes.
> 4. Alternatively, stopping the EC2 instance briefly during snapshot initiation guarantees full consistency.

---

### **Q4: How does Amazon Data Lifecycle Manager (DLM) contribute to operational efficiency and cost optimization?**
> **Answer:** DLM removes manual overhead by enforcing automated snapshot lifecycles. It prevents cost bloat by enforcing retention rules (e.g., automatically deleting snapshots older than 30 days) and enables cross-region copy features, automating disaster recovery preparedness without custom scripting.
