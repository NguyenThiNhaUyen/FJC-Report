---
title : "Dọn dẹp tài nguyên"
date: 2025-09-07
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---
# Dọn dẹp tài nguyên (Clean Up)

---

# 1. Dọn dẹp Networking & VPC

## 1.1 Xóa Hosted Zone & Resolver Rules trong Route 53

1. Vào **Route 53 → Hosted Zones**  
   Xóa Private Hosted Zone: `s3.us-east-1.amazonaws.com`.

2. Vào **Route 53 Resolver → Rules**  
   Hủy liên kết rule `myS3Rule` khỏi **VPC Onprem**, sau đó xóa rule này.

---

## 1.2 Xóa các tài nguyên trong VPC

**Thực hiện theo thứ tự sau:**

1. EC2 → **Network Interfaces (ENI)**  
   - Xóa các ENI còn lại của VPC endpoints, resolver endpoints, hoặc EC2 sau khi terminate.

2. Vào **VPC console** và xóa lần lượt:
   - Security Groups
   - Subnets
   - Route Tables
   - Internet Gateway (detach trước khi xóa)
   - NAT Gateway (nếu có)
   - Cuối cùng, xóa **VPC** `metropolitano`

---

# 2. Dọn dẹp EC2

1. EC2 → Instances  
   - Chọn instance `metropolitano-version-1` → **Terminate**

2. Xóa thêm:
   - Elastic IP (nếu đã allocate)
   - Key Pair `myKey.pm`
   - Security group `public-web-sg`

---

# 3. Dọn dẹp RDS

1. RDS → Databases → Chọn database SQL Server → **Delete**.
2. Tắt "final snapshot" nếu không cần.
3. Xóa **DB Subnet Group**: `private-db-metropolitano`
4. Xóa Security Group `private-db-sg` (nếu không còn sử dụng).

---

# 4. Dọn dẹp S3

1. Vào **S3 Console**  
2. Chọn bucket `metropolitano-2025`
3. Empty → Delete bucket

---

# 5. Dọn dẹp CloudFront

1. CloudFront → Distributions  
2. Chọn distribution → **Disable**
3. Chờ trạng thái “Disabled”
4. Nhấn **Delete**

---

# 6. Dọn dẹp Kinesis Data Stream

1. Vào **Kinesis → Data Streams**  
2. Chọn `metropolitano-stream`  
3. Nhấn **Delete**

---

# 7. Dọn dẹp EventBridge

1. EventBridge → Rules  
2. Chọn rule `metropolitano-event-rule`  
3. Nhấn **Delete**

Nếu bạn tạo thêm event bus hoặc target, hãy xóa luôn.

---

# 8. Dọn dẹp SQS

1. SQS Console → Queues  
2. Chọn `metropolitano-queue`  
3. Nhấn **Delete**

---

# 9. Dọn dẹp SNS

1. SNS Console → Topics  
2. Chọn `metropolitano-alerts`  
3. Xóa các subscription (email/SMS)  
4. Xóa topic

---

# 10. Dọn dẹp CloudWatch

## 10.1 Xóa Alarms
- CloudWatch → Alarms → Chọn alarm → Delete

## 10.2 Xóa Log Groups
- CloudWatch → Log Groups → Delete:
  - Log của EC2
  - Log của Lambda (nếu có)
  - VPC Flow Logs (nếu có)

## 10.3 Metrics  
- Metrics sẽ tự hết hạn → không cần xóa.

---

# 11. Dọn dẹp QuickSight

Nếu bạn bật QuickSight, có thể tốn phí hằng tháng.

**Thực hiện:**

1. Vào **QuickSight → Manage QuickSight**
2. Xóa:
   - Dataset
   - SPICE dataset
   - Dashboard
   - Analysis

3. Nếu không dùng nữa → **Unsubscribe QuickSight account**
