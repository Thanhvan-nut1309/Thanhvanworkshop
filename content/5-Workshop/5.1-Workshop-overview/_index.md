---
title: "Workshop overview"
date: 2026-08-15
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## Purpose

This workshop documents the AWS implementation of a **Library Management System (LMS)**: a Node.js/Express backend (JWT auth, book CRUD, search, borrow/return with a 14-day due date) running on **ECS Fargate** behind an **Application Load Balancer**, with **RDS MySQL** in private subnets, **S3** for cover images and **Secrets Manager** for database credentials — plus a serverless **due-date email reminder** pipeline (EventBridge → Lambda → SES).

The architecture follows the proposal flows:

| Flow | Components | My workshop coverage |
|------|------------|---------------------|
| **Web requests** | ALB, ECS Fargate, RDS | Foundation, Networking, Application services, Deploy |
| **Secrets** | Secrets Manager, task role | Application services, Deploy |
| **Automation** | EventBridge, Lambda, SES | Automation roadmap |
| **Delivery** | GitHub, CodeBuild, ECR | Containerize, Deploy |

Each section was implemented and verified on my own AWS account during the internship.

## Prerequisites

- AWS account with admin access in `ap-southeast-2`
- Node.js + npm and Docker Desktop for local development
- GitHub repository: `Thanhvan-nut1309/Thanhvanworkshop`
- AWS CLI configured locally (optional, for ECR commands)
- MySQL Workbench (used once for the one-time database migration)

## Resource naming reference

| Resource | Name / pattern |
|----------|----------------|
| VPC | `library-vpc` (2 public + 2 private subnets) |
| RDS instance / schema | `library-db` / `library_db` |
| Database security group | `library-db-sg` |
| S3 bucket | `library-covers-thanhvan-2026` |
| Secrets Manager secret | `library-db-credentials` |
| ECR repository | `library-management` |
| ECS cluster / task | `library-cluster` / `task-library-management` |
| IAM task role | `library-ecs-task-role` |
| ALB | `library-alb` (target group on `/health`, port 3000) |
| Region / account | `ap-southeast-2` / `367764690039` |

## Workshop order

Complete sections **5.2 → 5.7** in sequence — each step builds on the previous one (foundation → network → services → container → deploy → migration). Section **5.8** documents the automation and monitoring extension designed in the final phase. Section **5.9** verifies the running system end-to-end. Section **5.10** is the dependency-ordered teardown guide.

## Verification checklist

After all sections:

- [ ] Local Docker stack answers `GET /health` with `{"status":"ok"}`
- [ ] `library-vpc` shows 2 public + 2 private subnets and an Available state
- [ ] RDS `library-db` reaches `Available` with `Public access = No`
- [ ] ECR contains the tagged image `library-management:latest`
- [ ] ECS service is running and the ALB health check passes (`/health`)
- [ ] `schema.sql` executed on RDS; tables `books`, `users`, `borrow_records` exist
- [ ] RDS public access switched back **off** after migration