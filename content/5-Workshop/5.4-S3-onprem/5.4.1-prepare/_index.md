---
title : "Prepare the environment"
date: 2025-09-07
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---
# Part 1: Networking & Security

## 1.1 Create VPC

**Steps:**

1. Go to **VPC Console → Your VPCs → Create VPC**

![create vpc](/images/5-Workshop/5.4-S3-onprem/image.png)

2. Select **VPC and more**
3. Name: `metropolitano`
4. Create **public/private subnets** across at least 2 Availability Zones
5. VPC Endpoints: None (baseline)

![choose the vpc and more](/images/5-Workshop/5.4-S3-onprem/image_1.png)
![VPC endpoints](/images/5-Workshop/5.4-S3-onprem/image_2.png)
---

## 1.2 Enable Auto-assign Public IPv4 for Public Subnet

**Steps:**

1. VPC → Subnets → Select public subnet → Edit subnet settings  
2. Enable **Auto-assign Public IPv4** → Save  

![Enable public IPv4](/images/5-Workshop/5.4-S3-onprem/image_3.png)
![Enable public IPv4](/images/5-Workshop/5.4-S3-onprem/image_4.png)

---

## 1.3 Security Groups

### 1.3.1 Public Web/EC2 Security Group

- Name: `public-web-sg`
- Inbound rules:
  - HTTP/HTTPS from Internet
  - SSH from admin IP only

  ![Inbound](/images/5-Workshop/5.4-S3-onprem/image_5.png)

### 1.3.2 Private Database Security Group

- Name: `private-db-sg`
- Inbound rules:
  - MS SQL Server (1433) **only** from `public-web-sg`

![Inbound](/images/5-Workshop/5.4-S3-onprem/image_6.png)
---

# Part 2: Database Setup (RDS SQL Server)

## 2.1 Create DB Subnet Group

- RDS → Subnet Groups → Create  
- Name: `private-db-metropolitano`  
- VPC: `metropolitano-vpc`  
- Subnets: private subnets across 2 AZs

---

## 2.2 Create RDS SQL Server Instance

**Steps:**

1. RDS → Databases → Create database  
![Create database](/images/5-Workshop/5.4-S3-onprem/image_8.png)
2. Engine: **Microsoft SQL Server**
3. Template: **Dev/Test**
4. Credentials: Self-managed  
5. Public access: **No**
6. Security group: `private-db-sg`
![Create database](/images/5-Workshop/5.4-S3-onprem/image_9.png)
---

# Part 3: Compute – EC2 Application Server

**Steps:**

1. Go to EC2 → Instances → Launch instance 

![Create Instance](/images/5-Workshop/5.4-S3-onprem/image_10.png)
2. Name: `metropolitano-version-1`  
3. AMI: **Amazon Linux**  
4. Instance type: **t3.medium**  
5. Key pair: `myKey.pm`  
![Create Instance](/images/5-Workshop/5.4-S3-onprem/image_11.png)
6. Network:
   - VPC: `metropolitano-vpc`
   - Subnet: Public subnet
   - Security Group: `public-web-sg`

   ![Create Instance](/images/5-Workshop/5.4-S3-onprem/image_12.png)
7. Wait until instance status = **3/3 checks passed**
![Create Instance](/images/5-Workshop/5.4-S3-onprem/image_13.png)
---

# Part 4: Storage – S3 Bucket

## 4.1 Create S3 Bucket

1. Go to S3 → Buckets → Create bucket  
![Create Instance](/images/5-Workshop/5.4-S3-onprem/image_14.png)
2. Name: `metropolitano-2025`
3. Object Ownership: **ACLs disabled (recommended)**  
4. Block Public Access: Disable **Block all public access** 
![Create bucket ](/images/5-Workshop/5.4-S3-onprem/image_15.png) 
5. Click **Create bucket**  
6. Upload the **dist** folder from frontend
![Create bucket ](/images/5-Workshop/5.4-S3-onprem/image_16.png)

---

# Part 5: CloudFront

**Steps:**

1. CloudFront Console → Distributions → Create distribution 
![Create bucket ](/images/5-Workshop/5.4-S3-onprem/image_17.png) 
2. Plan: Free tier  
3. Name: `Metropolitano`  
4. Origin type: **Amazon S3**  
5. Origin: `metropolitano-2025`  
6. Origin path: `/dist`  
7. Cache settings:
   - Viewer protocol policy: **Redirect HTTP to HTTPS**
   - Allowed HTTP methods: `GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE`
   ![Create cloudfront](/images/5-Workshop/5.4-S3-onprem/image_18.png)

Wait 3–5 minutes for deployment.

    ![Create cloudfront](/images/5-Workshop/5.4-S3-onprem/image_19.png)

    ![Websites](/images/5-Workshop/5.4-S3-onprem/image_20.png)

---

# Part 6: Kinesis – Event Stream

**Steps:**

1. Console → Kinesis → Create Data Stream  

![Websites](/images/5-Workshop/5.4-S3-onprem/image_21.png)
2. Name: `metropolitano-stream`  
3. Shards: Select based on expected data volume  
4. Producers (EC2) will send data; Consumers will process data 
![Websites](/images/5-Workshop/5.4-S3-onprem/image_22.png) 

---

# Part 7: EventBridge – Event Automation

**Steps:**

1. Console → EventBridge → Create Rule  
2. Name: `metropolitano-event-rule`  
3. Event source: AWS services (CloudWatch Alarm, S3 Object Created)  
4. Target: **SQS**, **SNS**

![Websites](/images/5-Workshop/5.4-S3-onprem/image_26.png)
---

# Part 8: SQS – Message Queue

**Steps:**

1. Console → SQS → Create Queue  
2. Name: `metropolitano-queue`  
3. Queue type: **Standard** or **FIFO**
![Websites](/images/5-Workshop/5.4-S3-onprem/image_28.png)
---

# Part 9: SNS – Notification Service

**Steps:**

1. Console → SNS → Create Topic  
2. Name: `metropolitano-alerts`  
3. Add subscriptions: Email, SMS
![Websites](/images/5-Workshop/5.4-S3-onprem/image_30.png)
---

# Part 10: CloudWatch – Monitoring & Alerts

**Steps:**

1. Console → CloudWatch → Create Alarm  
2. Metrics:
   - EC2 CPU utilization
   - RDS instance status
   - Kinesis throughput
3. Actions:
   - Send notification via SNS when threshold is exceeded  


![Websites](/images/5-Workshop/5.4-S3-onprem/image_31.png)
![Websites](/images/5-Workshop/5.4-S3-onprem/image_32.png)
![Websites](/images/5-Workshop/5.4-S3-onprem/image_33.png)

---

# Part 11: Analytics & Visualization

**Steps:**

1. Console → QuickSight → Sign up / Create account  
2. Dataset sources: **RDS**, **S3**, or **Athena**  
3. Create Analysis → Build dashboards & visual reports  

![Websites](/images/5-Workshop/5.4-S3-onprem/image_34.png)
---