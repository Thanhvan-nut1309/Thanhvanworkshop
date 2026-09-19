---
title: "IAM"
date: 2024-01-01
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

## Goal

Configure IAM so **MatchMaker Lambda**, **EC2 game servers**, **GitHub Actions**, and **CodeDeploy** have least-privilege access to DynamoDB, EC2, S3, and deploy APIs.

## Step 1 — MatchMaker Lambda execution role

Grant the MatchMaker Lambda role permissions to:

- **DynamoDB:** read/write `MatchmakingQueue`, `ActiveMatches` (and related tables).
- **EC2:** `DescribeInstances` (discover `Role=FightingGameServer` hosts); optionally `AuthorizeSecurityGroupIngress` / `RevokeSecurityGroupIngress` for dynamic player IP rules.
- **CloudWatch Logs:** create log streams for Lambda logging.
- **VPC (later):** attach `AWSLambdaVPCAccessExecutionRole` when moving Lambda to private subnets ([5.8](5.8-VPC-MatchMaker/)).

## Step 2 — FightingGameServerInstanceRole (EC2)

1. **IAM → Roles → Create role** → AWS service → EC2.
2. Name: `FightingGameServerInstanceRole`.
3. Attach policies for:
   - **S3 read** on assets bucket (pull server binary/config at boot).
   - **DynamoDB write** on `ActiveMatches` for `markMatchFinished` (status, winner, timestamps) after async processing was added.
   - **CloudWatch Logs** (optional agent logs).
4. Create **instance profile** and attach to launch template ([5.2](5.2-EC2-Fleet/)).

![Roles for FightingGameServerInstanceRole](/images/5-Workshop/image15.png)

![FightingGameServerInstanceRole policies](/images/5-Workshop/image16.png)

### Modify IAM role

Update the instance role when deploy and match-finish features require additional DynamoDB or CodeDeploy permissions.

![Modify IAM role](/images/5-Workshop/image17.png)

## Step 3 — GitHub Actions deploy role

Role name used in CI: `GitHubActionsFightingGameDeploy`.

**Trust policy:** federated principal `token.actions.githubusercontent.com` (OIDC), scoped to repo `Thanhvan-nut1309/ThanhVanWorkshop`.

**Permissions policy** (summary):

- `s3:PutObject`, `s3:DeleteObject`, `s3:ListBucket` on assets bucket.
- `lambda:UpdateFunctionCode`, `lambda:PublishVersion`, `lambda:UpdateAlias` on MatchMaker function.
- CodeDeploy: `CreateDeployment`, `GetDeployment`, `ListDeployments`, `GetApplicationRevision`, register revision.
- `iam:PassRole` for CodeDeploy service roles as needed.

## Step 4 — CodeDeploy service roles

| Role | Used for |
|------|----------|
| Lambda CodeDeploy service role | Canary/linear deployments on MatchMaker alias |
| `CodeDeployServiceRoleForEC2` with `AWSCodeDeployRole` | EC2 deployment group (must **not** reuse Lambda service role) |

## Step 5 — MatchAnalytics Lambda role

1. Create `FightingGameMatchAnalyticsRole`.
2. Attach policy for **DynamoDB Streams** read on `ActiveMatches` (`NEW_AND_OLD_IMAGES`).
3. Grant write to `MatchAnalytics` table.

## Verification

- EC2 instance can pull from S3 and write match finish rows to DynamoDB.
- GitHub Actions workflow assumes deploy role without static access keys.
- CodeDeploy deployment group uses correct service role per compute type.
