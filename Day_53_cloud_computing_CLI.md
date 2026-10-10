Topic 2: AWS CLI (Amazon Web Services Command Line Interface)

AWS CLI is an extremely powerful tool for controlling and automating AWS services from the command line.

### 1. Credentials & Profiles Configuration
* **Meaning:** We need our security credentials (Access Key ID, Secret Access Key, Region) to communicate with the AWS account.
* **Command:**
  * `aws configure`
  * Running this command prompts for the following details:
    1. AWS Access Key ID
    2. AWS Secret Access Key
    3. Default region name (e.g., `us-east-1`)
    4. Default output format (e.g., `json` or `table`)
* **Profiles:** If we have multiple AWS accounts, we can save them as separate profiles:
  * `aws configure --profile dev-account`
  * To run commands using a specific profile: `aws s3 ls --profile dev-account`

### 2. `sts get-caller-identity` (Identity Verification)
* **Meaning:** Used to verify which AWS user or role (IAM User/Role) is currently connected to the CLI session.
* **Command:** `aws sts get-caller-identity`
* **Output:** Displays our Account ID, User ARN (Amazon Resource Name), and UserId in JSON format.

### 3. Core AWS Services Commands
* **EC2 (Elastic Compute Cloud):**
  * To list instances: `aws ec2 describe-instances --output table`
  * To launch a new EC2 instance: `aws ec2 run-instances --image-id ami-xxxxxx --instance-type t2.micro ...`
* **S3 (Simple Storage Service):**
  * To list buckets: `aws s3 ls`
  * To upload a local file to an S3 bucket: `aws s3 cp myfile.txt s3://my-bucket-name/`
* **IAM (Identity and Access Management):**
  * To list users: `aws iam list-users`

### 4. Filtering & Query Basics (`--query` & `--filters`)
* To filter large outputs, we use JMESPath queries and the `--filters` option.
* **Example (To view only running EC2 instance IDs):**
  `aws ec2 describe-instances --filters "Name=instance-state-name,Values=running" --query "Reservations[*].Instances[*].InstanceId" --output text`
## **Module 2: AWS CLI (Command Line Interface)**

### **1. Core Concepts & Abbreviations**
* **AWS CLI:** Unified tool to manage AWS services from your terminal.
* **AWS STS (Security Token Service):** A web service that enables you to request temporary, limited-privilege credentials.
* **`sts get-caller-identity`:** Diagnostic command to verify which IAM user or role is currently active in the CLI session.
* **IAM Profiles:** Named credential configurations (`~/.aws/credentials`) allowing easy switching between multiple AWS accounts.

### **2. Hands-on Practice: Configure Profiles and Perform EC2/S3 Operations**

#### **Step 1: Securely Configure Credentials and Profiles**
```bash
# Configure default AWS profile
aws configure
# Prompts for:
# AWS Access Key ID: AKIAIOSFODNN7EXAMPLE
# AWS Secret Access Key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
# Default region name: us-east-1
# Default output format: json

# Configure an alternative named profile (e.g., for staging/production)
aws configure --profile staging-profile
```

#### **Step 2: Verify Identity**
```bash
# Check active caller identity
aws sts get-caller-identity

# Check identity using the named profile
aws sts get-caller-identity --profile staging-profile
```

#### **Step 3: S3 Operations via CLI**
```bash
# 1. Create a unique S3 bucket
aws s3 mb s3://my-devops-lab-bucket-2026 --region us-east-1

# 2. Create a sample local text file and upload it
echo "Hello DevOps World!" > sample.txt
aws s3 cp sample.txt s3://my-devops-lab-bucket-2026/sample.txt

# 3. List contents of the bucket
aws s3 ls s3://my-devops-lab-bucket-2026/

# 4. Clean up bucket objects and delete bucket
aws s3 rm s3://my-devops-lab-bucket-2026/sample.txt
aws s3 rb s3://my-devops-lab-bucket-2026
```

#### **Step 4: EC2 Operations with Filtering**
```bash
# List all EC2 instances in a table format
aws ec2 describe-instances \
  --query "Reservations[*].Instances[*].{InstanceId:InstanceId, State:State.Name, Type:InstanceType}" \
  --output table

# Filter only running EC2 instances
aws ec2 describe-instances \
  --filters "Name=instance-state-name,Values=running" \
  --query "Reservations[*].Instances[*].InstanceId" \
  --output text
```

---

## **Module 3: AWS Systems Manager (SSM)**

### **1. Core Concepts & Abbreviations**
* **SSM (AWS Systems Manager):** Management service for gaining operational insights and executing infrastructure actions across hybrid environments.
* **Managed Node:** Any EC2 instance or on-premises server configured with the SSM Agent and granted IAM permissions to communicate with Systems Manager.
* **SSM Agent:** Software installed on an instance that processes requests from the Systems Manager service and configures the machine.
* **Session Manager:** Capability that lets you manage EC2 instances through an interactive, one-click browser-based shell or AWS CLI, eliminating the need to open inbound SSH (Port 22) ports.
* **Run Command:** Tool used to remotely and securely manage the configuration of managed nodes at scale (Fleet Operations) without logging in individually.

### **2. Hands-on Practice: Connect to a Lab EC2 through Session Manager**

#### **Prerequisites / Theoretical Setup Steps:**
1. **IAM Instance Profile:** Create an IAM role for EC2 with the managed policy `AmazonSSMManagedInstanceCore` attached.
2. **Launch EC2 Instance:** Launch an Ubuntu/Amazon Linux EC2 instance in a private or public subnet, and attach the above IAM role to it. *(Ensure outbound internet access via NAT Gateway or VPC Endpoints if in a private subnet, so the SSM Agent can reach AWS endpoints).*
3. **Verify SSM Agent:** Ensure `amazon-ssm-agent` service is running on the instance (`sudo systemctl status amazon-ssm-agent`).

#### **Execution via AWS Console:**
1. Open the **AWS Systems Manager Console**.
2. In the left navigation pane, click on **Session Manager**.
3. Click **Start Session**.
4. You will see a list of available **Managed Nodes**. Select your target EC2 instance and click **Start Session**.
5. An interactive terminal window opens directly in your browser without requiring SSH keys or opening Port 22 in your Security Groups.

#### **Execution via AWS CLI:**
```bash
# Connect to your managed EC2 instance directly from your local terminal using Session Manager plugin
aws ssm start-session --target i-0123456789abcdef0
```
