---
title: "Blog 1"
date: 2026-05-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# TÍCH HỢP AMAZON EVENTBRIDGE VÀO ỨNG DỤNG SERVERLESS

Kiến trúc event-driven giúp xây dựng các dịch vụ tách rời trong ứng dụng. Trong bài blog này — tóm tắt từ bài viết trên AWS Compute Blog của James Beswick — em trình bày cách **Amazon EventBridge** chuyển các event khớp quy tắc tới target như **AWS Lambda**, để business logic phản ứng với event thay vì gọi đồng bộ.

### Thành phần kiến trúc chính:

- **Event Producer & Consumer (AWS Lambda):** ứng dụng publish event lên EventBridge bằng `putEvents`. Trong ví dụ, ứng dụng ngân hàng ATM (producer) tạo các event giao dịch, nhiều Lambda function downstream (consumer) chỉ xử lý tập event mà mình đăng ký.

- **EventBridge Rules & Targets:** event bus mặc định so khớp event JSON với các rule; mỗi rule định nghĩa event pattern và target cần gọi. Rule được khai báo bằng resource `AWS::Events::Rule`, kèm `AWS::Lambda::Permission` cấp quyền EventBridge gọi Lambda.

- **Khai báo bằng AWS SAM:** toàn bộ sample được triển khai bằng AWS Serverless Application Model (SAM). Bài hướng dẫn hai kiểu — cấu hình rule qua thuộc tính `Events` của function, hoặc khai báo rule là resource độc lập với event pattern, targets và permissions tường minh.

### Lợi ích của kiến trúc:

- **Tách rời:** các dịch vụ trao đổi với nhau qua event thay vì gọi trực tiếp, nên producer và consumer mở rộng, phát triển độc lập.
- **Cấu hình tối giản:** EventBridge lo routing, phân phối at-least-once kèm retry và batching ngay khi dùng.
- **Chi phí thấp:** ví dụ chạy trong AWS Free Tier, chỉ trả phí khi event thực sự được xử lý.

---

### Liên kết tham khảo:

- **Bài đăng Facebook:** [Nhóm AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj)
- **Bài viết AWS:** [Integrating Amazon EventBridge into your serverless applications](https://aws.amazon.com/blogs/compute/integrating-amazon-eventbridge-into-your-serverless-applications)