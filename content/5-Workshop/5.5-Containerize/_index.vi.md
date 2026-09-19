---
title: "Container hóa — Amazon ECR"
date: 2026-08-24
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

## Mục tiêu

Đẩy image Docker đã test ở local lên **Amazon ECR** (Elastic Container Registry) để ECS có thể pull và chạy. ECR là container registry riêng tư trong chính tài khoản AWS của mình.

## Bước 1 — Tạo ECR repository

1. **Amazon ECR → Repositories → Create repository.**
2. **Visibility settings:** `Private` (mặc định).
3. **Repository name:** `library-management`.
4. Tạo repository.

## Bước 2 — Đăng nhập Docker vào ECR

Mở terminal trong thư mục project (nơi có `Dockerfile`), chạy:

```bash
aws ecr get-login-password --region ap-southeast-2 \
  | docker login --username AWS --password-stdin 367764690039.dkr.ecr.ap-southeast-2.amazonaws.com
```

Kết quả cuối phải là `Login Succeeded`.

## Bước 3 — Build, tag và push image

```bash
# build từ Dockerfile của project (chú ý dấu chấm ở cuối)
docker build -t library-management .

# tag bằng URI của ECR
docker tag library-management:latest \
  367764690039.dkr.ecr.ap-southeast-2.amazonaws.com/library-management:latest

# push (mất vài phút nếu mạng chậm)
docker push 367764690039.dkr.ecr.ap-southeast-2.amazonaws.com/library-management:latest
```

Dòng cuối báo digest và dung lượng image — image đã nằm trong ECR.

## Bước 4 — Kiểm tra

Trong **ECR → Repositories → library-management**, image `latest` xuất hiện với `sha256` digest khớp với bản build local.

## Kết quả mong đợi

- ECR repository riêng tư `library-management` chứa image đã test
- Không có image nào trong registry công khai
- ECS task definition sẽ trỏ trực tiếp vào URI image ECR

## Xử lý sự cố

| Vấn đề | Kiểm tra |
|-------|--------|
| Báo `no basic auth credentials` | Docker login vào ECR thất bại — chạy lại bước 2 và xác nhận `Login Succeeded` |
| Push chậm/kẹt khi mạng yếu | Bình thường ở lần push đầu (image layers); chạy lại — layer đã upload sẽ được bỏ qua |
| Sai registry/account | Đối chiếu account ID `367764690039` và region với lệnh push hiển thị trong ECR console |