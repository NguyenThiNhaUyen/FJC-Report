---
title: "Proposal"
# date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# AWS First Cloud AI Journey – Metropolitano Railways

**Project Team:** FPT HCM University  
**Client:** Metropolitano Railway Systems  
**Document Date:** 12/09/2025  

---

## TABLE OF CONTENTS
1. [BACKGROUND AND MOTIVATION](#1-background-and-motivation)  
   1.1 [EXECUTIVE SUMMARY](#11-executive-summary)  
   1.2 [PROJECT SUCCESS CRITERIA](#12-project-success-criteria)  
   1.3 [ASSUMPTIONS](#13-assumptions)  
2. [SOLUTION ARCHITECTURE / ARCHITECTURAL DIAGRAM](#2-solution-architecture--architectural-diagram)  
   2.1 [TECHNICAL ARCHITECTURE DIAGRAM](#21-technical-architecture-diagram)  
   2.2 [TECHNICAL PLAN](#22-technical-plan)  
   2.3 [PROJECT PLAN](#23-project-plan)  
   2.4 [SECURITY CONSIDERATIONS](#24-security-considerations)  
3. [ACTIVITIES AND DELIVERABLES](#3-activities-and-deliverables)  
   3.1 [ACTIVITIES AND DELIVERABLES](#31-activities-and-deliverables)  
   3.2 [OUT OF SCOPE](#32-out-of-scope)  
   3.3 [PATH TO PRODUCTION](#33-path-to-production)  
4. [EXPECTED AWS COST BREAKDOWN BY SERVICES](#4-expected-aws-cost-breakdown-by-services)  
5. [TEAM](#5-team)  
6. [RESOURCES & COST ESTIMATES](#6-resources--cost-estimates)  
7. [ACCEPTANCE](#7-acceptance)  

---

## 1. BACKGROUND AND MOTIVATION

### 1.1 EXECUTIVE SUMMARY

**Client background:**  
Rapid urbanization is increasing pressure on public transportation systems. Metropolitano Railway Systems is modernizing operations to improve **reliability**, **passenger experience**, and **operational efficiency**. Current on-premises systems face challenges in **scalability**, **high availability**, and **real-time data processing**.

**Business & Technical Objectives:**
- Ensure **high availability** and **24/7** operations for mission-critical rail services.
- Enable **elastic scalability** to handle peak passenger loads and seasonal demand.
- Improve **system reliability** and **disaster recovery** capabilities.
- Support real-time data processing for **ticketing and payment transaction monitoring**, including payment status tracking and reconciliation.
- Strengthen **security & compliance** aligned with public-sector transportation standards.
- Reduce operational costs through **cloud-native automation**.
- Accelerate innovation cycles for digital services such as **mobile ticketing** and **predictive maintenance**.

**Use cases (POC scope):**
- Digital ticketing & fare collection (web/app booking, QR/IC card integration).
- Train scheduling & dispatch management *(contextual use case; not the primary focus of this POC)*.
- Real-time payment/transaction event analytics through **Amazon Kinesis** (e.g., payment status updates, settlement events, anomaly detection).
- BI dashboards for management decision-making using **Amazon QuickSight**.
- Incident response and alerting through centralized monitoring and logging.

**Summary of Professional Services (Project Team):**
- Design an AWS architecture that is **secure, scalable, and fault-tolerant** based on **Amazon EC2** and managed services.
- Migrate selected workloads from on-premises infrastructure to AWS (POC scope).
- Implement real-time payment/ticketing event pipelines using **Amazon Kinesis**, with storage and analytics datasets delivered to **Amazon S3**.
- Deploy analytics using **QuickSight**.
- Set up CI/CD using **CodePipeline, CodeBuild, and CodeDeploy**.
- Provide knowledge transfer/training to ensure operational readiness for the Client’s technical team.

---

### 1.2 PROJECT SUCCESS CRITERIA

**Service Reliability & Availability**
- The deployed system must achieve **≥ 99.9% uptime** across all mission-critical services in scope (ticketing, payment monitoring, analytics).

**Scalability & Performance**
- The platform must automatically scale to handle peak passenger traffic without performance degradation.
- End-to-end response time for booking and payment APIs must remain under **300 ms** during peak load tests *(target)*.

**Real-Time Data Processing**
- Real-time payment and ticketing transaction events must be processed with a latency under **5 seconds** using **Amazon Kinesis**.
- Data ingestion pipelines must support at least **10,000 events/second** with auto-scaling *(target)*.

**Analytics & Insights Delivery**
- Management dashboards (QuickSight) must provide accurate, refreshed datasets within **≤ 5 minutes** of data arrival in S3.

**CI/CD & Operational Excellence**
- All application deployments must be executed via automated CI/CD pipelines with rollback capability.

**Cost Efficiency**
- AWS cost optimization mechanisms (Auto Scaling, RI/Savings Plans, lifecycle policies) target a reduction of at least **20%** compared to on-premises operations.
- Cost Explorer and Billing Alarms must be configured to prevent overruns.

---

### 1.3 ASSUMPTIONS

**Prerequisites & Dependencies**
- The Client provides timely access to required environments, systems, and personnel.
- The Client supplies necessary credentials, API documentation, and integration endpoints for on-premises/third-party systems.
- Existing operational data (ticketing, scheduling, payment transactions) is available and accessible for migration and integration.
- Third-party vendors (payment gateways, fare systems, transit card systems) offer stable APIs and complete documentation.
- Required AWS accounts/organizations/billing structures are set up prior to project start.
- The Client identifies Subject Matter Experts (SMEs) for each domain (operations, IT, ticketing, scheduling).

**Technical Constraints**
- Some legacy systems may remain on-premises, requiring hybrid connectivity via VPN or Direct Connect.
- Existing applications may have non-cloud-optimized architectures, limiting modernization within the POC scope.
- Legacy data quality issues may affect migration accuracy and analytics output.
- Operational networks and payment/ticketing systems must support secure and reliable cloud connectivity.
- Real-time analytics performance depends on ingestion latency and stability from payment gateways and ticketing transaction sources.

**Business Constraints**
- Project timelines may depend on internal Client approvals/procurement processes.
- The Client’s organizational readiness and staff availability may impact progress.
- Budget limitations may restrict scope.
- Certain regulatory/compliance requirements may limit data residency/retention options.

**Risks (high-level)**
- Integration risk (legacy undocumented behaviors).
- Data migration risk (inconsistent/incomplete legacy data).
- Operational risk (hybrid/on-prem hardware failures).
- Security risk (misconfigured third-party endpoints).
- Timeline & dependency risk (vendor approvals, API throughput).

---

## 2. SOLUTION ARCHITECTURE / ARCHITECTURAL DIAGRAM

### 2.1 TECHNICAL ARCHITECTURE DIAGRAM

**Figure 1. AWS Technical Architecture Diagram – Metropolitano Railways (POC)**

<img src="/images/2-Proposal/2.1.png" alt="Figure 1 - Technical Architecture Diagram" />

**Figure 1 (POC Outcome & Architecture Summary):**  
The POC enables near real-time monitoring and reconciliation of ticketing/payment transactions. Payment status events are streamed into **Amazon Kinesis** and delivered to **Amazon S3 (data lake)** for analytics. **Amazon QuickSight** dashboards provide revenue insights (daily/monthly) and highlight unpaid/incomplete payments for follow-up. User traffic is routed via **Route 53 → CloudFront → AWS WAF** to the application on **Amazon EC2**, with transactional data stored in **Amazon RDS (private subnet)**. **Amazon CloudWatch** supports monitoring and alerting. Asynchronous workflows are decoupled using **SNS/SQS** (and **EventBridge** where needed). Source control uses **GitLab**; CI/CD is implemented using **CodePipeline/CodeBuild/CodeDeploy**.

**Production note (recommendations for production readiness):**
- Enable **Multi-AZ** and place workloads in **private subnets** behind an **ALB**.
- Restrict egress using **NAT Gateway** and/or **VPC Endpoints**.
- Enforce **TLS (ACM)**, least-privilege IAM, and store secrets in **Secrets Manager/SSM**.
- Enable **CloudTrail/Config** and strengthen logging (WAF/CloudFront logs).
- Use **blue/green** or **canary** deployments with rollback.

---

### 2.2 TECHNICAL PLAN

**Key activities include:**
- Infrastructure provisioning using IaC templates to deploy:
  - **Amazon EC2** clusters for application workloads
  - **Amazon RDS** for database services
  - **Amazon S3** for static content and data lake storage
  - **Amazon Route 53** for DNS routing
  - **Amazon CloudFront** and **AWS WAF** for secure global content delivery
  - **Amazon Kinesis** for real-time data ingestion
  - **Amazon SQS/SNS** for asynchronous messaging
  - **Amazon EventBridge** for event-driven workflows
  - **Amazon CloudWatch** for logging, monitoring, alarms, and dashboards
  - **Amazon QuickSight** for analytics and BI reporting
- This POC does **not** include AWS Lambda; streaming consumers and asynchronous/background processing are implemented on EC2-based services (or containers) and integrated via SNS/SQS/EventBridge where applicable.
- Application build and release processes will be automated using **CodePipeline, CodeBuild, and CodeDeploy**, enabling blue/green or rolling deployments with automated rollback.
- Configuration items requiring approvals (production DNS changes, WAF rule modifications, RDS parameter adjustments, security group updates) will follow the Client’s Change Management Process (including CAB approvals and tracked deployment records).
- Critical paths (ticketing workflows, payment integrations, real-time ingestion, analytics refresh flows) will undergo unit, integration, load, and failover testing. Test scenarios, acceptance criteria, and validation procedures are provided in Appendix X.

---

### 2.3 PROJECT PLAN

**Stakeholder Participation:**
- Client stakeholders (Operations, IT, Data, Security teams) are required for:
  - Sprint Reviews (demo and feedback)
  - Sprint Retrospectives (continuous improvement)
  - UAT and sign-off sessions
  - Technical design workshops

**Team Responsibilities (high-level):**
- Cloud Architect – AWS solution design, security architecture, scalability & HA patterns
- DevOps Engineer – CI/CD pipelines, IaC, automated deployments
- Application Engineer – application refactoring and integration
- Client IT Lead – access provisioning, governance alignment
- Client Operations Team – process validation, UAT testing
- Client Security Officer – compliance and security controls review
- Data Engineer – Kinesis pipelines, SQS/SNS integration, data modeling
- Analytics/BI Engineer – QuickSight dashboards and dataset automation

**Communication Cadence:**
- Daily: standups with Project Team
- Weekly: project status updates to Client stakeholders
- Bi-weekly: sprint review + sprint planning
- Monthly: steering committee meeting
- Ad-hoc: incident response, change approvals, design deep-dives

**Knowledge Transfer:**
- AWS architecture overview
- CI/CD pipeline management
- Monitoring and incident response using CloudWatch
- Data pipeline operations (Kinesis → S3 → QuickSight)
- Infrastructure lifecycle & IaC updates
- Security and IAM operations

---

### 2.4 SECURITY CONSIDERATIONS

**Access Security**
- Implement least-privilege IAM, role-based access control, and best-practice IAM policies.
- Enable MFA for privileged accounts.
- Restrict CI/CD access using scoped IAM roles.
- Apply change approvals for Route 53 DNS, CloudFront updates, and WAF rule modifications.

**Infrastructure Security**
- Deploy Amazon EC2 in private subnets within a Multi-AZ VPC architecture (production target).
- Use AWS WAF to protect CloudFront distributions and application endpoints against common exploits.
- Enforce traffic boundaries via Security Groups and NACLs.
- Run Amazon RDS in Multi-AZ with encrypted storage and automated backups.
- Use EventBridge for security-trigger automation (e.g., config rule violations), where applicable.

**Data Security**
- Encrypt data at rest in S3, RDS, Kinesis, and SQS using AWS KMS CMKs.
- Protect data in transit using TLS 1.2+ across all services.
- Configure S3 lifecycle policies and object versioning for retention compliance.
- Data lake structure follows separation of raw, processed, curated layers.

**Detection & Monitoring**
- Enable AWS CloudTrail and AWS Config for full API auditing and compliance tracking.
- CloudWatch collects logs, metrics, application traces, alarms, and dashboards.
- Deliver WAF logs and CloudFront access logs to S3 for security analytics.

**Incident Management**
- Design incident response playbooks for system failures, security breaches, data exposure, and pipeline errors.
- Trigger automated alerts using SNS/EventBridge to the Client’s operations team.
- Implement disaster recovery procedures for critical EC2 and RDS workloads.
- Ensure backup snapshots comply with Client-defined retention policies.
- Route operational alarms to the correct on-call teams for faster MTTR.

---

## 3. ACTIVITIES AND DELIVERABLES

### 3.1 ACTIVITIES AND DELIVERABLES

> **Timeline basis:** Internship duration **08/09/2025 – 22/11/2025** (~11 weeks)

| Project Phase | Timeline | Activities | Deliverables / Milestones | Total man-day |
|---|---|---|---|---|
| Assessment | Week 1–2 (08/09–21/09) | Requirements workshops (business/technical/security); current-state analysis; identify integration points (payments, ticketing, analytics); environment readiness validation | Assessment report; Architecture blueprint v1; Backlog & sprint plan | TBD |
| Setup base infrastructure | Week 3–4 (22/09–05/10) | VPC/subnets/routing/SGs; IAM baseline; S3 data lake foundation; CloudFront + WAF + Route53; RDS setup; CloudWatch | Infrastructure provisioned; IaC templates delivered; Networking & security baseline | TBD |
| Setup component 1 | Week 5–6 (06/10–19/10) | EC2 Auto Scaling setup; deploy application backend; configure CI/CD (CodePipeline/CodeBuild/CodeDeploy); observability dashboards | Application deployed; CI/CD pipelines operational; Monitoring dashboard | TBD |
| Setup component 2 | Week 7–8 (20/10–02/11) | Kinesis streaming pipelines; SQS/SNS messaging; ETL to S3 data lake; QuickSight dashboards; EventBridge workflows | Data pipeline operational; Event-driven architecture; Revenue dashboards (daily/monthly); Unpaid/incomplete payments report (with alert thresholds) | TBD |
| Testing & Go-live (POC) | Week 9–10 (03/11–16/11) | Unit/integration/load testing; go-live readiness review; (POC) DNS cutover; monitoring & rozllback plans | UAT sign-off; Go-live checklist; POC launch | TBD |
| Handover | Week 11 (17/11–22/11) | Final documentation; operations training; knowledge transfer sessions; transition to BAU | Runbooks & SOPs; Admin training completion; Final acceptance | TBD |

**How to calculate man-day (guideline):**
- **Man-day = number of people × number of working days actually spent on the phase.**  
- Example: 5 people work 3 days on “Assessment” → **5 × 3 = 15 man-days**.  
- If you track by hours: **1 man-day ≈ 8 working hours** (common convention).  
  - Example: total 120 hours → **120 / 8 = 15 man-days**.

---

### 3.2 OUT OF SCOPE
- Custom application code development or feature enhancements not listed in the Scope of Work.
- On-premises infrastructure upgrades, network redesign, or hardware procurement.
- Performance tuning of third-party vendor systems.
- Mobile app development or UI/UX redesign outside the scope.
- Machine Learning model development beyond QuickSight & basic SageMaker patterns.
- Penetration testing or third-party security audits (unless contracted separately).
- Post go-live 24/7 operations unless contracted separately.
- Support for legacy networks or non-cloud-compatible components.
- Migration of data sources not included in the initial assessment.
- AWS Lambda–based serverless compute (event consumers/functions) is out of scope for this POC.

---

### 3.3 PATH TO PRODUCTION
The Proof of Concept (POC) environment will demonstrate selected use cases defined in Section 2.2.  
The POC environment will not contain all production-grade capabilities.

**Key gaps requiring enhancements before production deployment include:**
- Full resilience design (multi-AZ, failover, scaling policies).
- Complete observability coverage (CloudWatch metrics, distributed tracing, WAF logs).
- Hardened security baselines (advanced IAM controls, WAF tuning, encryption policies).
- Comprehensive testing (integration, performance, DR simulation).
- CI/CD hardening and automated rollback.
- Production-approved DNS, WAF, and network change processes.
- Enhanced error handling and exception flows for edge cases.

---

## 4. EXPECTED AWS COST BREAKDOWN BY SERVICES

**Costing considerations (high-level):**
- EC2 estimated using a mix of On-Demand + Reserved Instances/Savings Plans (production).
- Multi-AZ RDS with automated backups.
- CloudFront cost includes WAF usage (rule groups + request filtering).
- S3 includes storage tiers (Standard, Intelligent-Tiering) and lifecycle policies.
- Log storage and metrics in CloudWatch.
- Kinesis ingest & processing units.
- Data transfer cost between components.
- QuickSight Reader & Author licenses.
- Messaging (SQS, SNS, EventBridge) based on estimated throughput.
- Route 53 DNS queries + health checks.

**Assumptions (for accurate pricing):**
- Daily ingestion volume: X GB/day via Kinesis.
- EC2 sizing based on projected user load for ticketing and payment workloads.
- S3 storage baseline calculated for 12 months retention.
- Moderate WAF rule usage and CloudFront regional edge charges.
- SQS/SNS/EventBridge traffic estimated from projected workflow processes.
- QuickSight usage includes 1 Author and X Readers.

---

## 5. TEAM

| Name | Title | Description | Email / Contact Info |
|---|---|---|---|
| Tào Bảo Thành | Team Leader | Manage the project, configure AWS services, code Backend, write documents, design architecture | taobaothanh365@gmail.com |
| Nguyễn Thị Nhã Uyên | Member | Design the UI and code Frontend | uyenntnse183774@fpt.edu.vn |
| Nguyễn Bảo Khánh | Member | Design the UI and code Frontend | baokhanhtcv2005@gmail.com |
| Trần Văn Quyết | Member | Code Backend | quyettvse181574@fpt.edu.vn |
| Nguyễn Văn Cường | Member | Code Backend | cuongnvse183645@fpt.edu.vn |

---
