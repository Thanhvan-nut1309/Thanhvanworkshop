---
title: "Nền tảng ứng dụng"
date: 2026-08-17
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

## Mục tiêu

Xây dựng và chạy **backend ứng dụng ở local** trước khi đụng đến hạ tầng cloud — một stack Docker hóa Node.js/Express + MySQL, sau này chính là image triển khai lên ECS.

## Stack

- **Backend:** Node.js 20 + Express, image Docker `node:20-alpine`
- **Database:** MySQL (tương thích Amazon RDS MySQL sau này)
- **Auth:** register/login cấp **JWT**, phân quyền `admin` / `user`
- **Tính năng:** CRUD sách + tìm kiếm (tên/tác giả/thể loại), mượn/trả với **hạn 14 ngày**, kiểm tra tồn kho và ghi dữ liệu transaction

### Thiết kế database (`schema.sql`)

| Bảng | Mục đích |
|-------|---------|
| `books` | `id, title, author, category, cover_image_url, total_copies, available_copies` |
| `users` | `id, name, email, password_hash, role` |
| `borrow_records` | `id, book_id, user_id, borrow_date, due_date, return_date, status` |

`status` gồm `borrowing` / `returned` / `overdue`.

## Bước 1 — Chạy local bằng Docker (khuyên dùng) hoặc native

```powershell
# Phương án A — Docker (1 lệnh, không cần cài Node/MySQL)
docker compose up --build
# mở http://localhost:3000/health  →  {"status":"ok"}

# Phương án B — native (cần đã cài Node.js và MySQL)
mysql -u root -p < schema.sql      # cmd; PowerShell dùng: Get-Content schema.sql | mysql -u root -p
cp .env.example .env
npm install
npm run dev
```

`docker-compose.yml` chạy hai container (**app** ở host port `3000`, **mysql** ở host port `3307`). Trong mạng Docker nội bộ app kết nối qua `DB_HOST=mysql` / `DB_PORT=3306` — đổi host port **không** ảnh hưởng traffic app ↔ db.

## Bước 2 — Smoke-test API

```powershell
curl.exe -X POST http://localhost:3000/api/auth/register -H "Content-Type: application/json" -d "{\"name\":\"Test User\",\"email\":\"test@example.com\",\"password\":\"123456\"}"
```

Dùng `curl.exe` (không phải alias `curl` của PowerShell) để cú pháp `-d` chạy đúng cả PowerShell lẫn cmd.

## Bước 3 — Kiểm chứng logic mượn/trả

1. Register/login → lấy JWT (tài khoản `admin` để tạo sách).
2. Tạo sách với `total_copies` và `available_copies`.
3. Mượn sách với tài khoản `user` → tạo bản ghi `borrow_records` với `due_date = borrow_date + 14 ngày`, giảm `available_copies` trong transaction.
4. Trả sách → `status = returned`, khôi phục số bản.
5. Tìm kiếm theo tên/tác giả/thể loại trả về đúng tập con.

## Kết quả mong đợi

- `GET /health` trả `{"status":"ok"}`
- Register/login, CRUD sách, mượn/trả và tìm kiếm đều hoạt động trên MySQL
- `Dockerfile` + `docker-compose.yml` sẽ được tái sử dụng cho ECR/ECS sau này

## Xử lý sự cố

| Vấn đề | Kiểm tra |
|-------|--------|
| Báo `npm: command not found` trong PowerShell | Node.js chưa cài (hoặc PATH chưa refresh) — cài Node LTS và mở lại terminal |
| Báo `The '<' operator is reserved` trong PowerShell | PowerShell không hỗ trợ redirect `<`; dùng pipe: `Get-Content schema.sql | mysql -u root -p` |
| Báo `port 3306 already in use` | Có container MySQL khác (`mysql-local`) chiếm host port; remap **host** sang `3307` trong `docker-compose.yml` — cổng nội bộ vẫn `3306` |
| `curl` báo lỗi | `curl` trong PowerShell là alias của `Invoke-WebRequest`; luôn dùng `curl.exe` |