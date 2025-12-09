---
title: "Blog 3"
# date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---
---
## Các phương pháp tốt nhất để tạo và kiểm tra ground truth nhằm đánh giá việc hỏi – đáp khi sử dụng trí tuệ nhân tạo (Generative AI) bằng công cụ FMEval

## Giới thiệu

Các ứng dụng hỏi – đáp dựa trên trí tuệ nhân tạo (Generative AI) đang mở rộng giới hạn của năng suất trong doanh nghiệp. Các trợ lý này có thể được xây dựng dựa trên nhiều kiến trúc backend khác nhau, bao gồm Retrieval Augmented Generation (RAG), agentic workflows, LLMs được tinh chỉnh, hoặc sự kết hợp của các kỹ thuật này. Tuy nhiên, việc xây dựng và triển khai các trợ lý AI đáng tin cậy đòi hỏi phải có một bộ dữ liệu ground truth và khung đánh giá vững chắc.

Trong lĩnh vực AI, ground truth data là dữ liệu đã được xác nhận là đúng, đại diện cho kết quả mong đợi của hệ thống đang được mô phỏng. Bằng cách cung cấp kết quả mong đợi để so sánh, dữ liệu ground truth cho phép đánh giá chất lượng hệ thống một cách xác định. Việc thực hiện đánh giá xác định các trợ lý AI dựa trên dữ liệu ground truth theo từng trường hợp sử dụng cho phép xây dựng custom benchmarks.

<!--more-->

## Tạo dữ liệu ground truth cho đánh giá hỏi – đáp bằng FMEval

Một cách để bắt đầu tạo dữ liệu ground truth là chọn lọc thủ công một tập dữ liệu nhỏ gồm các cặp câu hỏi – câu trả lời. Tập dữ liệu được con người chọn lọc này nên có quy mô nhỏ (tùy theo năng lực thực hiện), chất lượng cao, và được chuẩn bị bởi các chuyên gia am hiểu nghiệp vụ (SMEs) của trường hợp sử dụng cụ thể.

### Kết quả của hoạt động tạo ground truth ban đầu

Kết quả của hoạt động này bao gồm ba điểm chính:

1. Thống nhất giữa các bên liên quan về N câu hỏi quan trọng hàng đầu
2. Nâng cao nhận thức của các bên liên quan về quy trình đánh giá
3. Một tập dữ liệu ground truth ban đầu có độ chính xác cao, dùng cho bước đánh giá proof of concept đầu tiên

### Mở rộng quy mô với LLM

Mặc dù việc chọn lọc dữ liệu ground truth bởi chuyên gia (SME curation) là một khởi đầu vững chắc, nhưng ở quy mô cơ sở tri thức doanh nghiệp lớn, việc chỉ dựa vào chuyên gia để tạo dữ liệu ground truth sẽ tốn rất nhiều thời gian và nguồn lực. Để mở rộng quy mô quá trình tạo và chọn lọc dữ liệu ground truth, bạn có thể kết hợp phương pháp đánh giá dựa trên rủi ro với chiến lược dựa trên prompt sử dụng LLM.

## Ví dụ về Ground Truth Generation

Theo các phương pháp tốt nhất về chọn lọc dữ liệu ground truth cho đánh giá hỏi – đáp FMEval, dữ liệu ground truth được xây dựng dưới dạng bộ ba câu hỏi – câu trả lời – dữ kiện.

### Ví dụ từ thư gửi cổ đông Amazon 2023

**Nguồn dữ liệu:**
> Năm 2023, tổng doanh thu của Amazon tăng 12% so với cùng kỳ năm trước ("YoY"), từ 514 tỷ USD lên 575 tỷ USD. Theo từng mảng, doanh thu khu vực Bắc Mỹ tăng 12% YoY từ 316 tỷ USD lên 353 tỷ USD, doanh thu quốc tế tăng 11% YoY từ 118 tỷ USD lên 131 tỷ USD, và doanh thu AWS tăng 13% YoY từ 80 tỷ USD lên 91 tỷ USD.

**Ground Truth được tạo ra:**

| Câu hỏi | Ground Truth Answer | Fact |
|---------|---------------------|------|
| Amazon có mức tăng trưởng tổng doanh thu năm 2023 là bao nhiêu? | Tổng doanh thu của Amazon tăng 12% so với cùng kỳ năm trước, từ 514 tỷ USD lên 575 tỷ USD vào năm 2023. | 12%<OR>514 tỷ USD đến 575 tỷ USD |
| Doanh thu khu vực Bắc Mỹ tăng bao nhiêu vào năm 2023? | Doanh thu khu vực Bắc Mỹ tăng 12% so với cùng kỳ năm trước, từ 316 tỷ USD lên 353 tỷ USD. | 12%<OR>316 tỷ USD đến 353 tỷ USD |
| Mức tăng trưởng doanh thu quốc tế của Amazon trong năm 2023 là bao nhiêu? | Doanh thu quốc tế tăng 11% so với cùng kỳ năm trước, từ 118 tỷ USD lên 131 tỷ USD. | 11%<OR>118 tỷ USD đến 131 tỷ USD |

