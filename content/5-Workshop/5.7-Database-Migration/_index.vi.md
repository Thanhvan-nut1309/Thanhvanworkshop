---
title: "Migrate database & củng cố bảo mật"
date: 2026-08-28
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

## Mục tiêu

Nạp `schema.sql` vào **RDS** (các bảng `books`, `users`, `borrow_records`) **mà không để database lộ ra công khai**. Migration là việc làm một lần từ máy trạm qua quy tắc inbound mở tạm — rồi đóng lại.

## Bước 1 — Bật tạm RDS public access

1. **RDS → Databases → library-db → Modify.**
2. **Connectivity → Public access:** `Yes`.
3. Chọn **Apply immediately** → **Continue** → **Modify**.
4. Đợi trạng thái quay lại `Available` (sau `Modifying`).

## Bước 2 — Chỉ mở port 3306 cho IP của bạn

1. **VPC → Security Groups → `library-db-sg` → Inbound rules → Edit inbound rules → Add rule.**
2. Type: `MySQL/Aurora`; Source: **`My IP`** (AWS tự điền IP công khai hiện tại của bạn).
3. Save rules.

## Bước 3 — Kết nối MySQL Workbench và chạy schema.sql

1. Trong MySQL Workbench tạo kết nối mới:
   - **Hostname:** `library-db.cb826ysqej0o.ap-southeast-2.rds.amazonaws.com`
   - **Port:** `3306`
   - **Username:** `admin`
   - **Password:** master password bạn đã lưu trong Secrets Manager
2. **Test Connection** → phải OK → lưu.
3. **File → Open SQL Script →** chọn `schema.sql` trong thư mục project → **Execute** (icon tia sét).
4. Xác nhận không có lỗi đỏ; ba bảng xuất hiện trong schema.

## Bước 4 — Đóng lại quyền truy cập công khai (quan trọng)

1. **RDS → Modify → Public access:** `No` → **Apply immediately** → **Continue** → **Modify**.
2. Trong `library-db-sg`, **gỡ** rule inbound 3306.
3. Kiểm chứng từ phía ECS: app vẫn đọc/ghi database (ALB `/health` và API mượn/trả vẫn hoạt động) — vì ECS chạy cùng VPC và dùng endpoint private.

> Đừng bỏ qua bước này. Cửa sổ public chỉ tồn tại để migrate; để mở sẽ lộ database ra internet.

### Ảnh minh hoạ cho phần này

{{< screenshot src="/images/5-Workshop/5.7-Database-Migration/sg-inbound-3306.png" lang="vi" caption="Inbound rules của library-db-sg: MySQL/Aurora port 3306 từ My IP." >}}

## Kết quả mong đợi

- RDS có đầy đủ schema (`books`, `users`, `borrow_records`)
- Database lại ở trạng thái **private** (không public access, không inbound 3306)
- Ứng dụng (ECS, private) vẫn hoạt động bình thường

## Xử lý sự cố

| Vấn đề | Kiểm tra |
|-------|--------|
| Workbench `Test Connection` fail | Public access đã bật? SG inbound đã thêm cho IP của bạn? Trạng thái đã về `Available`? |
| Không thấy database nào | Có thể `Initial database name` bỏ trống — kết nối và tạo `library_db` trước, rồi chạy `schema.sql` |
| App lỗi sau khi migrate | Bước revert đã đóng 3306 — ECS không cần nó; xác nhận task vẫn tới được RDS qua mạng private |