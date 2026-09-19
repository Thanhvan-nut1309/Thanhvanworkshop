---
title: "Bản đề xuất"
date: 2026-05-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Hệ thống quản lý thư viện Cloud-Native trên AWS

## Ứng dụng web quản lý sách, độc giả và mượn trả — mở rộng linh hoạt, bảo mật và vận hành tự động

---

### 1. Tóm tắt điều hành

Dự án trình bày kiến trúc backend cloud-native cho **Hệ thống quản lý thư viện (Library Management System)** xây dựng hoàn toàn trên Amazon Web Services (AWS). Nhân viên thư viện quản lý được sách, độc giả và lượt mượn; độc giả tra cứu danh mục, mượn sách và theo dõi hạn trả qua trình duyệt web — vận hành trên **Amazon ECS Fargate**, **Amazon RDS for MySQL**, **Amazon S3** và **AWS Secrets Manager**.

Thay vì chạy trên máy chủ ảo luôn bật, ứng dụng chạy container **Node.js/Express** trên **AWS Fargate** (compute serverless cho container) phía sau **Application Load Balancer**, còn database MySQL được đặt trong **private subnet** để không bao giờ lộ ra internet. Thiết kế tuân theo các nguyên tắc **least-privilege**, **không hardcode secret** và **mạng nội bộ riêng** được nhấn mạnh trong chương trình thực tập First Cloud AI Journey (FCAJ).

Điểm nhấn của dự án là **tự động nhắc hạn trả sách**: **Amazon EventBridge** lên lịch mỗi ngày, kích hoạt **AWS Lambda** quét lượt mượn và gửi email qua **Amazon SES** cho độc giả sắp đến hạn hoặc quá hạn. Phần tự động hóa serverless này vừa đáng tin cậy vừa gần như không tốn chi phí.

---

### 2. Tuyên bố vấn đề

#### Vấn đề là gì?

Các thư viện nhỏ, trường học thường quản lý mượn/trả bằng bảng tính hoặc phần mềm để bàn. Điều này gây ra ba vấn đề thực tế:

- **Theo dõi thủ công, dễ sai** — hạn trả tính tay, sách quá hạn hay bị quên, mất sách và doanh thu.
- **Không truy cập được từ xa** — hệ thống nằm trên một máy, độc giả ngoài thư viện không dùng được, không có sao lưu hay lịch sử kiểm toán.
- **Khó mở rộng và bảo trì** — máy chủ luôn bật lãng phí tiền khi ít người dùng; triển khai, nâng cấp và khôi phục sự cố vất vả.

#### Giải pháp

Ứng dụng web cloud-native với nguyên tắc thiết kế đơn giản: **giữ database riêng tư, giữ credential ngoài mã nguồn, và tự động hóa các việc lặp lại**.

- Backend **Node.js/Express** (xác thực JWT, phân quyền `admin`/`user`, CRUD sách, tìm kiếm, mượn/trả với **hạn 14 ngày**) chạy trong container Docker trên **AWS Fargate**.
- **MySQL trên Amazon RDS** lưu dữ liệu quan hệ (`books`, `users`, `borrow_records`) trong **private subnet**, chỉ ứng dụng truy cập được.
- **Amazon S3** lưu ảnh bìa sách; **AWS Secrets Manager** lưu thông tin đăng nhập database, được ứng dụng đọc lúc chạy.
- Pipeline serverless **EventBridge → Lambda → SES** tự động email nhắc hạn trả — điểm nhấn của dự án.
- **AWS CodeBuild** (cùng **ECR**) tự build và đẩy image mỗi lần commit; **Amazon CloudWatch** giám sát log, metric và cảnh báo.

#### Lợi ích và ROI

- **Luôn sẵn sàng, chi phí gần như bằng 0**: Fargate chỉ tính giờ theo vCPU; RDS free tier và các dịch vụ serverless giữ hóa đơn hằng tháng ở mức vài USD.
- **Bảo mật theo thiết kế**: database **không có public access**, credential không xuất hiện trong code hay image, IAM theo nguyên tắc quyền tối thiểu.
- **Tự động hóa tiết kiệm việc thật**: email nhắc hạn thay cho việc kiểm tra thủ công hằng ngày; CI/CD loại bỏ triển khai tay.
- **Di động và tái lập**: toàn bộ stack dùng Docker; cùng một image chạy được ở local, trong CI và trên AWS.

