---
title: "Dịch vụ ứng dụng — RDS, S3, Secrets Manager"
date: 2026-08-21
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

## Mục tiêu

Dựng ba **dịch vụ dữ liệu managed** mà ứng dụng phụ thuộc: **Amazon RDS MySQL** (dữ liệu quan hệ), **Amazon S3** (ảnh bìa sách) và **AWS Secrets Manager** (thông tin đăng nhập database).

## Bước 1 — Tạo RDS MySQL

1. **RDS → Create database → chọn "Full configuration"** từ dropdown (không chọn "Express configuration" — loại này giấu các tùy chọn mạng cần thiết).
   > Đảm bảo **Engine type = MySQL**. Với engine Aurora, tùy chọn "Full configuration" bị mờ — hai chỗ phải khớp.
2. **Templates:** `Free tier` (availability bị khóa ở `Single-AZ`).
3. **Settings:** DB instance identifier `library-db`; Master username `admin`; đặt master password mạnh và **chỉ** lưu trong Secrets Manager (không bao giờ trong code).
4. **Instance configuration:** `db.t3.micro` (do Free tier cố định). **Storage:** 20 GB mặc định.
5. **Connectivity:**
   - **VPC:** `library-vpc`
   - **Public access:** `No`
   - **VPC security group:** `Create new` → `library-db-sg`
   - DB subnet group: để mặc định (AWS tự chọn private subnet)
6. **Additional configuration → Initial database name:** `library_db` — bước quan trọng; nếu bỏ trống AWS sẽ không tự tạo database, sau này phải tạo thủ công.
7. **Create database** và đợi **5–10 phút** cho trạng thái sang `Available` (sẽ đi qua `Configuring-enhanced-monitoring` và `Backing-up` trước).

## Bước 2 — Tạo S3 bucket chứa ảnh bìa

1. **S3 → Create bucket** — tên `library-covers-thanhvan-2026` (tên bucket phải duy nhất toàn cầu; nếu trùng hãy đổi hậu tố).
2. Giữ mặc định: General purpose, ACLs **disabled**, **Block Public Access** bật hết (demo giữ private — ảnh bìa do app phục vụ).
3. Encryption: mặc định `SSE-S3`.
4. **Create bucket** và ghi nhớ tên — ứng dụng dùng nó để upload ảnh bìa.

## Bước 3 — Lưu thông tin đăng nhập vào Secrets Manager

1. **Secrets Manager → Store a new secret → Credentials for Amazon RDS database.**
2. **User name:** `admin`; **password:** master password ở Bước 1.
3. Chọn database **`library-db`** để liên kết secret.
4. **Secret name:** `library-db-credentials`.
5. **Automatic rotation:** giữ `Disable` (không cần cho workshop).
6. **Store**, rồi copy **Secret ARN** — dùng khi cấu hình ECS task definition ([5.6](5.6-Deploy/)).

### Ảnh minh hoạ cho phần này

{{< screenshot src="/images/5-Workshop/5.4-Application-Services/rds-create-config.png" lang="vi" caption="Màn hình RDS Create database (Full configuration): Engine MySQL, Free tier, identifier library-db, initial database name library_db." >}}

{{< screenshot src="/images/5-Workshop/5.4-Application-Services/rds-available.png" lang="vi" caption="Console RDS: library-db ở trạng thái Available, Public access = No, VPC = library-vpc." >}}

{{< screenshot src="/images/5-Workshop/5.4-Application-Services/s3-bucket-created.png" lang="vi" caption="Console S3 liệt kê bucket library-covers-thanhvan-2026 với Block Public Access bật." >}}

{{< screenshot src="/images/5-Workshop/5.4-Application-Services/secret-created.png" lang="vi" caption="Secrets Manager: secret library-db-credentials liên kết với database library-db." >}}

## Kết quả mong đợi

- `library-db` trạng thái `Available`, private trong `library-vpc`, không public access
- S3 bucket `library-covers-thanhvan-2026` sẵn sàng
- Secret `library-db-credentials` đã tạo và liên kết RDS instance

## Xử lý sự cố

| Vấn đề | Kiểm tra |
|-------|--------|
| `Full configuration` bị mờ | Bạn chọn nhầm engine Aurora/PostgreSQL — đổi Engine type về **MySQL** |
| RDS kẹt ở `Configuring`/`Backing-up` | Là bước cuối bình thường; refresh và đợi thêm vài phút |
| RDS available nhưng không có database | `Initial database name` bị bỏ trống lúc tạo — chạy thủ công `schema.sql` (xem [5.7](5.7-Database-Migration/)) |