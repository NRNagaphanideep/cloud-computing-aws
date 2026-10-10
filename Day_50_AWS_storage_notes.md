AWS S3 Concepts & Architecture

**Amazon S3 (Simple Storage Service)** is an object storage service built to store and retrieve any amount of data from anywhere on the web.

### 1. Core S3 Terminology
* **Bucket:** A public container for objects stored in S3. Bucket names must be **globally unique** across all AWS accounts worldwide.
* **Object:** The fundamental entity stored in S3, consisting of data (the file itself) and metadata (attributes like size, creation date, and content type).

### 2. S3 Storage Classes
* **S3 Standard:** For frequently accessed data.
* **S3 Intelligent-Tiering:** Automatically moves data between access tiers when access patterns change, saving cost without operational overhead.
* **S3 Standard-IA (Infrequent Access):** For data accessed less frequently, but requiring rapid access when needed.
* **S3 Glacier Flexible Retrieval / Deep Archive:** Low-cost storage for archiving data with retrieval times ranging from minutes to hours.

### 3. S3 Security, Versioning, and Lifecycle
* **Versioning:** Keeps multiple variants of an object in the same bucket, protecting against accidental deletion or overwrites.
* **Encryption:** Data is encrypted at rest by default using SSE-S3 (Server-Side Encryption with S3 Managed Keys).
* **Lifecycle Management:** Automates rules to transition objects to cheaper storage classes (e.g., moving to Glacier after 30 days) or expire/delete them permanently after a set duration.

---

## AWS Hands-on Practice Guide

### Hands-on 1: Creating an S3 Bucket, Uploading Objects, Versioning & Lifecycle

*(Designed for local conceptual execution / conceptual lab tracking)*

#### **Step 1: Create an S3 Bucket**
1. Open the **AWS Management Console** and navigate to the **S3** service dashboard.
2. Click **Create bucket**.
3. Enter a **Bucket name** (must be globally unique, e.g., `devops-demo-bucket-2026`).
4. Select your preferred **AWS Region** (e.g., `us-east-1`).
5. Under **Object Ownership**, select *ACLs disabled (recommended)*.
6. Under **Block Public Access settings for this bucket**, ensure *Block all public access* remains checked for strict security.
7. Click **Create bucket**.

#### **Step 2: Upload Objects**
1. Click on your newly created bucket name (`devops-demo-bucket-2026`).
2. Inside the **Objects** tab, click **Upload**.
3. Click **Add files** or **Add folders**, select a sample text or image file from your local machine, and click **Upload**.

#### **Step 3: Enable S3 Versioning**
1. Navigate to the **Properties** tab inside your S3 bucket.
2. Locate the **Bucket Versioning** section and click **Edit**.
3. Select **Enable**.
4. Click **Changes saved**. 
   *(Note: Once enabled, if you upload a file with the exact same name again, S3 preserves the older version rather than overwriting it).*

#### **Step 4: Configure S3 Lifecycle Rules**
1. Navigate to the **Management** tab inside your S3 bucket.
2. Click **Create lifecycle rule**.
3. Enter a **Lifecycle rule name** (e.g., `move-to-glacier-rule`).
4. Select **Limit scope to all objects in the bucket** (or apply prefix filters).
5. Under **Lifecycle rule actions**, check:
   * **Transition current versions of objects between storage classes** -> Set to move objects to **S3 Standard-IA** after `30 days` and **S3 Glacier Flexible Retrieval** after `90 days`.
   * **Expire current versions of objects** -> Set to permanently delete objects after `365 days`.
6. Click **Create rule**.