---

### 3. Kiến trúc giải pháp

![Kiến trúc Hệ thống quản lý thư viện](/images/2-Proposal/architecture.svg)

Kiến trúc là một ứng dụng web **ba lớp** điển hình, được củng cố theo best practice của AWS.

#### Các luồng kiến trúc

##### **Luồng 1: Đường đi yêu cầu người dùng (Web)**

- Độc giả/thủ thư mở ứng dụng web và đăng nhập (backend cấp JWT, phân quyền `admin`/`user`).
- Yêu cầu tới **Application Load Balancer** trong **public subnet**, được chuyển tới container **ECS Fargate** (Node.js/Express, cổng **3000**) trong **private subnet**.
- Backend đọc/ghi **Amazon RDS MySQL (`library_db`)** qua các bảng `books`, `users`, `borrow_records`. Thao tác mượn sách tạo bản ghi với `due_date = borrow_date + 14 ngày` trong một transaction, kiểm tra tồn kho trước.

##### **Luồng 2: Xử lý thông tin đăng nhập**

- Không có mật khẩu database nào trong code, image hay file cấu hình.
- Lúc khởi động, container gọi **AWS Secrets Manager** (secret `library-db-credentials`) qua role `library-ecs-task-role` để lấy thông tin kết nối.
- Việc chạy `schema.sql` một lần được thực hiện qua quy tắc inbound tạm thời (mở rồi đóng ngay), được ghi lại trong mục workshop.

##### **Luồng 3: Tự động hóa (Nhắc hạn trả)**

- **Amazon EventBridge** chạy rule **cron hằng ngày**.
- **AWS Lambda** quét `borrow_records` tìm sách sắp hạn (1–2 ngày) hoặc quá hạn, chuyển danh sách cho **Amazon SES**.
- Độc giả tự động nhận email nhắc hạn — hoàn toàn serverless, không thêm máy chủ, chi phí gần như bằng 0.

##### **Luồng 4: Phân phối (CI/CD)**

- Nhà phát triển push code lên **GitHub**; **AWS CodeBuild** build và test image Docker rồi đẩy lên **Amazon ECR** (`library-management`).
- Dịch vụ ECS được cập nhật lên bản image mới — triển khai lặp lại được, có lịch sử kiểm toán, không cần đăng nhập vào máy chủ.

#### Dịch vụ AWS sử dụng

- **Amazon ECS (Fargate)**: runtime container serverless cho ứng dụng backend.
- **Application Load Balancer**: cổng vào HTTP(S), health check tại `/health`.
- **Amazon RDS for MySQL**: lưu trữ quan hệ `books`, `users`, `borrow_records`.
- **Amazon S3**: lưu ảnh bìa sách bền vững.
- **AWS Secrets Manager**: lưu thông tin đăng nhập database an toàn.
- **AWS Lambda + Amazon EventBridge + Amazon SES**: gửi email nhắc hạn theo lịch (event-driven).
- **Amazon ECR + AWS CodeBuild**: registry container và pipeline CI.
- **Amazon CloudWatch**: log, metric và cảnh báo.
- **VPC (public/private subnet) + NAT Gateway**: cô lập mạng và kiểm soát egress.

---

### 4. Triển khai kỹ thuật

#### Các giai đoạn triển khai

1. **Giai đoạn 1: Nền tảng ứng dụng (Tuần 1–2)**  
   Xây backend Node.js/Express — xác thực JWT với phân quyền `admin`/`user`, CRUD sách kèm tìm kiếm, API mượn/trả với hạn 14 ngày và kiểm tra tồn kho — cùng `schema.sql` và `Dockerfile`. Container hóa stack với `docker-compose` (app + MySQL) và chạy thử local.
2. **Giai đoạn 2: Mạng (Tuần 3)**  
   Tạo `library-vpc` với hai public subnet và hai private subnet, Internet Gateway và NAT Gateway ("Regional") qua VPC wizard.
3. **Giai đoạn 3: Dịch vụ ứng dụng (Tuần 3–4)**  
   Dựng Amazon RDS MySQL (`library-db`, free tier, private subnet), bucket S3 chứa ảnh bìa (`library-covers-thanhvan-2026`) và secret Secrets Manager (`library-db-credentials`).
