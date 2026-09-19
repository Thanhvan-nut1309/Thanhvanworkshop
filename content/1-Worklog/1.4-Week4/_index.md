---
title: "Week 4 Worklog"
date: 2026-08-22
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives:

* Build the cloud networking foundation for the application.
* Provision the managed data services (RDS, S3, Secrets Manager).
* Prepare the ECR container flow for deployment.

**Period:** 22/08/2026 – 28/08/2026

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | ---------- | --------------- | ------------------ |
| 2 | - Finalize the architecture and ERD for the Library Management System <br> - Map each AWS service to its role (VPC, RDS, S3, Secrets Manager, ECS) | 24/08/2026 | 24/08/2026 | |
| 3 | - **VPC:** Create `library-vpc` via the VPC wizard (2 public + 2 private subnets) <br> - Configure Internet Gateway, NAT Gateway (Regional) and S3 Gateway endpoint | 25/08/2026 | 25/08/2026 | AWS VPC docs |
| 4 | - **RDS:** Create `library-db` MySQL (Free tier, private subnets, `library_db` schema) <br> - Wait and verify the instance reaches `Available` | 26/08/2026 | 26/08/2026 | |
| 5 | - **S3:** Create bucket `library-covers-thanhvan-2026` for cover images <br> - **Secrets Manager:** create secret `library-db-credentials` linked to RDS | 27/08/2026 | 27/08/2026 | |
| 6 | - **ECR:** create private repository `library-management` <br> - Verify all resource names and states before deployment | 28/08/2026 | 28/08/2026 | |

### Week 4 Achievements:

* Created the dedicated `library-vpc` with clean public/private separation and NAT egress.
* Provisioned Amazon RDS MySQL (`library-db`) in private subnets with no public access.
* Created the S3 covers bucket and the `library-db-credentials` secret in Secrets Manager.
* Created the private ECR repository ready to receive the tested image.
* Followed least-privilege and private-network principles throughout the setup.