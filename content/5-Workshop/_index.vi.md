---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Workshop: Fighting Game AWS Backend — Triển khai thực tế

![Ảnh chụp workshop](/images/5-Workshop/image1.png)

Các bước thực hành cho backend game multiplayer **serverless + EC2 Spot** đã mô tả trong proposal thực tập. Phần này ghi lại hạ tầng và CI/CD em phụ trách trên dự án [capstone](https://github.com/Thanhvan-nut1309/ThanhVanWorkshop).

**Region:** `ap-southeast-1`  
**Phạm vi:** EC2 warm pool fleet, S3 static client hosting, IAM, GitHub OIDC, CodeDeploy (Lambda + EC2), async match analytics, VPC private MatchMaker, demo game trực tiếp và quy trình teardown có tài liệu.

#### Nội dung

1. [Tổng quan workshop](5.1-Workshop-overview/)
2. [EC2 game server fleet & warm pool](5.2-EC2-Fleet/)
3. [S3 static client hosting](5.3-S3-Hosting/)
4. [IAM roles & policies](5.4-IAM/)
5. [GitHub OIDC → AWS (xác thực CI)](5.5-GitHub-OIDC/)
6. [CodeDeploy — Lambda & EC2 CI/CD](5.6-CodeDeploy/)
7. [Async post-match processing](5.7-Async-Processing/)
8. [VPC — private MatchMaker (không NAT)](5.8-VPC-MatchMaker/)
9. [Game demo](5.9-Game-Demo/)
10. [Dọn dẹp tài nguyên (có tài liệu)](5.10-Cleanup/)
