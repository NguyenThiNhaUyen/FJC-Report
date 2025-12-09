---
title : "Introduction"
date: 2025-09-07 
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---


#### Workshop overview
Here is a proposed overview of how AWS services are used to set up the Metropolitano system, ensuring the criteria of "high availability - secure - scalable":Overview of Metropolitano System Architecture on AWS

The system is designed to manage, monitor and coordinate urban railway operations, using a range of AWS services for each functional layer:

|Layer|AWS Services|Role on the Metropolitano system|
|-----|------------|--------------------------------|
|Network & Security|Route 53|Manage DNS, route user (operator, management) traffic to applications efficiently and reliably.|
|      | CloudFront|Content delivery network (CDN), which helps deliver static content (user interface, reports) with low latency and enhanced security.|
|      |WAF (Web Application Firewall)|Protect web applications from common network attacks, filtering malicious traffic before it reaches the application server.|
|      |Secrets Manager|Securely manage, rotate, and control access to sensitive information such as database (RDS) passwords, API keys, and other credentials. Helps strengthen application security and minimize the risk of information leakage.|
|Data storage & Application|S3(Simple Storage Service)|Store all unstructured and rarely accessed data, such as logs, backups, and project documents|
|      | EC2(Elastic Compute Cloud)|Provides compute resources to run back-end applications, orchestration services, and user interfaces. Can be used in Auto Scaling Groups to ensure high availability.|
|      |RDS (Relational Database Service) - MSSQL|Provides a fully managed relational database running on the Microsoft SQL Server platform. Used to store structured, mission-critical business data (route information, schedules, equipment status).|
|Collect & process|Kinesis|Build event-driven architectures, automate workflows by connecting different AWS services when events occur (e.g., when data reaches an alert threshold)|
|      |EventBridge|Build event-driven architectures, automate workflows by connecting different AWS services when events occur (e.g., when data reaches an alert threshold)|
|      | SQS (Simple Queue Service)|Message queues to decouple system components, handle asynchronous tasks (e.g., sending mass notifications, handling dispatch commands).|
|      |SNS (Simple Notification Service)|Push notification service for important events, used to send alerts to other systems or operators via email/SMS.|
|      | CloudWatch| Monitor resources and applications, collect logs, and set up alarms to ensure system stability and detect problems early.|
|Analyze & Visualization|Quicksight|Business intelligence (BI) services to visualize data from RDS and Kinesis (after it has been processed), helping management monitor system status in real-time and make decisions.|
|Automation develop|CodePipeline|Continuous Integration and Delivery (CI/CD) services to automate the entire process of building, testing, and deploying application source code.|
|      |CodeBuild|The service builds source code, runs automated tests, and generates deployment-ready packages.|
|      |CodeDeploy|The service automates the deployment of source code to EC2 instances or other environments, ensuring smooth, uninterrupted updates.|  

![overview](/images/5-Workshop/5.1-Workshop-overview/aws_metropolitano_train_service.drawio.png)
