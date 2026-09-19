---
title: "Blog 2"
date: 2026-05-01
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# XÂY DỰNG, TRIỂN KHAI VÀ VẬN HÀNH ỨNG DỤNG CONTAINER VỚI AWS FARGATE

Tóm tắt từ bài viết trên AWS Compute Blog, bài này trình bày quy trình hoàn chỉnh vận hành ứng dụng container bằng **Amazon ECS** và **AWS Fargate**, kèm pipeline CI/CD với **AWS CodeBuild** và **AWS CodePipeline** để tự động build, deploy và vận hành ứng dụng.

### Thành phần kiến trúc chính:

- **Amazon ECS Cluster & Task Definition:** cluster nhóm các workload; task definition là bản thiết kế mô tả image container, network mode `awsvpc` và yêu cầu CPU/memory của task.

- **AWS Fargate launch type:** Fargate chạy container mà không cần cấp/mở rộng EC2 instance — chỉ cần khai báo image, CPU và memory, Fargate cung cấp compute và tính phí theo giây.

- **Application Load Balancer & ECS service:** service giữ đúng số task mong muốn, đăng ký task vào ALB và trải task ra nhiều Availability Zone để đảm bảo tính sẵn sàng.

- **Pipeline CI/CD:** **AWS CodeBuild** compile ứng dụng và push image lên **Amazon ECR**; **AWS CodePipeline** tự động hóa vòng lặp build → deploy → vận hành.

### Lợi ích của kiến trúc:

- **Không quản lý hạ tầng:** không cần vận hành EC2 instance hay dung lượng cluster; Fargate cấp compute đúng theo nhu cầu của container.
- **Sẵn sàng cao:** ECS service duy trì số task mong muốn và trải task qua nhiều AZ phía sau load balancer.
- **Giao hàng tự động:** tích hợp CodePipeline biến mỗi thay đổi mã nguồn thành một release container đã được triển khai.

---

### Liên kết tham khảo:

- **Bài đăng Facebook:** [Nhóm AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj)
- **Bài viết AWS:** [Building, deploying, and operating containerized applications with AWS Fargate](https://aws.amazon.com/blogs/compute/building-deploying-and-operating-containerized-applications-with-aws-fargate)