---
title : "Chuẩn bị tài nguyên"
date: 2025-09-07
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

## Phần 1: Networking & Security

### 1.1 Create VPC

**Bước:**
1. Vào **VPC console -> Yours VPCs -> Create VPC**

![create vpc](/images/5-Workshop/5.4-S3-onprem/image.png)

2. Chọn **VPC and more**
3. Name:`metropolitano`
4. Tạo **public/private subnets** trên ≥ 2 AZ  
5. VPC endpoints: None (baseline)

![choose the vpc and more](/images/5-Workshop/5.4-S3-onprem/image_1.png)
![VPC endpoints](/images/5-Workshop/5.4-S3-onprem/image_2.png)


### 1.2 Enable Auto-assign Public IPv4 cho public subnet

**Bước:**
1. VPC → Subnets → chọn public subnet → Edit subnet settings
2. Enable: **Auto-assign Public IPv4** → Save

![Enable public IPv4](/images/5-Workshop/5.4-S3-onprem/image_3.png)
![Enable public IPv4](/images/5-Workshop/5.4-S3-onprem/image_4.png)

### 1.3 Security Groups

#### 1.3.1 Public Web/EC2 SG

- Name: `public-web-sg`
- Inbound rules: HTTP/HTTPS từ Internet, SSH từ admin IP

![Inbound](/images/5-Workshop/5.4-S3-onprem/image_5.png)

#### 1.3.2 Private Database SG

- Name: 'private-db-sg'
- Inbound: MS SQL 1433 chỉ cho phép từ `public-web-sg`

![Inbound](/images/5-Workshop/5.4-S3-onprem/image_6.png)


## Phần 2: Database setup(RDS SQL Server)
### 2.1 Create DB Subnet Groups

- RDS → Subnet Groups → Create  
- Name: `private-db-metropolitano`  
- VPC: `metropolitano-vpc`  
- Subnets: private subnets trong 2 AZ

![Subnet groups](/images/5-Workshop/5.4-S3-onprem/image_7.png)



### 2.2 Create RDS Instance

**Bước:**
1. RDS -> Databases - Create database
![Create database](/images/5-Workshop/5.4-S3-onprem/image_8.png)

2. Engine: Microsoft SQL Server
3. Template: Dev/Test
4. Credentials: self-managed 
5. Public access: No
6. Security group: `private-db-sg`

![Create database](/images/5-Workshop/5.4-S3-onprem/image_9.png)

## Phần 3: Compute - EC2 Application Server

**Bước:**
1. EC2 → Instances → Launch instance 
![Create Instance](/images/5-Workshop/5.4-S3-onprem/image_10.png)

2. Name: `metropolitano-version-1` 
3. AMI: Amazon Linux 
4. Instance type: t3.medium
5. Key pair: myKey.pm

![Create Instance](/images/5-Workshop/5.4-S3-onprem/image_11.png)

6. Network: VPC `metropolitano-vpc`, subnet public, SG `public-web-sg`  

![Create Instance](/images/5-Workshop/5.4-S3-onprem/image_12.png)


7. Chờ instance status: 3/3 passed

![Create Instance](/images/5-Workshop/5.4-S3-onprem/image_13.png)


## Phần 4: Storage - S3 bucket

### 4.1 Create Bucket

1. Choose S3 -> General purpose buckets -> Create buckets

![Create Instance](/images/5-Workshop/5.4-S3-onprem/image_14.png)

2. Name: `metropolitano-2025`
3. Object Ownership: ACLs disabled (recommended)
3. Block Public Access settings for this bucket: Untick `Block all public access`
4. Click create bucket

![Create bucket ](/images/5-Workshop/5.4-S3-onprem/image_15.png)

5. Upload the file dist from FE:

![Create bucket ](/images/5-Workshop/5.4-S3-onprem/image_16.png)


### Phần 5: CloudFront

**Bước:**
1. CloudFront console -> Distributions -> Create distribution

![Create bucket ](/images/5-Workshop/5.4-S3-onprem/image_17.png)

2. Choose a free plan 

3. Name: Metropolitano

4. Origin type: Amazon S3

5. S3 Origin: `metropolitano-2025`

6. Origin path: `/dist`

7. Cache setting: Customize cache settings
    Viewer protocol policy: Redirect HTTP to HTTPS
    Allowed HTTP methods: GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE

    ![Create cloudfront](/images/5-Workshop/5.4-S3-onprem/image_18.png)


8. Sau khi tạo xong thì chúng ta nên đợi tầm 3 đến 5 phút

    ![Create cloudfront](/images/5-Workshop/5.4-S3-onprem/image_19.png)

    ![Websites](/images/5-Workshop/5.4-S3-onprem/image_20.png)


### Phần 6 : Kinesis - Event Stream 

**Bước:**
1. Console → Kinesis → Create Data Stream

![Websites](/images/5-Workshop/5.4-S3-onprem/image_21.png)

2. Name: `metropolitano-stream`
3. Shard: tùy lượng dữ liệu
4. Producer(EC2) gửi dữ liệu, Consumer xử lý dữ liệu

![Websites](/images/5-Workshop/5.4-S3-onprem/image_22.png)

### Phần 7: EventBrighe - Event Automation
**Bước:**
1. Console → EventBridge → Create Rule
2. Name: `metropolitano-event-rule`
3. Event source: AWS services (CloudWatch Alarm, S3 Object Created)
4. Target: SQS, SNS

![Websites](/images/5-Workshop/5.4-S3-onprem/image_26.png)

### Phần 8: SQS - Queue

**Bước:**
1. Console → SQS → Create Queue
2. Name: `metropolitano-queue`
3. Type: Standard / FIFO

![Websites](/images/5-Workshop/5.4-S3-onprem/image_28.png)

### Phần 9: SNS - Notification

**Bước:**
1. Console → SNS → Create Topic
2. Name: `metropolitano-alerts`
3. Add subscriptions: email, SMS

![Websites](/images/5-Workshop/5.4-S3-onprem/image_30.png)

### Phần 10: CloudWatch - Monitoring


**Bước:**
1. Console → CloudWatch → Create Alarm
2. Metric: EC2 CPU, RDS status, Kinesis throughput
3. Action: gửi SNS notification khi vượt ngưỡng

![Websites](/images/5-Workshop/5.4-S3-onprem/image_31.png)
![Websites](/images/5-Workshop/5.4-S3-onprem/image_32.png)
![Websites](/images/5-Workshop/5.4-S3-onprem/image_33.png)

### Phần 11: Analytics & Visualization

**Bước:**
1. Console → QuickSight → Sign up / Create account
2. Dataset: kết nối RDS / S3 / Athena
3. Create Analysis → chọn biểu đồ, dashboard

![Websites](/images/5-Workshop/5.4-S3-onprem/image_34.png)




