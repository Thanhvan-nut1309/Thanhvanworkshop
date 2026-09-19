---
title: "Workshop"
date: 2026-08-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Workshop: Hệ thống quản lý thư viện trên AWS — Triển khai thực tế

![Kiến trúc workshop](/images/2-Proposal/architecture.svg)

Các bước triển khai thực tế cho **Hệ thống quản lý thư viện cloud-native** đã mô tả trong proposal thực tập. Phần này ghi lại toàn bộ hạ tầng AWS em tự xây dựng trong kỳ thực tập — từ nền tảng ứng dụng local đến mạng, dịch vụ managed, triển khai container và migrate database — trên dự án [capstone](https://github.com/Thanhvan-nut1309/Thanhvanworkshop).

**Region:** `ap-southeast-2` (Sydney)  
**Phạm vi:** nền tảng ứng dụng local (Node.js/Express/MySQL), mạng VPC, RDS + S3 + Secrets Manager, container hóa bằng ECR, triển khai ECS Fargate + ALB, migrate database, lộ trình tự động hóa, demo trực tiếp và quy trình dọn dẹp có tài liệu.

#### Nội dung

1. [Tổng quan workshop](5.1-Workshop-overview/)
2. [Nền tảng ứng dụng — backend local](5.2-Foundation/)
3. [Mạng — VPC `library-vpc`](5.3-Networking/)
4. [Dịch vụ ứng dụng — RDS, S3, Secrets Manager](5.4-Application-Services/)
5. [Container hóa — Amazon ECR](5.5-Containerize/)
6. [Triển khai — ECS Fargate + ALB](5.6-Deploy/)
7. [Migrate database & củng cố bảo mật](5.7-Database-Migration/)
8. [Lộ trình tự động hóa & giám sát](5.8-Automation/)
9. [Demo trực tiếp & kiểm chứng](5.9-Live-Demo/)
10. [Dọn dẹp tài nguyên (có tài liệu)](5.10-Cleanup/)