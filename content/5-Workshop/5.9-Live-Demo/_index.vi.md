---
title: "Demo trực tiếp & kiểm chứng"
date: 2026-09-01
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

## Hệ thống đang chạy

Sau khi triển khai healthy, ứng dụng truy cập được qua **ALB DNS name**:

```
http://library-alb-<random>.ap-southeast-2.elb.amazonaws.com
```

`GET /health` trả `{"status":"ok"}` — đúng check mà load balancer dùng cho target group.

## Walkthrough end-to-end ở local (đã kiểm chứng trong Docker)

### Register / login

```powershell
curl.exe -X POST http://localhost:3000/api/auth/register -H "Content-Type: application/json" -d "{\"name\":\"Demo Reader\",\"email\":\"demo@example.com\",\"password\":\"123456\"}"
# → tạo user với role "user"; sau đó POST /api/auth/login trả JWT
```

### Admin tạo sách

```powershell
curl.exe -X POST http://localhost:3000/api/books -H "Authorization: Bearer <admin-jwt>" -H "Content-Type: application/json" -d "{\"title\":\"Dế Mèn phiêu lưu ký\",\"author\":\"Tô Hoài\",\"category\":\"Văn học\",\"total_copies\":2}"
```

### Độc giả mượn và trả

1. `POST /api/borrow` (JWT độc giả) → tạo bản ghi `borrow_records` với `due_date = +14 ngày`, giảm `available_copies`.
2. `GET /api/borrow/me` → độc giả xem lượt mượn đang có và hạn trả.
3. `POST /api/borrow/:id/return` → `status = returned`, khôi phục số bản.

### Tìm kiếm

`GET /api/books?q=tô+hoài` → lọc theo tên/tác giả/thể loại.

### Ảnh cần bổ sung cho phần này

{{< screenshot src="/images/5-Workshop/5.9-Live-Demo/register-login.png" lang="vi" caption="Terminal: register và login qua ALB trả về user đã tạo và JWT." >}}

{{< screenshot src="/images/5-Workshop/5.9-Live-Demo/admin-create-book.png" lang="vi" caption="Admin tạo sách (ví dụ Dế Mèn phiêu lưu ký của Tô Hoài) và nhận lại bản ghi vừa tạo." >}}

{{< screenshot src="/images/5-Workshop/5.9-Live-Demo/borrow-return.png" lang="vi" caption="Luồng mượn của độc giả: POST /api/borrow tạo khoản mượn kèm hạn 14 ngày; GET /api/borrow/me liệt kê; trả sách đưa status về returned." >}}

{{< screenshot src="/images/5-Workshop/5.9-Live-Demo/search-results.png" lang="vi" caption="Kết quả tìm kiếm: GET /api/books?q=tô+hoài lọc theo tên/tác giả/thể loại." >}}

## Kiểm chứng trên hệ thống đã deploy

Sau khi ECS + ALB và migrate schema:

| Kiểm tra | Kỳ vọng | Kết quả |
|-------|----------|--------|
| `GET /health` qua ALB | `{"status":"ok"}` | ✅ |
| ALB target health | `Healthy` | ✅ |
| API register qua ALB (`curl.exe ... /api/auth/register`) | 201 / user tạo thành công | ✅ |
| Login + danh sách sách qua ALB | JWT + danh sách sách | ✅ |
| RDS có 3 bảng | `books`, `users`, `borrow_records` | ✅ |
| RDS `Public access` | `No` (đã tắt lại sau migrate) | ✅ |

## Kết quả mong đợi

- Cùng mã nguồn ứng dụng chạy được **local (Docker) và production (ECS Fargate)** từ cùng một image
- Mọi luồng chính — register, login, CRUD sách, mượn, trả, tìm kiếm — đã kiểm chứng end-to-end
- Database luôn ở trạng thái riêng tư trong suốt demo