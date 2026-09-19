---
title: "Workshop overview"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## Purpose

This workshop documents the AWS implementation steps for a **live-service fighting game backend**: players authenticate via Cognito, matchmake through API Gateway + Lambda, connect to **EC2 Spot** game servers on port **9000**, and trigger **async analytics** when matches finish.

The architecture follows the proposal flows:

| Flow | Components | My workshop coverage |
|------|------------|---------------------|
| **A** | Cognito, S3 assets | S3 static hosting (client bundle) |
| **R** | API GW, MatchMaker Lambda, DynamoDB, EC2 ASG | Fleet, IAM, VPC private Lambda |
| **C** | GitHub Actions, CodeDeploy | OIDC, Lambda + EC2 deploy |
| **E** | DynamoDB Streams, MatchAnalytics Lambda | Async processing setup |

Teammate-owned items (initial Cognito, API Gateway wiring, first MatchMaker Lambda code) are out of scope here.

## Prerequisites

- AWS account with admin access in `ap-southeast-1`
- A working game server binary listening on **TCP 9000**
- GitHub repository: `Thanhvan-nut1309/ThanhVanWorkshop`
- AWS CLI configured locally (optional, for verification)

## Resource naming reference

| Resource | Name / pattern |
|----------|----------------|
| EC2 tag | `Role=FightingGameServer` |
| S3 bucket | `fighting-game-assets-508768431157` |
| ASG | `FightingGameServerASG` |
| CodeDeploy (EC2) | `FightingGameServerDeploy` / `FightingGameServer-fleet` |
| CodeDeploy (Lambda) | `FightingGameMatchmakerDeploy` |
| DynamoDB | `MatchmakingQueue`, `ActiveMatches`, `MatchAnalytics` |
| Lambda | `FightingGameMatchmaker`, `FightingGameMatchAnalytics` |
| Instance profile | `FightingGameServerInstanceRole` |

## Workshop order

Complete sections **5.2 → 5.8** in sequence—later steps depend on earlier IAM, fleet, and networking. Section **5.9** demonstrates the live game client. Section **5.10** is a screenshot-based teardown guide for the internship report (cancel before confirming deletes).

## Verification checklist

After all sections:

- [ ] ASG warm pool has healthy instances tagged `Role=FightingGameServer`
- [ ] S3 website URL loads the browser client
- [ ] GitHub Actions deploy succeeds via OIDC (no static AWS keys)
- [ ] CodeDeploy jobs complete for both Lambda and EC2
- [ ] Finished matches appear in `MatchAnalytics` via stream processing
- [ ] MatchMaker Lambda runs in private subnets with VPC endpoints (no NAT)
