---
title: "Tổng quan workshop"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## Mục đích

Workshop ghi lại các bước triển khai AWS cho **backend game đối kháng live-service**: người chơi xác thực qua Cognito, matchmaking qua API Gateway + Lambda, kết nối **EC2 Spot** cổng **9000**, và kích hoạt **analytics bất đồng bộ** khi trận kết thúc.

| Flow | Thành phần | Phần workshop của em |
|------|------------|----------------------|
| **A** | Cognito, S3 assets | S3 static hosting (client) |
| **R** | API GW, MatchMaker Lambda, DynamoDB, EC2 ASG | Fleet, IAM, VPC private Lambda |
| **C** | GitHub Actions, CodeDeploy | OIDC, deploy Lambda + EC2 |
| **E** | DynamoDB Streams, MatchAnalytics Lambda | Thiết lập async processing |

Phần đồng đội làm (Cognito ban đầu, API Gateway, code MatchMaker giai đoạn đầu) không nằm trong phạm vi này.

## Điều kiện tiên quyết

- Tài khoản AWS quyền admin tại `ap-southeast-1`
- Game server binary chạy cổng **TCP 9000**
- Repository GitHub: `Thanhvan-nut1309/ThanhVanWorkshop`

## Tên tài nguyên tham chiếu

| Tài nguyên | Tên |
|------------|-----|
| EC2 tag | `Role=FightingGameServer` |
| S3 bucket | `fighting-game-assets-508768431157` |
| ASG | `FightingGameServerASG` |
| CodeDeploy EC2 | `FightingGameServerDeploy` / `FightingGameServer-fleet` |
| DynamoDB | `MatchmakingQueue`, `ActiveMatches`, `MatchAnalytics` |
| Instance profile | `FightingGameServerInstanceRole` |

## Thứ tự thực hiện

Làm **5.2 → 5.8** theo thứ tự. Mục **5.9** demo game trực tiếp. Mục **5.10** là hướng dẫn teardown có screenshot (hủy trước khi xác nhận xóa).

## Checklist xác minh

- [ ] ASG warm pool healthy, tag đúng
- [ ] S3 website load được client
- [ ] GitHub Actions deploy qua OIDC thành công
- [ ] CodeDeploy Lambda + EC2 thành công
- [ ] Match kết thúc có dữ liệu trong `MatchAnalytics`
- [ ] MatchMaker Lambda chạy private subnet + VPC endpoints
