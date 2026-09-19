---
title: "Triển khai — ECS Fargate + ALB"
date: 2026-08-26
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

## Mục tiêu

Chạy backend trong container trên **Amazon ECS với AWS Fargate** (compute serverless cho container) phía sau **Application Load Balancer**, và cho container đọc thông tin đăng nhập database từ **Secrets Manager** lúc chạy.

## Bước 1 — Tạo IAM task role

1. **IAM → Roles → Create role.** Trusted entity type: **AWS service**; use case: **Elastic Container Service → Elastic Container Service Task** (không phải "Elastic Container Service" thường).
2. Gắn policy **`SecretsManagerReadWrite`** (app chỉ dùng phần đọc — `GetSecretValue` — trong code).
3. Role name: `library-ecs-task-role`. Tạo.

## Bước 2 — Tạo cluster

1. **ECS → Clusters → Create cluster.**
2. Name: `library-cluster`; Infrastructure: **AWS Fargate (serverless)** (không phải quản lý EC2 instance).
3. Tạo.

## Bước 3 — Tạo task definition

1. **ECS → Task definitions → Create new task definition**, dùng **image ECR** `367764690039.dkr.ecr.ap-southeast-2.amazonaws.com/library-management:latest`.
2. **Task role:** `library-ecs-task-role`.
3. Container name: `Main` (theo tạo sẵn). Port mapping: container port `3000` (HTTP).
4. Thêm environment variables:

| Tên | Giá trị |
|------|-------|
| `PORT` | `3000` |
| `USE_SECRETS_MANAGER` | `true` |
| `AWS_REGION` | `ap-southeast-2` |
| `DB_SECRET_NAME` | `library-db-credentials` |
| `JWT_SECRET` | (chuỗi random tự đặt) |

> Chúng ta **không** ghi mật khẩu database vào các biến này — app tự đọc từ Secrets Manager khi `USE_SECRETS_MANAGER=true`.

5. Tạo task definition.

## Bước 4 — Tạo service + ALB

1. Từ **Cluster → Services → Create service**, chọn task definition vừa tạo.
2. **Deployment:** Default (Rolling update).
3. **Networking:** gắn service vào **private subnets** của `library-vpc`; security group cho phép inbound **3000** từ load balancer.
4. **Load balancing:** tạo mới **Application Load Balancer**, listener **80/443**, target group health check **`/health`** cổng **3000**.
5. Tạo service và đợi tasks sang **Running** và target group **healthy**.

## Bước 5 — Kiểm tra /health qua ALB

Mở **ALB DNS name** trong trình duyệt (vd `http://library-alb-....elb.amazonaws.com/health`). Kết quả khỏe mạnh là `{"status":"ok"}`.

### Ảnh minh hoạ cho phần này

{{< screenshot src="/images/5-Workshop/5.6-Deploy/task-role.png" lang="vi" caption="Console IAM: role library-ecs-task-role gắn policy SecretsManagerReadWrite." >}}

{{< screenshot src="/images/5-Workshop/5.6-Deploy/cluster.png" lang="vi" caption="Trang ECS Clusters hiển thị library-cluster với hạ tầng Fargate." >}}

{{< screenshot src="/images/5-Workshop/5.6-Deploy/task-definition-env.png" lang="vi" caption="Task definition task-library-management với 5 biến môi trường (PORT, USE_SECRETS_MANAGER, AWS_REGION, DB_SECRET_NAME, JWT_SECRET)." >}}

{{< screenshot src="/images/5-Workshop/5.6-Deploy/service-alb.png" lang="vi" caption="ECS service với Application Load Balancer và target group: health check /health port 3000, target group healthy." >}}

{{< screenshot src="/images/5-Workshop/5.6-Deploy/health-alb.png" lang="vi" caption="Trình duyệt mở /health theo DNS name của ALB trả về status ok — đây cũng là endpoint load balancer dùng để check." >}}

## Câu chuyện debug — app fallback về `localhost:3306`

Triệu chứng: ALB bắt đầu trả lời, nhưng app vẫn cố kết nối `localhost:3306` thay vì RDS — tức `USE_SECRETS_MANAGER` chưa có hiệu lực, code quay về connection string mặc định.

Nguyên nhân gốc: các environment variables **không được lưu vào task definition revision đang chạy**.

Cách sửa:
1. Trong **ECS → Task definitions → task-library-management → revision → Container: Main → "Environment and secrets"**, xác nhận đủ năm biến ở trên thực sự tồn tại.
2. Tạo **revision mới** với đầy đủ biến, rồi **Cluster → Services → (service) → Update service → Revision** chọn revision mới và cập nhật.

Khi task khởi động lại với `USE_SECRETS_MANAGER=true`, app đọc `library-db-credentials` từ Secrets Manager và kết nối được RDS.

## Kiểm chứng

- [ ] Tasks của ECS service ở trạng thái **Running**
- [ ] ALB target group health check **healthy**
- [ ] `GET /health` qua ALB trả `{"status":"ok"}`

## Xử lý sự cố

| Vấn đề | Hành động |
|-------|--------|
| Task restart liên tục (`Stopped (reason)`) | Kiểm tra **stopped reason** và CloudWatch logs: số lượng replica, sai URI image, hay thiếu task role |
| Health check fail | Xác nhận port mapping và container port; xem log app; `/health` phải trả lời trong container |
| App kết nối `localhost:3306` | `USE_SECRETS_MANAGER` thiếu/false trong task definition — tạo lại revision với khối env ở trên |