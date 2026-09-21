---
title: "Workshop"
date: 2026-08-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Library Management System on AWS — Implementation Workshop

Hands-on implementation steps for the **cloud-native Library Management System** described in my internship proposal. This section documents the AWS infrastructure I built end-to-end during the internship — from the local application foundation to networking, managed services, container deployment and database migration — on the [capstone project](https://github.com/Thanhvan-nut1309/Thanhvanworkshop) repository.

**Region:** `ap-southeast-2` (Sydney)  
**Scope:** local app foundation (Node.js/Express/MySQL), VPC networking, RDS + S3 + Secrets Manager, ECR containerization, ECS Fargate + ALB deployment, database migration, automation roadmap, live demo and documented teardown.

#### Contents

1. [Workshop overview](5.1-Workshop-overview/)
2. [Application foundation — local backend](5.2-Foundation/)
3. [Networking — VPC `library-vpc`](5.3-Networking/)
4. [Application services — RDS, S3, Secrets Manager](5.4-Application-Services/)
5. [Containerize — Amazon ECR](5.5-Containerize/)
6. [Deploy — ECS Fargate + ALB](5.6-Deploy/)
7. [Database migration & hardening](5.7-Database-Migration/)
8. [Automation & monitoring roadmap](5.8-Automation/)
9. [Live demo & verification](5.9-Live-Demo/)
10. [Resource cleanup (documented)](5.10-Cleanup/)