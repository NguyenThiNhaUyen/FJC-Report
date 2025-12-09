---
title : "Chuẩn bị cấu hình môi trường"
date: 2025-09-07 
weight : 4 
chapter : false
pre : " <b> 5.4. </b> "
---

#### AWS Metropolitano Workshop

Trong phần này, bạn sẽ chuẩn bị toàn bộ môi trường cho hệ thống **Metropolitano** hoạt động trên AWS. Môi trường này bao gồm các dịch vụ xử lý dữ liệu thời gian thực, lưu trữ dữ liệu, API backend, phân tích dữ liệu và CI/CD pipeline.

Mục tiêu của phần này là xây dựng một kiến trúc hoàn chỉnh cho việc quản lý hệ thống tàu điện Metropolitano, bao gồm:

+ **Dữ liệu thời gian thực (real-time event streaming)** — xử lý lượng hành khách, trạng thái tàu, giao dịch vé  
+ **Hàng đợi xử lý sự kiện** bằng Amazon SQS và SNS  
+ **RDS** để lưu dữ liệu  
+ **S3** làm data lake chứa log, dữ liệu thô, dữ liệu phân tích  
+ **EventBridge** để điều phối sự kiện trong toàn hệ thống  
+ **CloudWatch** để theo dõi hệ thống  
+ **QuickSight** để trực quan hóa dữ liệu  
+ **CodePipeline + CodeBuild + CodeDeploy** để tự động hóa triển khai  

---

#### Kiến trúc tổng quan Metropolitano

Kiến trúc dưới đây mô phỏng toàn bộ hệ thống vé, hành khách, luồng dữ liệu và xử lý sự kiện:

![Interface endpoint architecture](/images/5-Workshop/5.1-Workshop-overview/aws_metropolitano_train_service.drawio.png)

---

#### Tại sao cần thiết lập Environment?

Hệ thống Metropolitano sử dụng nhiều dịch vụ AWS khác nhau. Việc chuẩn bị môi trường được thực hiện để:

+ **Kết nối các dịch vụ lại với nhau** — API Gateway ⇄ Lambda ⇄ RDS ⇄ S3 ⇄ DynamoDB  
+ **Thiết lập pipeline dữ liệu real-time**, bao gồm Kinesis, SQS, SNS, EventBridge  
+ **Tạo cơ sở hạ tầng mạng (VPC)** để các thành phần giao tiếp nội bộ và tránh public internet  
+ **Chuẩn bị phân tích dữ liệu** bằng QuickSight và S3 Data Lake  
+ **Tự động hóa triển khai** bằng CodePipeline  

---

#### Những gì bạn sẽ thực hiện trong phần này

Trong phần Prepare Environment, bạn sẽ lần lượt tạo và cấu hình:

1. **VPC & mạng**  
   - Subnets, Route Tables, Security Groups  
   - Private & Public networking dành cho EC2, RDS và Lambda  

2. **EC2 instances**  
   - Mô phỏng backend hoặc worker xử lý  
   - Gắn IAM Role cho phép truy cập S3 / DynamoDB / Kinesis  

3. **Amazon RDS**  
   - Database chính của hệ thống Metropolitano  
   - Lưu trữ dữ liệu vé, người dùng, lịch sử  

4. **S3 Data Lake**  
   - raw/ (dữ liệu thô)  
   - analytics/ (dữ liệu phân tích)  
   - app-assets/ (tài nguyên frontend)  
  
5. **Kinesis Data Stream**  
   - Thu thập dữ liệu real-time như lượng hành khách, trạng thái tàu  

6. **SQS + SNS**  
   - Xử lý hàng đợi giao dịch  
   - Gửi thông báo delay  

7. **EventBridge**  
   - Router sự kiện giữa API, Lambda, Payment, và Alerts  

8. **CloudWatch**  
    - Log, Metrics, Alarms, Dashboard  

9. **QuickSight**  
    - Dashboard phân tích hoạt động hệ thống  

10. **CodeBuild + CodeDeploy + CodePipeline**  
    - Tự động build → test → deploy backend  

---

#### Sau bước này

Bạn sẽ có một môi trường AWS hoàn chỉnh sẵn sàng triển khai toàn bộ ứng dụng Metropolitano, đảm bảo:

- Lưu trữ dữ liệu ổn định  
- Xử lý sự kiện real-time  
- Tích hợp API linh hoạt  
- Pipeline CI/CD tự động  
- Hệ thống phân tích dữ liệu mạnh mẽ  

---


