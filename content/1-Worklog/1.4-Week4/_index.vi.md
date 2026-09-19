---
title: "Worklog Tuần 4"
date: 2026-08-22
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu tuần 4:

* Xây dựng nền tảng mạng cloud cho ứng dụng.
* Cung cấp các dịch vụ dữ liệu được quản lý (RDS, S3, Secrets Manager).
* Chuẩn bị luồng container ECR cho việc triển khai.

**Thời gian:** 22/08/2026 – 28/08/2026

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | ------------ | --------------- | -------------- |
| 2 | - Hoàn thiện kiến trúc và ERD cho Hệ thống quản lý thư viện <br> - Gán vai trò cho từng dịch vụ AWS (VPC, RDS, S3, Secrets Manager, ECS) | 24/08/2026 | 24/08/2026 | |
| 3 | - **VPC:** Tạo `library-vpc` bằng VPC wizard (2 public + 2 private subnet) <br> - Cấu hình Internet Gateway, NAT Gateway (Regional) và S3 Gateway endpoint | 25/08/2026 | 25/08/2026 | AWS VPC docs |
| 4 | - **RDS:** Tạo `library-db` MySQL (Free tier, private subnet, schema `library_db`) <br> - Chờ và xác minh instance đạt trạng thái `Available` | 26/08/2026 | 26/08/2026 | |
| 5 | - **S3:** Tạo bucket `library-covers-thanhvan-2026` để lưu ảnh bìa sách <br> - **Secrets Manager:** tạo secret `library-db-credentials` liên kết với RDS | 27/08/2026 | 27/08/2026 | |
| 6 | - **ECR:** tạo repository private `library-management` <br> - Xác minh tên và trạng thái tất cả tài nguyên trước khi deploy | 28/08/2026 | 28/08/2026 | |

### Kết quả đạt được tuần 4:

* Tạo `library-vpc` riêng với phân tách public/private rõ ràng và egress qua NAT.
* Cấp phát Amazon RDS MySQL (`library-db`) trong private subnet, không có quyền truy cập công khai.
* Tạo S3 bucket bìa sách và secret `library-db-credentials` trong Secrets Manager.
* Tạo repository ECR private sẵn sàng nhận image đã được kiểm thử.
* Tuân thủ nguyên tắc least-privilege và private network trong suốt quá trình thiết lập.