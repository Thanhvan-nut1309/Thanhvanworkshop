---
title: "Worklog Tuần 5"
date: 2026-08-29
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu tuần 5:

* Hoàn thành đồ án capstone (xử lý bất đồng bộ, tăng cường VPC).
* Hoàn thành nội dung lab workshop và deploy website báo cáo thực tập.
* Hoàn thiện và nộp báo cáo thực tập.

**Thời gian:** 29/08/2026 – 04/09/2026

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | ------------ | --------------- | -------------- |
| 2 | - Triển khai xử lý bất đồng bộ sau trận: DynamoDB Streams → MatchAnalytics Lambda <br> - Cài đặt và cấu hình CodeDeploy agent trên EC2 fleet | 31/08/2026 | 31/08/2026 | |
| 3 | - Chuyển MatchMaker Lambda sang private subnet với VPC endpoints (không NAT) <br> - Kiểm thử tích hợp cuối: matchmaking → gameplay → kết thúc trận → analytics | 01/09/2026 | 01/09/2026 | |
| 4 | - Hoàn thành các phần workshop; tài liệu hóa quy trình dọn dẹp tài nguyên kèm screenshot <br> - Thiết lập site báo cáo Hugo từ template FCAJ; tùy chỉnh `config.toml` | 02/09/2026 | 02/09/2026 | |
| 5 | - Điền nội dung worklog, proposal và workshop vào Hugo <br> - Cấu hình GitHub Actions (`hugo.yml`) để deploy lên GitHub Pages | 03/09/2026 | 03/09/2026 | |
| 6 | - Rà soát và hoàn thiện tất cả phần báo cáo (worklog, proposal, workshop, tự đánh giá) <br> - Viết bài thu hoạch sự kiện <br> - Gửi link báo cáo cho giảng viên trường và cán bộ hướng dẫn công ty | 04/09/2026 | 04/09/2026 | |

### Kết quả đạt được tuần 5:

* Triển khai pipeline async Flow E: ActiveMatches DynamoDB Stream → MatchAnalytics Lambda → bảng analytics.
* Cài đặt và cấu hình CodeDeploy agent trên game server instance; tạo deployment group EC2.
* Chuyển MatchMaker sang private subnet với DynamoDB gateway và EC2/CloudWatch interface endpoints.
* Xác minh vòng đời game đầy đủ: login → queue → match → WebSocket gameplay → ghi kết quả vào DynamoDB.
* Hoàn thành tất cả bài viết lab workshop và tài liệu hóa screenshots quy trình dọn dẹp.
* Fork và tùy chỉnh template báo cáo thực tập Hugo cho cá nhân.
* Trỏ repository về `ThanhVan-nut1309/ThanhVanWorkshop` với `baseURL` và author đúng.
* Viết bài thu hoạch sự kiện và hoàn thành các phần tự đánh giá, chia sẻ góp ý.
* Deploy thành công website báo cáo qua GitHub Actions lên GitHub Pages và nộp báo cáo cuối kỳ.