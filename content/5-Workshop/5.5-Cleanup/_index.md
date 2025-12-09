---
title: "Clean up"
date: 2025-09-07
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

# Clean Up Resources

---

# 1. Networking & VPC Clean Up

## 1.1 Delete Route 53 Hosted Zone & Resolver Rules

1. Navigate to **Route 53 → Hosted Zones**.  
   Delete the Private Hosted Zone `s3.us-east-1.amazonaws.com`.

2. Go to **Route 53 Resolver → Rules**.  
   Disassociate the rule `myS3Rule` from **VPC Onprem**, then delete it.

---

## 1.2 Delete VPC Resources

**Steps:**

1. Go to **EC2 → Network Interfaces**  
   - Delete ENIs created for VPC Endpoints, Resolver Endpoints, or EC2 instances after termination.

2. Go to **VPC console:**
   - Delete **Security Groups** created for the lab.
   - Delete **Subnets** (ensure no ENIs or route tables attached).
   - Delete **Route Tables**.
   - Delete **Internet Gateway** (detach first).
   - Delete **NAT Gateway** (if created).
   - Finally, delete the **VPC** `metropolitano`.

---

# 2. EC2 Clean Up

1. Go to **EC2 → Instances**.
2. Select instance `metropolitano-version-1` and **Terminate**.
3. Delete:
   - Elastic IPs (if allocated)
   - Key pair `myKey.pm` (local file optional)
   - Security group `public-web-sg`

---

# 3. RDS Clean Up

1. Go to **RDS Console → Databases**.
2. Select SQL Server instance and choose **Delete**.
3. Options:
   - Disable final snapshot (optional)
4. Delete the **DB Subnet Group** `private-db-metropolitano`.
5. Delete the **security group** `private-db-sg` if unused.

---

# 4. S3 Clean Up

1. Go to **S3 Console**.
2. Empty the bucket `metropolitano-2025`.
3. Click **Delete bucket**.

---

# 5. CloudFront Clean Up

1. Go to **CloudFront → Distributions**.
2. Select the distribution.
3. Choose **Disable** → Wait for status “Disabled”.
4. Click **Delete**.

---

# 6. Kinesis Data Stream Clean Up

1. Go to **Kinesis Console → Data Streams**.
2. Select `metropolitano-stream`.
3. Click **Delete**.

---

# 7. EventBridge Clean Up

1. Go to **EventBridge → Rules**.
2. Select `metropolitano-event-rule`.
3. Click **Delete**.
4. If event buses or targets were created manually, delete them too.

---

# 8. SQS Queue Clean Up

1. Go to **SQS Console → Queues**.
2. Select `metropolitano-queue`.
3. Click **Delete**.

---

# 9. SNS Clean Up

1. Go to **SNS Console → Topics**.
2. Select `metropolitano-alerts`.
3. Delete subscriptions (email/SMS).
4. Delete topic.

---

# 10. CloudWatch Clean Up

## 10.1 Delete Alarms
1. CloudWatch → Alarms → Select alarms → Delete.

## 10.2 Delete Logs
1. CloudWatch → Log Groups  
2. Delete:
   - EC2 log groups  
   - Lambda log groups (if any)  
   - VPC Flow Logs (if created)

## 10.3 Delete Metrics (optional)
Metrics automatically expire; no action required.

---

# 11. QuickSight Clean Up

If enabled, QuickSight can generate monthly cost.

**Steps:**
1. Go to **QuickSight Console → Manage QuickSight**.
2. Delete:
   - Datasets
   - SPICE datasets
   - Dashboards
   - Analyses

3. If not needed → **Unsubscribe QuickSight account**.

---