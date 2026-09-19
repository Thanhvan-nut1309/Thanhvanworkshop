# WORKSHOP SCREENSHOTS — danh sách ảnh thao tác workshop

Danh sách 24 ảnh màn hình (screenshot) đã được gắn vào phần **5-Workshop** (EN + VI)
thông qua shortcode `{{< screenshot >}}`. Ảnh hiển thị trực tiếp trên trang báo cáo.

> Trạng thái: ✅ = đã gắn ảnh (file `.png` tồn tại trong `static/images/5-Workshop/`).
> 9 placeholder ảnh chưa có file đã được **bỏ khỏi nội dung** (không còn ô "Ảnh cần bổ sung" trên trang).

| # | Thư mục chứa ảnh | Tên file ảnh | Nội dung ảnh | Trạng thái |
|---|---|---|---|---|
| 1 | `static/images/5-Workshop/5.3-Networking/` | `vpc-wizard-config.png` | Màn hình Create VPC (VPC and more): tên `library-vpc`, 2 AZ, 2 public + 2 private subnet, NAT Gateway Regional | ✅ |
| 2 | `static/images/5-Workshop/5.3-Networking/` | `vpc-created.png` | Trang Your VPCs hiển thị `library-vpc` với State = Available | ✅ |
| 3 | `static/images/5-Workshop/5.3-Networking/` | `subnets-list.png` | Trang Subnets lọc theo `library-vpc`: 2 public + 2 private subnet | ✅ |
| 4 | `static/images/5-Workshop/5.3-Networking/` | `db-security-group.png` | Security group `library-db-sg` đã tạo, chưa có inbound rule | ✅ |
| 5 | `static/images/5-Workshop/5.4-Application-Services/` | `rds-create-config.png` | RDS Create database (Full configuration): Engine MySQL, Free tier, identifier `library-db`, initial database name `library_db` | ✅ |
| 6 | `static/images/5-Workshop/5.4-Application-Services/` | `rds-available.png` | Console RDS: `library-db` ở trạng thái Available, Public access = No, VPC = `library-vpc` | ✅ |
| 7 | `static/images/5-Workshop/5.4-Application-Services/` | `s3-bucket-created.png` | Console S3: bucket `library-covers-thanhvan-2026` với Block Public Access bật | ✅ |
| 8 | `static/images/5-Workshop/5.4-Application-Services/` | `secret-created.png` | Secrets Manager: secret `library-db-credentials` liên kết database `library-db` | ✅ |
| 9 | `static/images/5-Workshop/5.5-Containerize/` | `ecr-repository.png` | Trang ECR Repositories: repository private `library-management` | ✅ |
| 10 | `static/images/5-Workshop/5.5-Containerize/` | `docker-login-succeeded.png` | Terminal: lệnh đăng nhập ECR kết thúc bằng `Login Succeeded` | ✅ |
| 11 | `static/images/5-Workshop/5.5-Containerize/` | `image-pushed.png` | Terminal: `docker push` hoàn tất kèm digest — hoặc console ECR hiển thị image `latest` với sha256 digest | ✅ |
| 12 | `static/images/5-Workshop/5.6-Deploy/` | `task-role.png` | IAM role `library-ecs-task-role` gắn policy `SecretsManagerReadWrite` | ✅ |
| 13 | `static/images/5-Workshop/5.6-Deploy/` | `cluster.png` | ECS cluster `library-cluster` với hạ tầng Fargate | ✅ |
| 14 | `static/images/5-Workshop/5.6-Deploy/` | `task-definition-env.png` | Task definition `task-library-management` với 5 biến môi trường (PORT, USE_SECRETS_MANAGER, AWS_REGION, DB_SECRET_NAME, JWT_SECRET) | ✅ |
| 15 | `static/images/5-Workshop/5.6-Deploy/` | `service-alb.png` | ECS service + ALB/target group: health check `/health` port 3000, target group healthy | ✅ |
| 16 | `static/images/5-Workshop/5.6-Deploy/` | `health-alb.png` | Trình duyệt mở `/health` theo DNS name của ALB trả về `{"status":"ok"}` | ✅ |
| 17 | `static/images/5-Workshop/5.7-Database-Migration/` | `sg-inbound-3306.png` | `library-db-sg`: inbound rule MySQL/Aurora port 3306 từ My IP | ✅ |
| 18 | `static/images/5-Workshop/5.8-Automation/` *(tùy chọn)* | `lambda-function.png` | Lambda `library-due-date-reminder` đã tạo | ✅ |
| 19 | `static/images/5-Workshop/5.8-Automation/` *(tùy chọn)* | `eventbridge-rule.png` | EventBridge rule schedule cron `0 1 * * ? *` trỏ tới Lambda | ✅ |
| 20 | `static/images/5-Workshop/5.8-Automation/` *(tùy chọn)* | `ses-email.png` | Email nhắc hạn trả nhận được (sách đến hạn 1–2 ngày hoặc quá hạn) | ✅ |
| 21 | `static/images/5-Workshop/5.9-Live-Demo/` | `register-login.png` | Terminal: register + login qua ALB trả về user và JWT | ✅ |
| 22 | `static/images/5-Workshop/5.9-Live-Demo/` | `admin-create-book.png` | Admin tạo sách (ví dụ Dế Mèn phiêu lưu ký – Tô Hoài) trả về bản ghi mới | ✅ |
| 23 | `static/images/5-Workshop/5.9-Live-Demo/` | `borrow-return.png` | Luồng mượn/trả: POST /api/borrow (hạn 14 ngày), GET /api/borrow/me, trả sách → status returned | ✅ |
| 24 | `static/images/5-Workshop/5.9-Live-Demo/` | `search-results.png` | Kết quả tìm kiếm `GET /api/books?q=tô+hoài` | ✅ |

## Tổng kết

- ✅ **24/24** ảnh đã gắn, từ thư mục `E:\ảnh aws` (17/09/2026).
- Đã **bỏ tất cả placeholder** của 9 ảnh chưa chụp (3 ảnh 5.2-Foundation, 4 ảnh 5.7-Database-Migration, 2 ảnh 5.10-Cleanup) — trang không còn ô "Ảnh cần bổ sung".

## Nếu muốn thêm ảnh về sau

1. Chụp ảnh, đặt file `.png` đúng tên vào `static/images/5-Workshop/<thư mục>/`.
2. Chèn dòng shortcode vào đoạn tương ứng trong `content/5-Workshop/<thư mục>/_index.md` và `_index.vi.md`:
   `{{< screenshot src="/images/5-Workshop/<thư mục>/<ten-file>.png" lang="en|vi" caption="Mô tả ảnh" >}}`
3. `git add -A && git commit -m "..." && git push origin main` — GitHub Actions tự build và deploy.

> Lưu ý bảo mật: ảnh chụp console AWS có thể lộ **Account ID** (367764690039) — báo cáo đã công khai nội dung này, OK. Chú ý **che mật khẩu** RDS/đăng nhập nếu vô tình xuất hiện trong ảnh terminal.