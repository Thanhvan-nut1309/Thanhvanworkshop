---
title: "Worklog Tuần 4"
date: 2026-08-22
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu tuần 4:

* Handoff client với luồng login cho team (netcode + auth đã sketch).
* Triển khai pipeline CI/CD qua GitHub và GitHub Actions.
* Triển khai IAM Permission Boundaries và kiểm tra khả năng deploy của CodeDeploy.

**Thời gian:** 22/08/2026 – 28/08/2026

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | ------------ | --------------- | -------------- |
| 2 | - Hoàn thiện UI login client và xử lý session cho team tích hợp <br> - Handoff codebase client với interface auth + netcode đã tài liệu hóa | 24/08/2026 | 24/08/2026 | |
| 3 | - Thiết lập cấu trúc GitHub repository và quy ước nhánh <br> - **CI/CD:** Tạo workflow GitHub Actions đầu tiên cho build và deploy | 25/08/2026 | 25/08/2026 | Github Actions docs |
| 4 | - Cấu hình S3 static website hosting cho client <br> - Học IAM Permission Boundaries và pattern deploy least-privilege | 26/08/2026 | 26/08/2026 | AWS IAM docs |
| 5 | - Đăng ký GitHub OIDC identity provider trong IAM (không dùng access key tĩnh) <br> - Tạo role deploy với trust policy giới hạn đến repo GitHub | 27/08/2026 | 27/08/2026 | |
| 6 | - **Test:** Push lên GitHub → xác minh S3 sync, Lambda update và CodeDeploy job thành công | 28/08/2026 | 28/08/2026 | |

### Kết quả đạt được tuần 4:

* Giao package handoff client hoàn chỉnh với luồng login và stub netcode cho team.
* Tạo repository dự án và thiết lập quy ước nhánh.
* Triển khai workflow GitHub Actions đầu tiên cho build client tự động.
* Tạo S3 assets bucket với static website hosting, policy public-read và CORS.
* Loại bỏ access key tĩnh khỏi CI bằng GitHub OIDC → IAM role authentication.
* Áp dụng IAM permission boundaries để giới hạn khả năng của role deploy.
* Xác minh deploy end-to-end: push GitHub kích hoạt Actions → S3 client sync + CodeDeploy cho Lambda và EC2 fleet.