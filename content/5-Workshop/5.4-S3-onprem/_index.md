---
title : "Prepare Environment Configuration"
date: 2025-09-07 
weight : 4 
chapter : false
pre : " <b> 5.4. </b> "
---

#### AWS Metropolitano Workshop

In this section, you will prepare the entire environment for the **Metropolitano** system running on AWS. This environment includes real-time data processing services, data storage, backend API, data analytics, and CI/CD pipeline.

The goal of this section is to build a complete architecture for operating the Metropolitano train system, including:

+ **Real-time event streaming** — processing passenger count, train status, and ticket transactions  
+ **Event-driven processing** using Amazon SQS and SNS  
+ **RDS** for persistent data storage  
+ **S3** as a data lake for logs, raw data, and analytical datasets  
+ **EventBridge** for orchestrating events across the system  
+ **CloudWatch** for system monitoring  
+ **QuickSight** for data visualization  
+ **CodePipeline + CodeBuild + CodeDeploy** for automated deployment  

---

#### Metropolitano Architecture Overview

The architecture below represents the ticketing system, passenger flow, data streaming, and event processing pipeline:

![Interface endpoint architecture](/images/5-Workshop/5.1-Workshop-overview/aws_metropolitano_train_service.drawio.png)

---

#### Why Prepare the Environment?

The Metropolitano system uses multiple AWS services. Preparing the environment ensures:

+ **Service-to-service connectivity** — API Gateway ⇄ Lambda ⇄ RDS ⇄ S3 ⇄ DynamoDB  
+ **Real-time data pipelines** using Kinesis, SQS, SNS, and EventBridge  
+ **Network infrastructure (VPC)** for internal communication without using public internet  
+ **Analytics readiness** using QuickSight and an S3 Data Lake  
+ **Automated application deployment** using CodePipeline  

---

#### What You Will Configure in This Section

During the Prepare Environment phase, you will create and configure the following:

1. **VPC & Networking**  
   - Subnets, Route Tables, Security Groups  
   - Private & public networking for EC2, RDS, and Lambda  

2. **EC2 Instances**  
   - Simulated backend or worker nodes  
   - IAM Roles for S3 / DynamoDB / Kinesis access  

3. **Amazon RDS**  
   - Primary database for the Metropolitano system  
   - Stores ticketing, user, and historical data  

4. **S3 Data Lake**  
   - raw/ (raw data)  
   - analytics/ (processed/analytical data)  
   - app-assets/ (frontend static assets)  
   
5. **Kinesis Data Stream**  
   - Collects real-time passenger metrics and train status  

6. **SQS + SNS**  
   - Handles transaction queues  
   - Sends delay or incident notifications  

7. **EventBridge**  
   - Routes events between API, Lambda, Payment, and Alerts  

8. **CloudWatch**  
   - Logs, Metrics, Alarms, Dashboards  

9. **QuickSight**  
   - Provides analytical dashboards for system monitoring  

10. **CodeBuild + CodeDeploy + CodePipeline**  
    - Automates backend build → test → deployment  

---

#### After Completing This Section

You will have a fully prepared AWS environment ready to deploy the entire Metropolitano application, ensuring:

- Reliable data storage  
- Real-time event processing  
- Flexible API integration  
- Automated CI/CD pipeline  
- Powerful analytics system  

---
