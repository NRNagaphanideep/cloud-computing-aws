Day 58: Production Lab - Secrets + Data Protection

## 1. Detailed Topic Notes

### A. Runtime Key Vault & Secrets Manager Retrieval
* **Security Principle**: Eliminates hardcoded credentials from application source code and configuration files.
* **Runtime Fetching**: Applications authenticate using IAM roles or managed identities to query secrets dynamically from the vault upon initialization.
* **Rotation**: Supports automated rotation of database credentials and API keys without requiring application redeployment.

### B. EBS Snapshots & Data Protection
* **Incremental Backups**: EBS snapshots back up only the blocks that have changed since the last snapshot, saving storage space.
* **Disaster Recovery**: Snapshots can be restored into new EBS volumes or copied across different AWS regions for high availability.
* **Automation**: Integrated with AWS Backup or lifecycle policies to schedule automated snapshot creation.

### C. S3 Lifecycle Policies & Backups
* **Cost Optimization**: Transition older log files and backup archives to lower-cost storage tiers (e.g., S3 Standard-IA, Glacier) automatically.
* **Object Versioning**: Protects against accidental overwrites and deletions by keeping multiple variants of an object in the same bucket.

### D. Recovery Workflow
* **Incident Response**: Establishes a repeatable playbook to recover storage volumes from snapshots and re-provision secrets during emergencies.
* **Validation**: Regular testing of backup restoration paths to ensure Recovery Time Objectives (RTO) and Recovery Point Objectives (RPO) are met[cite: 5].

---

## 2. Hands-On Practice: Secret Retrieval & Backup/Restore Procedure

### Task 1: Store and Retrieve a Secret at Runtime
1. Open the **AWS Secrets Manager** console and click **Store a new secret**[cite: 5].
2. Choose secret type (e.g., *Credentials for RDS database*), enter your username and password, and click **Next**[cite: 5].
3. Name your secret (e.g., `prod/db/credentials`) and configure automatic rotation if desired[cite: 5].
4. Complete the wizard and use the AWS CLI or SDK inside your application runtime to fetch the secret dynamically:
   ```bash
   aws secretsmanager get-secret-value --secret-id prod/db/credentials --query 'SecretString' --output text
```

## Task 2: EBS Snapshot Backup & Restore Procedure (Data Protection)

### Overview & Concepts
* **EBS Snapshots**: Point-in-time incremental backups of block storage volumes stored securely in Amazon S3[cite: 5]. Only the blocks that have changed since the last snapshot are saved, saving storage overhead.
* **Recovery Workflow**: The structured operational procedure required to restore a volume from a snapshot and attach it to a recovery instance during data loss emergencies[cite: 5].

### Step-by-Step Hands-On Guide
1. **Create an EBS Snapshot**:
   * Navigate to the **EC2 Console** and select **Volumes** under *Elastic Block Store*[cite: 5].
   * Select your target production volume, click **Actions**, and choose **Create snapshot**[cite: 5].
   * Provide a descriptive name and tag (e.g., `Name: Prod-DB-Snapshot-Day58`), then click **Create snapshot**[cite: 5].
2. **Execute the Recovery Workflow (Restore Volume)**:
   * Go to the **Snapshots** section in the EC2 console[cite: 5].
   * Select your completed snapshot, click **Actions**, and choose **Create volume from snapshot**[cite: 5].
   * Choose the matching Availability Zone (AZ) where your recovery instance resides and click **Create volume**[cite: 5].
   * Attach the newly restored volume to your target recovery EC2 instance (`Actions` > **Attach volume**) to complete the backup/restore procedure[cite: 5].

---

## Task 3: S3 Lifecycle Policies & Automated Backups

### Overview & Concepts
* **S3 Lifecycle/Backups**: Automated rules governing object transitions across storage tiers (e.g., Standard to Glacier) and expiration policies for cost-efficient data retention[cite: 5].
* **Data Protection & Recovery Workflow**: Ensuring long-term archiving and compliance with automated backup management[cite: 5].

### Step-by-Step Hands-On Guide
1. **Configure S3 Lifecycle Backup Rules**:
   * Open the **Amazon S3 console** and select your target backup bucket[cite: 5].
   * Go to the **Management** tab and click **Create lifecycle rule**[cite: 5].
   * Name the rule (e.g., `Backup-Archival-Lifecycle`) and configure the scope (e.g., applying to objects with the prefix `backups/`)[cite: 5].
2. **Define Transitions & Expirations**:
   * Configure a transition rule to move current versions of backup objects to **S3 Standard-Infrequent Access (IA)** after 30 days[cite: 5].
   * Add a transition to move objects to **S3 Glacier Flexible Retrieval** after 90 days for long-term audit compliance[cite: 5].
   * Set an expiration rule to permanently delete temporary staging artifacts after 365 days, executing a complete data protection and cost optimization workflow[cite: 5].
