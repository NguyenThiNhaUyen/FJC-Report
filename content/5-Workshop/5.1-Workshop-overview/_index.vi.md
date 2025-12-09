---
title : "Giới thiệu"
date: 2025-09-07 
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---

#### Tổng quan workshop
Dưới đây là cái nhìn tổng quan về cách các dịch vụ AWS được sử dụng để thiết lập hệ thống Metropolitano, đảm bảo các tiêu chí **"high availability – secure – scalable"** (khả dụng cao – bảo mật – mở rộng tốt):

### Tổng quan kiến trúc hệ thống Metropolitano trên AWS

Hệ thống được thiết kế để quản lý, giám sát và điều phối hoạt động đường sắt đô thị, sử dụng nhiều dịch vụ AWS cho từng lớp chức năng:

|Lớp|Dịch vụ AWS|Vai trò trong hệ thống Metropolitano|
|-----|------------|--------------------------------|
|**Mạng & Bảo mật**|Route 53|Quản lý DNS, định tuyến lưu lượng người dùng (operator, quản lý) tới các ứng dụng một cách hiệu quả và ổn định.|
|      | CloudFront|Mạng phân phối nội dung (CDN), giúp phân phối nội dung tĩnh (giao diện người dùng, báo cáo) với độ trễ thấp và tăng cường bảo mật.|
|      |WAF (Web Application Firewall)|Bảo vệ ứng dụng web khỏi các cuộc tấn công phổ biến, lọc lưu lượng độc hại trước khi vào máy chủ ứng dụng.|
|      |Secrets Manager|Quản lý an toàn thông tin nhạy cảm như mật khẩu database (RDS), API Key và các credentials khác. Giúp tăng cường bảo mật và giảm rủi ro rò rỉ thông tin.|
|**Lưu trữ & Ứng dụng**|S3 (Simple Storage Service)|Lưu trữ dữ liệu phi cấu trúc hoặc ít truy cập, như log, backup, tài liệu dự án.|
|      | EC2 (Elastic Compute Cloud)|Cung cấp tài nguyên tính toán để chạy backend, các dịch vụ vận hành và giao diện người dùng. Có thể chạy trong Auto Scaling Groups để đảm bảo High Availability.|
|      | RDS (MSSQL)|Cơ sở dữ liệu quan hệ được quản lý hoàn toàn, dùng để lưu thông tin quan trọng (route, lịch trình, trạng thái thiết bị).|
|**Thu thập & Xử lý**|Kinesis|Thu thập và xử lý dữ liệu realtime, giúp xây dựng hệ thống phân tích theo thời gian thực.|
|      |EventBridge|Xây dựng kiến trúc hướng sự kiện, tự động hóa workflow khi có sự kiện xảy ra (ví dụ: cảnh báo vượt ngưỡng).|
|      |SQS (Simple Queue Service)|Hàng đợi tin nhắn, tách biệt các thành phần hệ thống, xử lý tác vụ async (như gửi thông báo hàng loạt).|
|      |SNS (Simple Notification Service)|Gửi thông báo quan trọng qua Email/SMS, tích hợp với các hệ thống khác.|
|      |CloudWatch|Giám sát tài nguyên và ứng dụng, thu thập log, tạo cảnh báo sớm để đảm bảo hệ thống ổn định.|
|**Phân tích & Trực quan hóa**|QuickSight|Dịch vụ BI trực quan hóa dữ liệu từ RDS và Kinesis, hỗ trợ quản lý theo dõi hệ thống realtime và ra quyết định.|
|**Tự động hóa phát triển (DevOps)**|CodePipeline|CI/CD tự động build – test – deploy mã nguồn.|
|      |CodeBuild|Build source, chạy test tự động và tạo artifact sẵn sàng deploy.|
|      |CodeDeploy|Triển khai ứng dụng lên EC2 hoặc môi trường khác mà không gián đoạn hoạt động.|
  
![overview](/images/5-Workshop/5.1-Workshop-overview/aws_metropolitano_train_service.drawio.png)
