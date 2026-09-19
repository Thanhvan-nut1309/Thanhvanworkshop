---
title: "Worklog Tuần 5"
date: 2026-08-29
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu tuần 5:

* Triển khai backend capstone lên ECS sau Application Load Balancer.
* Kết nối ứng dụng với database được quản lý và hoàn tất di trú dữ liệu.
* Thiết kế automation giai đoạn cuối và hoàn thiện workshop + báo cáo thực tập.

**Thời gian:** 29/08/2026 – 04/09/2026

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | ------------ | --------------- | -------------- |
| 2 | - Đẩy image đã kiểm thử lên repository ECR `library-management` <br> - Tạo ECS cluster `library-cluster`, đăng ký task definition `task-library-management` <br> - Tạo IAM role `library-ecs-task-role` | 31/08/2026 | 31/08/2026 | AWS ECS docs |
| 3 | - Xây dựng ALB `library-alb` + target group + listeners <br> - Deploy Fargate service trong private subnet của `library-vpc`; gắn `library-db-credentials` từ Secrets Manager | 01/09/2026 | 01/09/2026 | |
| 4 | - Chạy migration schema và dữ liệu mẫu trên RDS `library-db` <br> - Kiểm thử end-to-end qua HTTPS qua ALB: register, login, mượn, trả | 02/09/2026 | 02/09/2026 | |
| 5 | - Thiết kế automation giai đoạn cuối: EventBridge → Lambda → SES nhắc hạn trả, CloudWatch alarms, CodeBuild CI/CD <br> - Tài liệu hóa automation và quy trình dọn dẹp trong các phần workshop | 03/09/2026 | 03/09/2026 | |
| 6 | - Thiết lập site báo cáo Hugo từ template FCAJ; tùy chỉnh `config.toml` <br> - Điền nội dung worklog, proposal và workshop; cấu hình GitHub Actions (`hugo.yml`) <br> - Rà soát, hoàn thiện, viết bài thu hoạch sự kiện và nộp báo cáo | 04/09/2026 | 04/09/2026 | |

### Kết quả đạt được tuần 5:

* Triển khai backend Hệ thống quản lý thư viện thành Fargate service trong `library-cluster` sau load balancer `library-alb`.
* Kết nối ứng dụng với Amazon RDS `library-db`, hoàn tất migration schema và nạp dữ liệu mẫu.
* Bảo mật thông tin đăng nhập database trong task ECS qua secret `library-db-credentials` của Secrets Manager.
* Xác minh luồng hoàn chỉnh qua ALB: register → login → xem sách → mượn → trả trong quy tắc 14 ngày.
* Thiết kế và tài liệu hóa automation giai đoạn cuối (EventBridge → Lambda → SES nhắc hạn, CloudWatch alarms, CodeBuild CI/CD) được trình bày trung thực dưới dạng thiết kế kèm bảng trạng thái.
* Hoàn thành tất cả bài viết lab workshop và tài liệu dọn dẹp tài nguyên.
* Xây dựng site báo cáo thực tập Hugo và deploy lên GitHub Pages qua GitHub Actions.
* Trỏ repository về `Thanhvan-nut1309/Thanhvanworkshop` với `baseURL` và author đúng.
* Viết bài thu hoạch sự kiện và hoàn thành các phần tự đánh giá, chia sẻ góp ý.
* Deploy thành công website báo cáo và nộp báo cáo cuối kỳ.