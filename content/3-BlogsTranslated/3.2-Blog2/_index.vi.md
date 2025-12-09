---
title: "Blog 2"
# date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---
---
## Tận dụng sức mạnh của Amazon Q Business kết hợp với Microsoft SharePoint để tìm kiếm doanh nghiệp

## Giới thiệu

Trong bối cảnh kinh doanh hiện nay, các tổ chức đang tìm kiếm những phương pháp nhằm khai thác triệt để giá trị và thông tin từ khối lượng dữ liệu ngày càng gia tăng của mình. Các tổ chức phụ thuộc đáng kể vào hạ tầng máy chủ tệp để lưu trữ, quản lý và chia sẻ dữ liệu quan trọng. Khối lượng và mức độ phức tạp của dữ liệu tạo ra nhiều thách thức khi cố gắng tối đa hóa giá trị của chúng. Do đó, các công ty cần một chiến lược mới để vượt qua những trở ngại này.

Amazon Q Business tận dụng sức mạnh của Trí tuệ nhân tạo (Generative AI) để giải quyết các thách thức liên quan đến dữ liệu. Dịch vụ này hỗ trợ các tổ chức ứng dụng AI sinh nhằm nâng cao chất lượng ra quyết định và đạt được các mục tiêu kinh doanh. Amazon Q Business tích hợp với các nguồn dữ liệu hiện có để khám phá thông tin giá trị tiềm ẩn.

Thông qua GenAI, Amazon Q Business hỗ trợ người dùng nhanh chóng phân tích dữ liệu, nhận diện các mẫu và xu hướng, đồng thời tạo ra các khuyến nghị được cá nhân hóa, phù hợp với nhu cầu kinh doanh đặc thù của từng tổ chức. Cho dù mục tiêu là cải thiện dịch vụ khách hàng, tối ưu hóa hiệu quả vận hành hay khám phá các cơ hội doanh thu mới, Amazon Q Business cùng với GenAI sẽ thúc đẩy đổi mới và tăng trưởng.

Trong bài viết này, chúng tôi sẽ trình bày cách Amazon Q Business tích hợp với Microsoft SharePoint Server nhằm khai thác toàn bộ tiềm năng của các tệp dữ liệu của bạn. Bạn sẽ được hướng dẫn cách truy vấn dữ liệu trên Microsoft SharePoint Server bằng ngôn ngữ tự nhiên, tìm kiếm thông tin liên quan, trích xuất các điểm chính và rút ra những insight giá trị.

---

## Tổng quan giải pháp


Như minh họa, việc đảm bảo tính bảo mật, toàn vẹn và khả dụng của dữ liệu là ưu tiên hàng đầu. Bộ kết nối Amazon Q Business dành cho SharePoint áp dụng một khung bảo mật vững chắc, tôn trọng các danh tính người dùng, vai trò và quyền hạn hiện có. Điều này được thực hiện thông qua cơ chế quét danh tính và danh sách kiểm soát truy cập (ACLs), sử dụng thông tin xác thực an toàn được quản lý bởi AWS Secrets Manager. Giải pháp tuân thủ nguyên tắc “quyền hạn tối thiểu”, chỉ cho phép người dùng truy cập dữ liệu mà họ được cấp quyền rõ ràng.

Trong môi trường Microsoft, thông tin người dùng và nhóm thường được lưu trữ trong Active Directory. Amazon Q Business đồng bộ thông tin này vào AWS IAM Identity Center, cho phép thực thi cơ chế kiểm soát truy cập chi tiết và cung cấp phản hồi đã được lọc theo quyền hạn của người dùng.

Bộ kết nối dữ liệu SharePoint Server thu nhận nội dung tài liệu cùng với quyền truy cập NTFS và SharePoint, đảm bảo tuân thủ chính sách kiểm soát truy cập dữ liệu. Khi người dùng gửi truy vấn, giải pháp tạo phản hồi đã lọc, bảo vệ dữ liệu nhạy cảm.

