---
title: "Worklog Tuần 3"
date: 2026-08-15
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu tuần 3:

* Bắt đầu dự án capstone thực tập.
* Thiết kế kiến trúc ứng dụng và schema database.
* Xây dựng và chạy nền tảng backend (auth, sách, mượn/trả) ở local.

**Thời gian:** 15/08/2026 – 21/08/2026

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | ------------ | --------------- | -------------- |
| 2 | - Tham dự buổi kickoff dự án: tổng quan kiến trúc, vai trò team, deliverable <br> - Xác định yêu cầu dự án và phạm vi triển khai cloud | 17/08/2026 | 17/08/2026 | FCAJ project brief |
| 3 | - Khởi tạo repository `library-management` và skeleton backend (Express + MySQL) <br> - Viết `schema.sql`: các bảng `books`, `users`, `borrow_records` | 18/08/2026 | 18/08/2026 | Node.js docs |
| 4 | - Triển khai xác thực: register/login, JWT, phân quyền `admin`/`user` <br> - Hash mật khẩu trước khi lưu | 19/08/2026 | 19/08/2026 | |
| 5 | - Triển khai CRUD sách kèm tìm kiếm (tên/tác giả/thể loại) <br> - Triển khai mượn/trả: hạn 14 ngày, transaction, kiểm tra tồn kho | 20/08/2026 | 20/08/2026 | |
| 6 | - Docker hóa app (`Dockerfile` + `docker-compose.yml`: app + MySQL) <br> - Chạy và kiểm thử local: `/health`, register, mượn, trả | 21/08/2026 | 21/08/2026 | Docker docs |

### Kết quả đạt được tuần 3:

* Gia nhập dự án capstone với vai trò thành viên tích cực.
* Thiết kế kiến trúc cloud và schema quan hệ cho Hệ thống quản lý thư viện.
* Xây dựng nền tảng backend: JWT auth, CRUD sách + tìm kiếm, mượn/trả với quy tắc 14 ngày.
* Container hóa toàn bộ stack và chạy local bằng Docker Compose.
* Kiểm chứng luồng API chính end-to-end trên MySQL ở máy local.
* Chuẩn bị đúng image Docker sẽ được đẩy lên ECR và deploy trên ECS sau này.