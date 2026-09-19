---
title: "Mạng — VPC"
date: 2026-08-19
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

## Mục tiêu

Tạo **ranh giới mạng ba lớp** cho ứng dụng: **public subnet** cho Application Load Balancer và **private subnet** cho ECS Fargate và RDS. Một VPC riêng giúp báo cáo rõ ràng, tránh xung đột với các VPC lab khác (`window-vpc`, `Linux-vpc`, default VPC) và dễ dọn dẹp cuối kỳ.

## Bước 1 — Tạo VPC bằng wizard

1. **VPC → Your VPCs → Create VPC → VPC and more** (chế độ wizard).
2. Name tag: `library-vpc`.
3. IPv4 CIDR: giữ mặc định của wizard (vd `10.0.0.0/16`).
4. **Number of AZs:** `2`; **Number of public subnets:** `2`; **Number of private subnets:** `2`.
5. NAT Gateway: **`Regional - new`** (một gateway dùng chung cho các AZ — rẻ hơn Zonal và đủ cho nhu cầu ra ngoài của app). **Không** chọn `None` (resource private sẽ không reach internet để pull update/image) và không chọn `Zonal` (tốn phí không cần thiết).
6. VPC endpoints: giữ mặc định **S3 Gateway** endpoint.
7. Tạo và đợi VPC sang trạng thái **Available**.

> ⚠️ **Lưu ý chi phí:** NAT Gateway là resource duy nhất chạy liên tục tính phí theo giờ (~$0.045/h). Sẽ được xoá khỏi tài khoản ở bước dọn dẹp sau demo.

## Bước 2 — Kiểm tra bố cục mạng

Vào **Your VPCs** thấy `library-vpc` với **State = Available**, và **Subnets** có **2 public + 2 private** gắn với nó (public route qua Internet Gateway; private route qua NAT).

## Bước 3 — Tạo security group database

1. **VPC → Security Groups → Create security group**, tên `library-db-sg`.
2. VPC: `library-vpc`.
3. Chưa thêm inbound rule — rule MySQL inbound chỉ mở ngắn hạn lúc migrate schema một lần ([5.7](5.7-Database-Migration/)), sau đó gỡ bỏ.

## Kết quả mong đợi

- VPC `library-vpc` riêng với tách biệt public/private subnet
- Internet Gateway cho traffic public; NAT Gateway (Regional) cho egress private
- S3 Gateway endpoint cho private subnet truy cập S3
- `library-db-sg` sẵn sàng gắn vào RDS

## Xử lý sự cố

| Vấn đề | Kiểm tra |
|-------|--------|
| VPC kẹt ở `Pending` | Đợi vài phút; NAT Gateway tạo mất thời gian |
| Private subnet không ra internet | NAT Gateway đã gắn? Route table private trỏ về NAT gateway? |
| Wizard bị khoá các tùy chọn `Full configuration` | Đây là trạng thái UI dễ nhầm — wizard "VPC and more" tự tạo toàn bộ subnet/route table trong một lần |