## Mở rộng quy mô tạo dữ liệu ground truth bằng pipeline

Để tự động hóa quá trình tạo dữ liệu ground truth, có thể sử dụng một kiến trúc pipeline xử lý hàng loạt không máy chủ. Pipeline AWS Step Functions tiếp nhận dữ liệu nguồn được lưu trữ trong Amazon S3, và điều phối các hàm AWS Lambda để thực hiện:

- Nạp dữ liệu
- Chia nhỏ dữ liệu (chunking)
- Gửi prompt trên Amazon Bedrock
- Tạo ra dữ liệu ground truth ở định dạng JSONLines

### Tham số đầu vào

Ba tham số đầu vào cần cung cấp cho Step Function:

```json
{
  "dataset_name": "YOUR_DATASET_NAME",
  "input_prefix": "YOUR_INPUT_PREFIX",
  "review_percentage": "REVIEW_PERCENTAGE"
}
```

## Đánh giá ground truth cho việc đánh giá hỏi-đáp của FMEval

Việc đo lường chất lượng ground truth là một yếu tố thiết yếu trong toàn bộ vòng đời đánh giá. Có hai thành phần chính trong việc đánh giá chất lượng của ground truth:

### 1. Human-in-the-loop (HITL)

Mức độ cần thiết của việc xem xét ground truth bởi con người được xác định dựa trên rủi ro của việc có ground truth không chính xác. Các bước của HITL bao gồm:

1. **Classify risk**: Thực hiện phân tích rủi ro để xác định mức độ nghiêm trọng
2. **Human review**: Các chuyên gia đánh giá một tỷ lệ tương ứng của ground truth
3. **Identify findings**: Xác định các hiện tượng "ảo giác" hoặc vấn đề về tính xác thực
4. **Action results**: Thực hiện các hành động nghiệp vụ như cập nhật hoặc xóa bản ghi

#### Bảng đánh giá rủi ro

| Likelihood | Severity | | | | |
|------------|----------|---|---|---|---|
| | **Extreme** | Low | Medium | High | Critical | Critical |
| | **Major** | Very low | Low | Medium | High | Critical |
| | **Moderate** | Very low | Low | Medium | Medium | High |
| | **Low** | Very low | Very low | Low | Low | Medium |
| | **Very Low** | Very low | Very low | Very low | Very low | Low |

### 2. LLM-as-a-judge

Khi mở rộng quy mô của HITL, các mô hình LLM có thể thực hiện việc phát hiện và khắc phục hiện tượng hallucination. Ý tưởng này được gọi là self-reflective RAG và có thể được sử dụng để giảm bớt khối lượng công việc của con người trong quy trình phát hiện hallucination.

Amazon Bedrock hiện cung cấp khả năng sử dụng các LLM làm công cụ đánh giá, đồng thời thực hiện kiểm tra lập luận tự động thông qua Amazon Bedrock Guardrails.

### Ví dụ về phát hiện Hallucination

| Nguồn dữ liệu gốc | Hallucination | Khắc phục |
|-------------------|---------------|-----------|
| Tổng doanh thu tăng **12%** từ 514 tỷ USD lên 575 tỷ USD | Ground truth answer: "tăng **15%**" | Ground truth answer: "tăng **12%**" |

## Kết luận

Bài viết này đã cung cấp hướng dẫn về việc tạo và đánh giá ground truth nhằm phục vụ quá trình đánh giá các ứng dụng hỏi–đáp bằng FMEval. Các thực tiễn tốt nhất được trình bày bao gồm:

- Sử dụng LLM để mở rộng quy mô tạo ground truth
- Kiến trúc đường ống xử lý dạng serverless batch
- Quy trình human-in-the-loop dựa trên rủi ro
- Áp dụng LLM-as-a-judge để phát hiện hallucination

Bằng cách tuân theo các hướng dẫn này, các tổ chức có thể áp dụng các thực tiễn AI có trách nhiệm trong việc xây dựng các tập dữ liệu ground truth chất lượng cao.

---

## Về các tác giả

**Samantha Stuart** là Data Scientist thuộc bộ phận AWS Professional Services với chuyên môn về generative AI, MLOps và ETL. Cô có bằng thạc sĩ nghiên cứu về kỹ thuật tại Đại học Toronto.

**Philippe Duplessis-Guindon** là cloud consultant tại AWS, chuyên về hạ tầng, DevOps, phát triển phần mềm và AI/ML. Anh có bằng thạc sĩ về computer vision và machine learning tại Polytechnique Montreal.

**Rahul Jani** là Data Architect thuộc AWS Professional Services, chuyên về thiết kế và triển khai các ứng dụng big data và phân tích trên nền tảng AWS.

**Ivan Cui** là Data Science Lead tại AWS Professional Services, hỗ trợ khách hàng xây dựng và triển khai các giải pháp ML và AI trên AWS.