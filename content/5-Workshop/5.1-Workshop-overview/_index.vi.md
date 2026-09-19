---
title: "Tổng quan workshop"
date: 2026-08-15
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## Mục đích

Workshop ghi lại quá trình triển khai AWS cho **Hệ thống quản lý thư viện (LMS)**: backend Node.js/Express (xác thực JWT, CRUD sách, tìm kiếm, mượn/trả với hạn 14 ngày) chạy trên **ECS Fargate** phía sau **Application Load Balancer**, **RDS MySQL** trong private subnet, **S3** lưu ảnh bìa và **Secrets Manager** lưu thông tin đăng nhập database — cùng pipeline serverless **nhắc hạn trả sách qua email** (EventBridge → Lambda → SES).

| Luồng | Thành phần | Phần workshop của em |
|------|------------|----------------------|
| **Yêu cầu web** | ALB, ECS Fargate, RDS | Nền tảng, Mạng, Dịch vụ ứng dụng, Triển khai |
| **Secrets** | Secrets Manager, task role | Dịch vụ ứng dụng, Triển khai |
| **Tự động hóa** | EventBridge, Lambda, SES | Lộ trình tự động hóa |
| **Phân phối** | GitHub, CodeBuild, ECR | Container hóa, Triển khai |

Từng phần đều được em tự triển khai và kiểm chứng trên tài khoản AWS riêng trong kỳ thực tập.

## Điều kiện tiên quyết

- Tài khoản AWS quyền admin tại `ap-southeast-2`
- Node.js + npm và Docker Desktop cho phát triển local
- Repository GitHub: `Thanhvan-nut1309/Thanhvanworkshop`
- AWS CLI cấu hình local (tùy chọn, cho lệnh ECR)
- MySQL Workbench (dùng một lần để migrate database)

## Tên tài nguyên tham chiếu

| Tài nguyên | Tên |
|------------|-----|
| VPC | `library-vpc` (2 public + 2 private subnet) |
| RDS instance / schema | `library-db` / `library_db` |
| Security group database | `library-db-sg` |
| S3 bucket | `library-covers-thanhvan-2026` |
| Secrets Manager secret | `library-db-credentials` |
| ECR repository | `library-management` |
| ECS cluster / task | `library-cluster` / `task-library-management` |
| IAM task role | `library-ecs-task-role` |
| ALB | `library-alb` (target group `/health`, cổng 3000) |
| Region / account | `ap-southeast-2` / `367764690039` |

## Thứ tự thực hiện

Làm **5.2 → 5.7** theo thứ tự — mỗi bước dựa trên bước trước (nền tảng → mạng → dịch vụ → container → triển khai → migrate). Mục **5.8** ghi lại phần mở rộng tự động hóa & giám sát ở giai đoạn cuối. Mục **5.9** kiểm chứng hệ thống đang chạy end-to-end. Mục **5.10** là hướng dẫn dọn dẹp theo thứ tự phụ thuộc.

## Checklist xác minh

- [ ] Stack Docker local trả `GET /health` với `{"status":"ok"}`
- [ ] `library-vpc` có 2 public + 2 private subnet, trạng thái Available
- [ ] RDS `library-db` ở trạng thái `Available`, `Public access = No`
- [ ] ECR có image đã tag `library-management:latest`
- [ ] Service ECS đang chạy, ALB health check `/health` pass
- [ ] `schema.sql` đã chạy trên RDS; có các bảng `books`, `users`, `borrow_records`
- [ ] RDS public access đã tắt lại **sau** khi migrate