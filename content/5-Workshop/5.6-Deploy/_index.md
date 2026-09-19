---
title: "Deploy — ECS Fargate + ALB"
date: 2026-08-26
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

## Goal

Run the containerized backend on **Amazon ECS with AWS Fargate** (serverless compute for containers) behind an **Application Load Balancer**, and let the container read its database credentials from **Secrets Manager** at runtime.

## Step 1 — Create the IAM task role

1. **IAM → Roles → Create role.** Trusted entity type: **AWS service**; use case: **Elastic Container Service → Elastic Container Service Task** (not plain "Elastic Container Service").
2. Attach the policy **`SecretsManagerReadWrite`** (the app only uses the read side — `GetSecretValue` — inside the code).
3. Role name: `library-ecs-task-role`. Create.

## Step 2 — Create the cluster

1. **ECS → Clusters → Create cluster.**
2. Name: `library-cluster`; Infrastructure: **AWS Fargate (serverless)** (no EC2 instances to manage).
3. Create.

## Step 3 — Create the task definition

1. **ECS → Task definitions → Create new task definition**, using the **ECR image** `367764690039.dkr.ecr.ap-southeast-2.amazonaws.com/library-management:latest`.
2. **Task role:** `library-ecs-task-role`.
3. Container name: `Main` (as generated). Port mapping: container port `3000` (HTTP).
4. Add environment variables:

| Name | Value |
|------|-------|
| `PORT` | `3000` |
| `USE_SECRETS_MANAGER` | `true` |
| `AWS_REGION` | `ap-southeast-2` |
| `DB_SECRET_NAME` | `library-db-credentials` |
| `JWT_SECRET` | (your own random string) |

> We do **not** write the database password into these variables — the app loads it from Secrets Manager when `USE_SECRETS_MANAGER=true`.

5. Create the task definition.

## Step 4 — Create the service + ALB

1. From **Cluster → Services → Create service**, select the task definition.
2. **Deployment:** Default (Rolling update).
3. **Networking:** attach the service to the **private subnets** of `library-vpc`; security group allowing inbound **3000** from the load balancer.
4. **Load balancing:** create a new **Application Load Balancer**, listener on **80/443**, target group health check path **`/health`** on port **3000**.
5. Create the service and wait for the tasks to become **Running** and the target group **healthy**.

## Step 5 — Verify /health through the ALB

Open the **ALB DNS name** in a browser (e.g. `http://library-alb-....elb.amazonaws.com/health`). A healthy response is `{"status":"ok"}`.

## Debugging story — app fell back to `localhost:3306`

Symptom: the ALB started answering, but the app kept trying to connect to `localhost:3306` instead of RDS — i.e. `USE_SECRETS_MANAGER` was not active, so the code fell back to its default connection string.

Root cause: the environment variables were **not saved into the running task definition revision**.

Fix:
1. In **ECS → Task definitions → task-library-management → revision → Container: Main → "Environment and secrets"**, confirm all five variables above are actually present.
2. Create a **new revision** with the variables set, then in **Cluster → Services → (service) → Update service → Revision** pick the new revision and update.

Once the task restarts with `USE_SECRETS_MANAGER=true`, the app reads `library-db-credentials` from Secrets Manager and reaches RDS.

## Verification

- [ ] ECS service tasks are **Running**
- [ ] ALB target group shows the health check **healthy**
- [ ] `GET /health` through the ALB returns `{"status":"ok"}`

## Troubleshooting

| Issue | Action |
|-------|--------|
| Task keeps restarting (`Stopped (reason)`) | Spread/replica count, image URI typo, or task role missing — check the task **stopped reason** and CloudWatch logs |
| Health check failing | Confirm port mapping and container port; check app logs; `/health` must respond on the container |
| App connects to `localhost:3306` | `USE_SECRETS_MANAGER` missing/false in the task definition — recreate the revision with the env block above |