# WORKSHOP SCREENSHOTS — checklist ảnh thao tác workshop

Danh sách toàn bộ ảnh màn hình (screenshot) cần chụp để hoàn thiện phần **5-Workshop**.
Mỗi ảnh đã được "cắm sẵn" vào đúng trang trong báo cáo (EN + VI) dưới dạng ô màu cam:
`📸 Ảnh cần bổ sung` / `Screenshot to add`. Chỉ cần **lưu ảnh PNG đúng tên file và đúng thư mục**
bên dưới (`static/images/5-Workshop/<thư mục>/`) rồi commit + push — ảnh sẽ **tự động hiển thị**,
không cần sửa lại file markdown.

> Thư mục đã được tạo sẵn (mỗi thư mục có `.gitkeep`). Đặt file `.png` vào là xong.
> Ảnh không bắt buộc: mục 5.8 (Automation) là phần thiết kế road map — chỉ chụp nếu đã triển khai thật.

| # | Thư mục chứa ảnh | Tên file ảnh | Nội dung cần chụp |
|---|---|---|---|
| 1 | `static/images/5-Workshop/5.2-Foundation/` | `local-health-check.png` | Kết quả `docker compose up` (2 container app + mysql); trình duyệt/curl `http://localhost:3000/health` trả về `{"status":"ok"}` |
| 2 | `static/images/5-Workshop/5.2-Foundation/` | `register-api.png` | Kết quả curl `POST /api/auth/register` trả về user mới và JWT |
| 3 | `static/images/5-Workshop/5.2-Foundation/` | `borrow-record.png` | Kết quả mượn sách: bản ghi `borrow_records` có `due_date = ngày mượn + 14 ngày`, `available_copies` giảm |
| 4 | `static/images/5-Workshop/5.3-Networking/` | `vpc-wizard-config.png` | Màn hình Create VPC (VPC and more): tên `library-vpc`, 2 AZ, 2 public + 2 private subnet, NAT Gateway Regional |
| 5 | `static/images/5-Workshop/5.3-Networking/` | `vpc-created.png` | Trang Your VPCs hiển thị `library-vpc` với State = Available |
| 6 | `static/images/5-Workshop/5.3-Networking/` | `subnets-list.png` | Trang Subnets lọc theo `library-vpc`: 2 public + 2 private subnet |
| 7 | `static/images/5-Workshop/5.3-Networking/` | `db-security-group.png` | Security group `library-db-sg` đã tạo, chưa có inbound rule |
| 8 | `static/images/5-Workshop/5.4-Application-Services/` | `rds-create-config.png` | RDS Create database (Full configuration): Engine MySQL, Free tier, identifier `library-db`, initial database name `library_db` |
| 9 | `static/images/5-Workshop/5.4-Application-Services/` | `rds-available.png` | Console RDS: `library-db` ở trạng thái Available, Public access = No, VPC = `library-vpc` |
| 10 | `static/images/5-Workshop/5.4-Application-Services/` | `s3-bucket-created.png` | Console S3: bucket `library-covers-thanhvan-2026` với Block Public Access bật |
| 11 | `static/images/5-Workshop/5.4-Application-Services/` | `secret-created.png` | Secrets Manager: secret `library-db-credentials` liên kết database `library-db` |
| 12 | `static/images/5-Workshop/5.5-Containerize/` | `ecr-repository.png` | Trang ECR Repositories: repository private `library-management` |
| 13 | `static/images/5-Workshop/5.5-Containerize/` | `docker-login-succeeded.png` | Terminal: lệnh đăng nhập ECR kết thúc bằng `Login Succeeded` |
| 14 | `static/images/5-Workshop/5.5-Containerize/` | `image-pushed.png` | Terminal: `docker push` hoàn tất kèm digest — hoặc console ECR hiển thị image `latest` với sha256 digest |
| 15 | `static/images/5-Workshop/5.6-Deploy/` | `task-role.png` | IAM role `library-ecs-task-role` gắn policy `SecretsManagerReadWrite` |
| 16 | `static/images/5-Workshop/5.6-Deploy/` | `cluster.png` | ECS cluster `library-cluster` với hạ tầng Fargate |
| 17 | `static/images/5-Workshop/5.6-Deploy/` | `task-definition-env.png` | Task definition `task-library-management` với 5 biến môi trường (PORT, USE_SECRETS_MANAGER, AWS_REGION, DB_SECRET_NAME, JWT_SECRET) |
| 18 | `static/images/5-Workshop/5.6-Deploy/` | `service-alb.png` | ECS service + ALB/target group: health check `/health` port 3000, target group healthy |
| 19 | `static/images/5-Workshop/5.6-Deploy/` | `health-alb.png` | Trình duyệt mở `/health` theo DNS name của ALB trả về `{"status":"ok"}` |
| 20 | `static/images/5-Workshop/5.7-Database-Migration/` | `rds-public-yes.png` | RDS Modify: Public access = Yes (Apply immediately) |
| 21 | `static/images/5-Workshop/5.7-Database-Migration/` | `sg-inbound-3306.png` | `library-db-sg`: inbound rule MySQL/Aurora port 3306 từ My IP |
| 22 | `static/images/5-Workshop/5.7-Database-Migration/` | `workbench-connection.png` | MySQL Workbench: Test Connection tới `library-db.cb826ysqej0o.ap-southeast-2.rds.amazonaws.com` thành công |
| 23 | `static/images/5-Workshop/5.7-Database-Migration/` | `workbench-schema.png` | MySQL Workbench sau khi chạy `schema.sql`: 3 bảng `books`, `users`, `borrow_records` trong `library_db` |
| 24 | `static/images/5-Workshop/5.7-Database-Migration/` | `rds-public-reverted.png` | RDS quay lại Public access = No và xoá inbound rule 3306 khỏi `library-db-sg` |
| 25 | `static/images/5-Workshop/5.8-Automation/` *(tùy chọn)* | `lambda-function.png` | Lambda `library-due-date-reminder` đã tạo |
| 26 | `static/images/5-Workshop/5.8-Automation/` *(tùy chọn)* | `eventbridge-rule.png` | EventBridge rule schedule cron `0 1 * * ? *` trỏ tới Lambda |
| 27 | `static/images/5-Workshop/5.8-Automation/` *(tùy chọn)* | `ses-email.png` | Email nhắc hạn trả nhận được (sách đến hạn 1–2 ngày hoặc quá hạn) |
| 28 | `static/images/5-Workshop/5.9-Live-Demo/` | `register-login.png` | Terminal: register + login qua ALB trả về user và JWT |
| 29 | `static/images/5-Workshop/5.9-Live-Demo/` | `admin-create-book.png` | Admin tạo sách (ví dụ Dế Mèn phiêu lưu ký – Tô Hoài) trả về bản ghi mới |
| 30 | `static/images/5-Workshop/5.9-Live-Demo/` | `borrow-return.png` | Luồng mượn/trả: POST /api/borrow (hạn 14 ngày), GET /api/borrow/me, trả sách → status returned |
| 31 | `static/images/5-Workshop/5.9-Live-Demo/` | `search-results.png` | Kết quả tìm kiếm `GET /api/books?q=tô+hoài` |
| 32 | `static/images/5-Workshop/5.10-Cleanup/` | `service-desired-zero.png` | ECS service scale Desired = 0, tasks dừng trước khi xoá |
| 33 | `static/images/5-Workshop/5.10-Cleanup/` | `no-resources-remain.png` | Kiểm chứng sau dọn dẹp: VPC / RDS / ECS / S3 không còn tài nguyên `library-*` |

## Quy trình sau khi chụp xong

1. Đặt từng file `.png` đúng tên, đúng thư mục trong `static/images/5-Workshop/<thư mục>/`.
2. `git add -A && git commit -m "Add workshop screenshots"` (thư mục sẽ `git add` mặc định — nếu thấy
   đường dẫn thừa/khác thì chỉnh lại cho khớp tên file trong bảng trên).
3. `git push origin main` — GitHub Actions tự build lại; ảnh hiện trên
   https://Thanhvan-nut1309.github.io/Thanhvanworkshop/5-workshop/ mà không cần sửa markdown.

> Lưu ý bảo mật: ảnh chụp console AWS có thể lộ **Account ID** (367764690039) — báo cáo đã công khai nội dung này, OK. Chú ý **che mật khẩu** RDS/đăng nhập nếu vô tình xuất hiện trong ảnh terminal.