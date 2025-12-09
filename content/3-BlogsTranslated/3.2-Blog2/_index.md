---
title: "Blog 2"
# date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---
---
## Harness Amazon Q Business power with Microsoft SharePoint for enterprise search

## Introduction

In today’s business landscape, organizations are looking for ways to extract insights from their growing data assets. Organizations rely heavily on their file server infrastructure to store, manage, and share mission-critical data. The volume and complexity of data creates challenges when working to maximize its value. Companies need a new strategy to overcome these obstacles.

Amazon Q Business leverages Generative AI (GenAI) to address these data challenges. This service helps organizations use generative AI to improve decision-making and achieve business goals. Amazon Q Business integrates with existing data sources to reveal valuable insights.

Using GenAI, Amazon Q Business assists you in quickly analyzing your data, identifying patterns, and generating personalized recommendations tailored to your unique business needs. Whether you’re looking to improve customer service, optimize operational efficiency, or uncover new revenue opportunities, Amazon Q Business and GenAI will aid in driving innovation and growth.

In this blog post, we’ll demonstrate how Amazon Q Business integrates with Microsoft SharePoint Server to unlock the full potential of your files. You will learn how to query Microsoft SharePoint server data using natural language, find relevant information, extract key points, and derive valuable insights.

---

## Solution Overview

Ensuring the confidentiality, integrity, and availability of data is of utmost importance. The Amazon Q Business connector for SharePoint employs a robust security framework that honors existing user identities, roles, and permissions. Identity crawling and access control lists (ACLs) are implemented on the connector using secure credentials managed by AWS Secrets Manager. The solution enforces the principle of least privilege, allowing users access only to the data for which they have explicit permissions.

In environments using Microsoft products, user and group objects are often stored in Microsoft Active Directory. Q Business synchronizes this information into AWS IAM Identity Center, enabling fine-grained access control and filtered query responses according to user permissions.

The SharePoint Server data connector ingests document content and NTFS/SharePoint permissions to provide a complete understanding of data access. When a user submits a query, the solution generates filtered responses that enforce permissions, ensuring sensitive data is accessed only by authorized individuals.

> **Note:** This solution is for SharePoint Server and not applicable to SharePoint Online.

---

## Prerequisites

- A working SharePoint environment deployed on Amazon EC2. [How to deploy SharePoint server on Amazon EC2](#)
- AWS IAM Identity Center configured, along with an IAM role and user with permissions to manage Q Business resources.
- Onboard Amazon Q Business.
- AWS Directory Service for Microsoft Active Directory domain-joined SharePoint Server.
- One Amazon EC2 Windows instance with Remote Server Administrative Tools (RSAT) for managing AWS Managed AD users.
- AWS Managed AD as source of truth for AWS Identity Center.
- Secrets in AWS Secrets Manager with access to SharePoint Server credentials.

> Self-managed AD is also a viable alternative integration option.

---

## Walkthrough

### Configure Amazon Q Business Application

1. Sign in to the Amazon Q Business console.
2. Select **Create application**.
3. Enter application name and select **Web experience**.
4. Choose **IAM Identity Center** for Access management.
5. Configure Application service access:
   - Create and use a new service-linked role (SLR).
   - Leave encryption default (AWS KMS key).
6. Choose **Create and open web experience** to deploy application.

### Connect to SharePoint

1. Navigate to **Data sources** → **Add index**.
2. Create new index:
   - Choose **Enterprise index** and number of units (e.g., 50).
   - Amazon Q Business charges by document capacity.
3. Add SharePoint data source:
   - Select SharePoint Server version.
   - Enter site URL, domain, and SSL certificate S3 path.
   - Configure Authorization (ACLs) and Authentication (NTLM/Kerberos).
   - Create or choose AWS Secrets Manager secret for credentials.
   - Create IAM role for data source.
   - Set Sync scope, mode (Full Sync), and schedule (Daily).
4. Click **Sync now** to crawl and index data.

### Access Application

Users access the Amazon Q Business application via its Web URL.

---

## Test the Solution

**Scenario:** HR reviews resumes to select a “Cyber Security Strategist.”

- HR Admin queries using NLP in Amazon Q AI assistant → receives full results.
- IT Admin (SP_Gary) queries → receives no results due to restricted permissions.

> Demonstrates principle of least privilege and ACL enforcement.

---

## Cleanup

- Delete Q Business application, Data source, AWS Managed AD, EC2 management server, Secrets, etc., to avoid charges.

---

## Conclusion

You learned how to configure the SharePoint connector for Amazon Q Business using least privilege principles. Employees can securely interact with organizational knowledge in SharePoint using natural language, improving productivity, decision-making, and knowledge sharing. Multiple data connectors can be integrated similarly for other use cases.

AWS provides more services and features than any other cloud provider, enabling faster, easier, and cost-effective migration and modernization.

---

## AWS Gen AI Services

- Amazon Q – Generative AI Assistance
- Amazon Q Business
- Amazon Q Developer
- Supported data connector
- Amazon Q Business with SharePoint Online
- Amazon Bedrock

---

## Authors

**Mangesh Budkule** – Senior Specialist Solution Architect at AWS, expert in GenAI and Microsoft workloads.  

**Jarod Oliver** – Specialist Solutions Architect, focuses on containers and GenAI.  

**Siavash Irani** – Principal Solutions Architect at AWS, specializes in Microsoft workloads and EC2Rescue for Windows.
