
dule 2: Auditing - AWS CloudTrail**

### **Core Concepts**
* **AWS CloudTrail:** A governance, compliance, and auditing service that records API calls and account activity across your AWS infrastructure.
* **The 5 Ws of Auditing:** Every CloudTrail event answers **Who** made the request, **What** action was taken, **What Service** was called, **When** it happened, and **From Where** (Source IP).
* **Management Events vs. Data Events:**
  * *Management Events:* Control plane operations performed on resources (e.g., `CreateUser`, `AttachSecurityGroup`, `RunInstances`).
  * *Data Events:* Data plane operations performed *on or within* a resource (e.g., `GetObject`, `PutObject` on an S3 bucket).
* **Security Troubleshooting:** Investigating unauthorized changes, privilege escalations, or accidental resource deletions by querying CloudTrail history.

---

### **Hands-On Practice: Finding and Analyzing an API Event**

#### **Step 1: Generate an Audited Action**
Using the AWS CLI, perform a standard operation that CloudTrail tracks:
```bash
# Create a test S3 bucket to generate an API event
aws s3 mb s3://audit-lab-bucket-xyz-12345 --region us-east-1
```

#### **Step 2: Investigate the Event in CloudTrail**
1. Open the **AWS Management Console** and navigate to **CloudTrail**.
2. Click on **Event history** in the left navigation pane.
3. Filter by **Event source**: `s3.amazonaws.com` or look for the `CreateBucket` event name.
4. Expand the event record to inspect the JSON structure. Identify the key attributes:
   * **`userIdentity` (The Actor):** Shows the IAM user or role ARN that executed the command.
   * **`eventName` (The Action):** `CreateBucket`.
   * **`requestParameters` (The Resource):** Shows the specific bucket name `audit-lab-bucket-xyz-12345`.
   * **`sourceIPAddress`:** The IP address from which the CLI command was initiated.

---

## **Module 3: Monitoring - Metrics & Logs Investigation**

### **Core Concepts**
* **Metrics vs. Logs:**
  * *Metrics:* Numerical, time-series data captured at regular intervals (e.g., CPU Utilization, Network In/Out). Lightweight and ideal for threshold alerts.
  * *Logs:* Discrete records of events, errors, or application output containing rich textual context and timestamps.
* **Centralized Observability:** Aggregating metrics and logs into a single workspace (e.g., Amazon CloudWatch Logs / Azure Monitor Log Analytics) to diagnose anomalies across distributed fleets.
* **Operational Troubleshooting:** Using log queries (like CloudWatch Logs Insights or KQL) to search for specific error patterns (`ERROR`, `Exception`, `Timeout`) during system outages.

---

### **Hands-On Practice: Monitoring and Log Investigation (AWS CloudWatch)**

#### **Step 1: Generate a Log Event**
If you have an active EC2 instance running with the CloudWatch Agent or SSM, or by pushing test logs via AWS CLI:
```bash
# Create a log group and stream manually to simulate application logging
aws logs create-log-group --log-group-name /aws/app/lab-service
aws logs create-log-stream --log-group-name /aws/app/lab-service --log-stream-name instance-01
```

#### **Step 2: Investigate and Query Logs via CloudWatch**
1. Navigate to the **CloudWatch Console** -> **Log groups**.
2. Click on `/aws/app/lab-service`.
3. Select **Logs Insights** to run query filtering.
4. Use a sample query to inspect entries:
   ```text
   fields @timestamp, @message
   | sort @timestamp desc
   | limit 20
   ```
5. Review the metrics dashboard to check CPU utilization alarms and ensure operational health.
