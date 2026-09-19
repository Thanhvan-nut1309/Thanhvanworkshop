---
title: "Dọn dẹp"
date: 2026-09-02
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

Sau khi capstone đã deploy và demo, em tài liệu hóa quy trình teardown toàn bộ stack **theo thứ tự phụ thuộc** để không để lại gì và không phát sinh phí theo giờ (đặc biệt **NAT Gateway**).

## Thứ tự dọn dẹp

| # | Tài nguyên | Các bước |
|---|----------|-------|
| 1 | **ECS service + cluster** | Giảm service xuống `Desired = 0`, rồi xóa service; xóa cluster `library-cluster`. |
| 2 | **ALB** | Xóa load balancer (sẽ kéo theo xóa listener và target group). |
| 3 | **ECR repository** | Xóa repository `library-management` (xóa image trước, hoặc force-delete). |
| 4 | **Amazon RDS** | Xóa instance `library-db` (bỏ qua final snapshot nếu không cần) và đợi xóa xong. |
| 5 | **Secrets Manager** | Xóa secret `library-db-credentials`. |
| 6 | **Amazon S3** | Làm rỗng bucket `library-covers-thanhvan-2026`, rồi xóa bucket. |
| 7 | **NAT Gateway** ⚠️ | Xóa trước tiên — đây là resource **tính phí theo giờ**. Giải phóng Elastic IP nếu có gắn. |
| 8 | **VPC** | Xóa `library-vpc` — kéo theo xóa subnets, route tables, internet gateway và các security group còn lại. |
| 9 | **IAM roles** | Xóa `library-ecs-task-role` và các role tạm đã tạo cho task. |

## Kiểm chứng sau dọn dẹp

- [ ] **ECS** — không còn cluster, service hay task definition
- [ ] **EC2** — không còn load balancer nào (xem "Load Balancers" trong EC2)
- [ ] **RDS** — không còn instance `library-db` ở trạng thái nào
- [ ] **S3** — bucket đã xóa
- [ ] **VPC** — không còn `library-vpc`
- [ ] **IAM** — role `library-ecs-task-role` đã xóa
- [ ] **Cost Explorer / Billing** — không còn chi phí tồn đọng từ stack

> Mật khẩu và thông tin kết nối database chỉ nằm trong Secrets Manager — đã xóa ở đây — nên không còn credential nào sót lại ở bất kỳ đâu.