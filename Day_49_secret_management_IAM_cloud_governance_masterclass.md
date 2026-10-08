Day 49: Secrets Management, IAM, and Cloud Governance Masterclass

This comprehensive guide covers the theory, core architecture, best practices, interview questions, and hands-on implementation steps for Azure Key Vault, AWS Secrets Manager, and AWS Organizations Service Control Policies (SCPs).

---

## Module 1: Azure Key Vault (Secrets Management)

### 1. Theoretical Foundation & Core Concepts
In modern DevOps, embedding database passwords, API keys, or connection strings into source code, environment variables, or configuration files (like `appsettings.json` or `Dockerfile`) represents a major security vulnerability. **Azure Key Vault** is a centralized cloud service designed to safeguard cryptographic keys, secrets, and certificates.

*   **Secrets, Keys, and Certificates:**
    *   **Secrets:** Store arbitrary strings up to 25 KB (e.g., database passwords, connection strings).
    *   **Keys:** Hardware Security Module (HSM) protected cryptographic keys used for encryption/decryption operations.
    *   **Certificates:** Built on top of keys and secrets, supporting automated renewal and management.
*   **Access Control Models:**
    *   *Legacy Access Policies:* Granular per-vault permissions assigned directly to security principals.
    *   *Azure RBAC (Recommended):* Aligns Key Vault access with Azure's standard Role-Based Access Control model, using built-in roles like *Key Vault Secrets User* or *Key Vault Secrets Officer*.
*   **Runtime Secret Retrieval & Avoiding Hardcoding:** Instead of static secrets, applications use a **Managed Identity** (System-Assigned or User-Assigned) to authenticate against Azure Entra ID automatically, fetching credentials dynamically at startup or runtime.
*   **Rotation Concept:** Key Vault allows automatic rotation of secrets by integrating with Azure Functions, ensuring credentials change regularly without application downtime.

### 2. Practical Project: Store a Test Secret and Retrieve it Using a Managed Identity

#### Step-by-Step Implementation Guide

1. **Create an Azure Key Vault:**
   * Open the Azure Portal and search for **Key vaults**.
   * Click **+ Create**, select your Resource Group, name your vault (e.g., `kv-devops-demo-01`), choose a region, and under **Access configuration**, select **Azure role-based access control (RBAC)**.
   * Click **Review + create**, then **Create**.

2. **Store a Test Secret:**
   * Open your Key Vault (`kv-devops-demo-01`), navigate to **Objects** -> **Secrets**, and click **+ Generate/Import**.
   * Set **Name** to `MyDatabasePassword` and **Value** to `SuperSecretPassword123!`. Click **Create**.

3. **Enable Managed Identity on an Azure VM:**
   * Go to your **Virtual Machine**, select **Identity** from the left navigation pane under **Settings**.
   * Turn **System assigned** status to **On** and click **Save**.

4. **Assign RBAC Role to the VM's Identity:**
   * Return to your Key Vault, click **Access control (IAM)** -> **+ Add** -> **Add role assignment**.
   * Select the **Key Vault Secrets User** role.
   * Under **Assign access to**, choose **Managed identity**, select your Virtual Machine, and click **Review + assign**.

5. **Retrieve Secret at Runtime (Verification):**
   * SSH into your Azure VM and run:
     ```bash
     az login --identity
     az keyvault secret show --vault-name "kv-devops-demo-01" --name "MyDatabasePassword" --query "value" -o tsv
     ```
   * The plaintext password `SuperSecretPassword123!` is output directly in memory without any stored configuration files.

---

## Module 2: AWS Secrets Manager

### 1. Theoretical Foundation & Core Concepts
**AWS Secrets Manager** helps you protect access to your applications, services, and IT resources without the upfront investment and ongoing maintenance cost of managing your own infrastructure.

*   **Secret Storage & Application Integration:** Stores sensitive data securely, allowing applications to retrieve secrets via APIs rather than hardcoding them.
*   **Encryption at Rest & In Transit:** Secrets are encrypted by default using AWS Key Management Service (KMS) customer managed or AWS managed keys (`aws/secretsmanager`).
*   **Automatic Rotation:** Integrates with AWS Lambda to automatically rotate database credentials, API keys, and OAuth tokens on a defined schedule (e.g., every 30 days) without downtime.
*   **IAM Permissions:** Access to secrets is governed strictly by IAM Policies attached to IAM Roles or Users, ensuring the principle of least privilege.

