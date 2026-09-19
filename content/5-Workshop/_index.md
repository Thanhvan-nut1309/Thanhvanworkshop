---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Fighting Game AWS Backend — Implementation Workshop

![Workshop screenshots](/images/5-Workshop/image1.png)

Hands-on steps for the **serverless + EC2 Spot** multiplayer game backend documented in my internship proposal. This section covers the infrastructure and CI/CD work I was responsible for on the [capstone project](https://github.com/Thanhvan-nut1309/ThanhVanWorkshop) repository.

**Region:** `ap-southeast-1`  
**Scope:** EC2 warm pool fleet, S3 static client hosting, IAM, GitHub OIDC, CodeDeploy (Lambda + EC2), async match analytics, private MatchMaker VPC, live game demo, and documented teardown.

#### Contents

1. [Workshop overview](5.1-Workshop-overview/)
2. [EC2 game server fleet & warm pool](5.2-EC2-Fleet/)
3. [S3 static client hosting](5.3-S3-Hosting/)
4. [IAM roles & policies](5.4-IAM/)
5. [GitHub OIDC → AWS (CI authentication)](5.5-GitHub-OIDC/)
6. [CodeDeploy — Lambda & EC2 CI/CD](5.6-CodeDeploy/)
7. [Async post-match processing](5.7-Async-Processing/)
8. [VPC — private MatchMaker (no NAT)](5.8-VPC-MatchMaker/)
9. [Game demo](5.9-Game-Demo/)
10. [Resource cleanup (documented)](5.10-Cleanup/)
