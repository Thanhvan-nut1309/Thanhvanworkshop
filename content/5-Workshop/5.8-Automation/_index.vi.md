---
title: "Lộ trình tự động hóa & giám sát"
date: 2026-08-29
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

## Mục tiêu

Thêm **tính năng điểm nhấn** của dự án — pipeline **nhắc hạn trả hoàn toàn serverless** — cùng giám sát cơ bản. Đây là phần anh hướng dẫn đánh giá cao nhất: thể hiện cả **serverless** lẫn **event-driven**, và không phải ai cũng làm.

## Thiết kế — nhắc hạn trả hằng ngày

```
Amazon EventBridge (cron, hằng ngày)
        │  gọi
        ▼
AWS Lambda ──quét──► Amazon RDS (borrow_records)
        │  dựng danh sách người nhận
        ▼
Amazon SES ──email──► độc giả (hạn trong 1–2 ngày, hoặc quá hạn)
```

## Bước 1 — Tạo Lambda function

1. **Lambda → Create function → Author from scratch.**
2. Name: `library-due-date-reminder`; Runtime: Node.js (khớp project).
3. Execution role: cho phép đọc (kiểu `dynamodb`/tùy helper) trên RDS và `ses:SendEmail`. Project này kết nối MySQL bằng chính secret Secrets Manager (`library-db-credentials`).
4. Khung logic:

```js
// pseudocode
const due = await findDueRecords(secret);   // due_date trong 1-2 ngày HOẶC status='overdue'
for (const r of due) {
  await ses.sendEmail(r.email, `"${r.title}" sắp đến hạn ${r.due_date}`);
}
```

## Bước 2 — Lên lịch bằng EventBridge

1. **Amazon EventBridge → Rules → Create rule**, event source **Schedule**.
2. Cron expression (UTC): `0 1 * * ? *` — chạy **01:00 hằng ngày**.
3. Target: Lambda function ở trên.
4. Tạo rule và kiểm thử bằng nút **Invoke** thủ công trong Lambda console.

## Bước 3 — Kiểm chứng email nhắc hạn

1. Mượn một cuốn sách có `due_date` nằm trong cửa sổ (hoặc tạm đặt một bản ghi sang `overdue`).
2. Chạy Lambda thủ công (hoặc đợi lịch).
3. Xem **CloudWatch Logs** của Lambda và xác nhận người nhận nhận được email SES.

## Bước 4 — Giám sát

1. **CloudWatch → Log groups**: log task ECS (`/ecs/task-library-management`), log Lambda.
2. **CloudWatch → Alarms**: vd cảnh báo khi ALB target trả `5xx` liên tục 5 phút, hoặc CPU service ECS vượt ngưỡng.
3. **AWS Budgets** (tùy chọn): cảnh báo vài USD cho NAT Gateway và phí RDS theo giờ trong kỳ thực tập.

## Bước 5 — Mở rộng CI/CD (tùy chọn)

Nối deploy vào tự động hóa: **GitHub → AWS CodeBuild → Amazon ECR → ECS** để mỗi lần `git push` lên `main` tự build và redeploy image `library-app` (cùng pattern đã dùng để deploy site báo cáo này bằng GitHub Actions + GitHub Pages).

### Ảnh cần bổ sung cho phần này (tùy chọn — nếu bạn triển khai pipeline)

{{< screenshot src="/images/5-Workshop/5.8-Automation/lambda-function.png" lang="vi" caption="Console Lambda: hàm library-due-date-reminder đã tạo." >}}

{{< screenshot src="/images/5-Workshop/5.8-Automation/eventbridge-rule.png" lang="vi" caption="EventBridge rule với event source Schedule, cron 0 1 * * ? * trỏ tới Lambda function." >}}

{{< screenshot src="/images/5-Workshop/5.8-Automation/ses-email.png" lang="vi" caption="Email nhắc hạn trả nhận được (độc giả có sách đến hạn trong 1-2 ngày hoặc quá hạn)." >}}

## Kết quả mong đợi

- Độc giả tự động nhận email nhắc hạn — không cần kiểm tra thủ công mỗi ngày
- Pipeline serverless chi phí gần như bằng 0 (Lambda + EventBridge + SES)
- CloudWatch cho cái nhìn toàn diện về app, DB và Lambda

## Trạng thái

| Hạng mục | Trạng thái |
|------|--------|
| Thiết kế EventBridge + Lambda + SES | Đã tài liệu ở trên (mở rộng giai đoạn cuối) |
| Giám sát CloudWatch ECS/RDS | Đã bật trong lúc triển khai |
| Pipeline CI/CD rebuild | Khuyến nghị bước tiếp theo sau báo cáo |