> **Lưu ý:** Giải pháp này thiết kế cho SharePoint Server, không áp dụng cho SharePoint Online.

---

## Điều kiện tiên quyết

Để thử nghiệm giải pháp, cần có:

- Một môi trường SharePoint Server triển khai trên Amazon EC2.
- AWS IAM Identity Center, với vai trò và người dùng có quyền quản lý tài nguyên Q Business.
- Kích hoạt Amazon Q Business.
- AWS Directory Service for Microsoft Active Directory cho SharePoint Server domain joined.
- Phiên bản EC2 chạy Windows, cài RSAT để quản lý người dùng.
- AWS Secrets Manager để lưu thông tin xác thực SharePoint.
- Tùy chọn tích hợp self-managed AD vẫn khả thi.

---

## Hướng dẫn từng bước

### 1. Cấu hình ứng dụng Amazon Q Business

1. Đăng nhập vào bảng điều khiển Amazon Q Business.
2. Chọn **Create application**, nhập tên ứng dụng và chọn **Web experience**.
3. Chọn **IAM Identity Center** cho quản lý truy cập.
4. Sử dụng các thiết lập mặc định cho Application service access, bao gồm tạo Service-Linked Role (SLR) và mã hóa bằng AWS KMS.

### 2. Kết nối với SharePoint

1. Truy cập **Data sources** → **Add index** để tạo một chỉ mục.
2. Chọn **Enterprise index** với số lượng units phù hợp.
3. Thêm data source SharePoint Server:
   - Chọn phiên bản SharePoint, nhập URL, domain và SSL certificate S3 path.
   - Cấu hình **Authorization** (ACLs), **Authentication** (NTLM/Kerberos), và AWS Secrets Manager secret.
   - Tạo IAM role riêng cho dữ liệu nguồn.
   - Chọn **Full Sync mode** và lịch **Daily Sync**.
4. Chọn **Sync now** để bắt đầu lập chỉ mục.

### 3. URL Web của ứng dụng

Người dùng truy cập URL để tương tác với Amazon Q Business thông qua trình duyệt.

---

## Kiểm thử giải pháp

Ví dụ: Bộ phận Nhân sự muốn xác định ứng viên phù hợp cho vị trí **Cyber Security Strategist**.  

- HR Admin truy vấn bằng NLP sẽ nhận kết quả đầy đủ.  
- IT Admin (SP_Gary) không nhận được dữ liệu do quyền truy cập hạn chế.

> Giải pháp minh họa cơ chế phân quyền tối thiểu và kiểm soát ACLs, bảo vệ dữ liệu nhạy cảm.

---

## Dọn dẹp tài nguyên

- Xóa ứng dụng Q Business, Data source, AWS Managed AD, EC2 management server, Secrets, v.v. để tránh phí phát sinh.

---

## Kết luận

Bài viết trình bày cách cấu hình trình kết nối SharePoint cho Amazon Q Business dựa trên nguyên tắc phân quyền tối thiểu. Giải pháp giúp nhân viên truy xuất tri thức và dữ liệu an toàn bằng ngôn ngữ tự nhiên, tăng năng suất, khả năng ra quyết định và chia sẻ tri thức.

Bạn có thể tích hợp nhiều trình kết nối khác với Amazon Q Business dựa trên cùng khái niệm này.

---

## Tác giả

**Mangesh Budkule** – Senior Specialist Solution Architect tại AWS, chuyên về Generative AI và di chuyển workloads Microsoft lên AWS.  

**Jarod Oliver** – Application Modernization Specialist Solutions Architect tại AWS, tập trung vào containers và GenAI.  

**Siavash Irani** – Principal Solutions Architect tại AWS, chuyên về Microsoft workloads, trước đây từng hỗ trợ AWS Support và phát triển EC2Rescue cho Windows.