### 2. Practical Project: Store/Retrieve a Test Database Credential Using an IAM Role

#### Step-by-Step Implementation Guide

1. **Store a Secret in AWS Secrets Manager:**
   * Navigate to **Secrets Manager** in the AWS Console and click **Store a new secret**.
   * Select **Other type of secret**, add key-value pairs (`username` = `admin`, `password` = `SuperSecretAWS123!`), name the secret `prod/app/db-credentials`, and save.

2. **Create an IAM Policy and Role:**
   * Go to **IAM** -> **Policies** -> **Create policy** (JSON tab):
     ```json
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Action": [
             "secretsmanager:GetSecretValue",
             "secretsmanager:DescribeSecret"
           ],
           "Resource": "arn:aws:secretsmanager:us-east-1:123456789012:secret:prod/app/db-credentials-*"
         }
       ]
     }
     ```
   * Name the policy `SecretsManagerReadPolicy`. Create an **EC2 IAM Role** named `EC2-SecretsManager-Role` and attach this policy.

3. **Attach IAM Role to EC2 Instance:**
   * Go to the **EC2 Console**, select your instance, click **Actions** -> **Security** -> **Modify IAM role**, select `EC2-SecretsManager-Role`, and update.

4. **Retrieve Secret at Runtime:**
   * SSH into the EC2 instance and execute:
     ```bash
     aws secretsmanager get-secret-value --secret-id prod/app/db-credentials --query SecretString --output text
     ```

---

## Module 3: AWS Organizations & Service Control Policies (SCPs)

### 1. Theoretical Foundation & Core Concepts
When scaling cloud infrastructure across multiple business units or environments (Dev, Test, Prod), centralized governance becomes essential. **AWS Organizations** allows you to manage multiple AWS accounts under a single master billing/management account.

*   **Organizational Units (OUs):** Logical containers for grouping accounts (e.g., a `Sandbox` OU vs. a `Production` OU) to apply collective policies.
*   **Guardrails vs. Granting Permissions:**
    *   *IAM Policies* **grant** permissions (e.g., allowing a user to launch EC2 instances).
    *   *Service Control Policies (SCPs)* act as **guardrails** that **restrict** the maximum available permissions across an account or OU. **An SCP can never grant permissions; it can only filter down what IAM allows.**
*   **Inheritance Hierarchy:** Root SCP -> OU SCP -> Child OU SCP -> Account effective permissions.

### 2. Practical Project: Explain How an SCP Can Restrict a Service/Action Across Accounts

#### Scenario & Implementation Explanation
Suppose an enterprise wants to ensure that no developer or administrator can launch EC2 instances outside the approved `us-east-1` region across the entire `Workloads` Organizational Unit.

#### Sample SCP JSON:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyEC2OutsideUSStandard",
      "Effect": "Deny",
      "Action": "ec2:*",
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:RequestedRegion": [
            "us-east-1"
          ]
        },
        "ArnNotLike": {
          "aws:PrincipalArn": [
            "arn:aws:iam::123456789012:role/OrganizationAccountAccessRole"
          ]
        }
      }
    }
  ]
}
```

#### How It Works:
1. **Evaluation Order:** When an IAM principal tries to perform an action, AWS checks IAM policies first (to see if permission is granted). Even if IAM says **Allow**, AWS then checks the applied SCPs.
2. **Explicit Deny Trigger:** If the requested region is `eu-west-1`, the condition `StringNotEquals` matches, triggering an explicit **DENY**.
3. **Cross-Account Enforcement:** Because this SCP is attached at the OU level, it automatically blankets every child account, overriding local user permissions instantly.

---

## Quick Interview & Revision Q&A

*   **Q: Why should we use Managed Identities or IAM Roles instead of storing access keys in code?**
    *   *A:* Storing keys in code risks exposure via GitHub commits. Managed identities and IAM roles rotate keys automatically behind the scenes and eliminate hardcoded credentials entirely.
*   **Q: Can an SCP grant administrator permissions to a sub-account?**
    *   *A:* No. SCPs are boundary policies; they can only restrict permissions granted by IAM, never grant new ones.