4. **Giai đoạn 4: Containerize & Triển khai (Tuần 4–5)**  
   Đẩy image lên Amazon ECR, tạo cluster Fargate, task definition và service phía sau ALB, migrate schema lên RDS và kiểm thử end-to-end.
5. **Giai đoạn 5: Tự động hóa, Giám sát & Dọn dẹp (Tuần 5)**  
   Thiết kế pipeline EventBridge–Lambda–SES nhắc hạn, bật CloudWatch, tài liệu hóa quy trình dọn dẹp theo thứ tự phụ thuộc và hoàn thiện phần workshop.

#### Yêu cầu kỹ thuật

- **Stack ứng dụng**: Node.js 20 + Express + MySQL (image Docker `node:20-alpine`).
- **Database**: MySQL 8 — các bảng `books`, `users`, `borrow_records` (schema trong `schema.sql`).
- **Mạng**: VPC với public subnet (ALB) và private subnet (ECS + RDS); NAT Gateway cho cập nhật và gọi ra ngoài.
- **Secrets**: `USE_SECRETS_MANAGER=true` lúc chạy; credential lấy từ Secrets Manager, không hardcode.
- **Bảo mật**: RDS `Public access = No`; security group database giới hạn; IAM task role quyền tối thiểu.

---

### 5. Timeline & Milestone

| Tuần | Milestone |
| --- | --- |
| **Tuần 1–2** | Nền tảng: ứng dụng local chạy được (auth, sách, mượn/trả) trong Docker |
| **Tuần 3** | Tạo VPC; dựng RDS, bucket S3 và Secrets Manager |
| **Tuần 4** | Đẩy image lên ECR; service ECS Fargate + ALB hoạt động |
| **Tuần 5** | Migrate schema lên RDS; thiết kế tự động hóa & giám sát; tài liệu dọn dẹp; nộp báo cáo |

---

### 6. Ước tính ngân sách & tối ưu chi phí

- **Compute**: ECS Fargate tính phí theo gb/vCPU-giờ khi chạy — phù hợp lưu lượng thư viện nhỏ.
- **Database**: RDS MySQL free tier (`db.t3.micro`, 20 GB) đủ cho giai đoạn demo; sau đó tính theo giờ.
- **Serverless**: Lambda và EventBridge tốn chưa tới vài xu mỗi ngày; SES có free tier hào phóng cho lượng email nhỏ.
- **Mạng**: NAT Gateway là khoản tốn duy nhất chạy liên tục (~$0.045/giờ) — sẽ xoá khi dọn dẹp sau demo.
- **Storage**: S3 standard tier cho ảnh bìa — chi phí không đáng kể ở quy mô này.

---

### 7. Đánh giá rủi ro

| Rủi ro | Tác động | Xác suất | Giảm thiểu |
| --- | --- | --- | --- |
| **RDS bị bật public access quên tắt** | Cao | Thấp | Tắt ngay sau khi migrate một lần; kiểm tra lại khi dọn dẹp |
| **Credential mất/hết hạn** | Trung bình | Thấp | Chỉ lưu trong Secrets Manager; xoay vòng bằng cách tạo lại secret; không commit lên Git |
| **Quên phí NAT Gateway** | Trung bình | Trung bình | Ghi rõ trong checklist dọn dẹp; AWS Budgets cảnh báo chi phí |
| **Container chạy nhưng không kết nối được RDS** | Cao | Trung bình | Đã xử lý bằng cách kiểm tra biến môi trường trong task definition (ghi lại trong workshop 5.6) |
| **Email nhắc hạn cấu hình sai** | Thấp | Thấp | SES sandbox giới hạn email đã xác minh trước, sau đó mở production; theo dõi bằng CloudWatch |

---

### 8. Kết quả mong đợi

- **Ứng dụng web chạy được, triển khai được** — thủ thư và độc giả tra cứu sách, mượn/trả và tự động nhận email nhắc hạn.
- **Bảo mật mặc định** — database riêng tư, secret quản lý tập trung, IAM quyền tối thiểu, không lộ credential.
- **Tiết kiệm và mở rộng** — compute serverless và dịch vụ on-demand giữ hóa đơn nhỏ, hệ thống phát triển cùng quy mô thư viện.
- **Bài học thực tế** — dự án phản ánh đầy đủ hành trình FCAJ: mạng, container, dịch vụ managed, serverless, CI/CD và giám sát.