---
title: "Worklog Tuần 2"
date: 2026-08-08
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu tuần 2:

* Tiếp tục khám phá các dịch vụ AWS ngoài EC2 và VPC.
* Triển khai ứng dụng thành công trên hạ tầng AWS.
* Đi sâu vào các dịch vụ lưu trữ dữ liệu và giám sát hệ thống của AWS.

**Thời gian:** 08/08/2026 – 14/08/2026

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | ------------ | --------------- | -------------- |
| 2 | - Khám phá S3: bucket, object, quyền truy cập, static website hosting <br> - Khám phá IAM: user, role, policy, nguyên tắc least-privilege | 10/08/2026 | 10/08/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Lab:** Triển khai web tĩnh lên S3 với quyền public read <br> - **Lab:** Triển khai ứng dụng đơn giản trên EC2 (web server + mã nguồn) | 11/08/2026 | 11/08/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - Học S3 storage class, versioning và lifecycle policy <br> - Học EBS vs EFS và cơ bản về DynamoDB | 12/08/2026 | 12/08/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Lab:** Tạo bảng DynamoDB, insert/query qua console và CLI | 13/08/2026 | 13/08/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - Học CloudWatch: metrics, alarm, logs, dashboard <br> - Cài đặt và cấu hình AWS CLI (credential, region mặc định `ap-southeast-1`) | 14/08/2026 | 14/08/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 2:

* Triển khai website tĩnh trên Amazon S3 và ứng dụng trên EC2; xác minh truy cập công khai.
* Hiểu sự khác biệt IAM role vs user và gắn policy cơ bản.
* Tạo và truy vấn bảng DynamoDB; cấu hình S3 versioning và lifecycle rule.
* So sánh EBS, EFS và S3; xác định use case phù hợp cho từng loại.
* Cài đặt AWS CLI và thực hiện thao tác CRUD đầy đủ trên EC2, S3 và DynamoDB từ dòng lệnh.
* Tạo CloudWatch alarm và dashboard để giám sát tài nguyên đang chạy.
* Thành thạo CLI và kiến thức lưu trữ — nền tảng cho dự án capstone và CI/CD sau này.