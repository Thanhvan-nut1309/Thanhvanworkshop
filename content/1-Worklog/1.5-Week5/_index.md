---
title: "Week 5 Worklog"
date: 2026-08-29
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:

* Deploy the capstone backend to ECS behind an Application Load Balancer.
* Connect the application to the managed database and complete data migration.
* Design the final-phase automation and finalize the workshop + internship report.

**Period:** 29/08/2026 – 04/09/2026

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | ---------- | --------------- | ------------------ |
| 2 | - Push the tested image to ECR repository `library-management` <br> - Create ECS cluster `library-cluster`, register task definition `task-library-management` <br> - Create IAM role `library-ecs-task-role` | 31/08/2026 | 31/08/2026 | AWS ECS docs |
| 3 | - Build ALB `library-alb` + target group + listeners <br> - Deploy the Fargate service in `library-vpc` private subnets; wire `library-db-credentials` from Secrets Manager | 01/09/2026 | 01/09/2026 | |
| 4 | - Run schema migration and seed data against RDS `library-db` <br> - End-to-end verification over HTTPS through the ALB: register, login, borrow, return | 02/09/2026 | 02/09/2026 | |
| 5 | - Design final-phase automation: EventBridge → Lambda → SES due-date reminders, CloudWatch alarms, CodeBuild CI/CD <br> - Document the automation and teardown procedure in the workshop sections | 03/09/2026 | 03/09/2026 | |
| 6 | - Set up the Hugo report site from the FCAJ template; customize `config.toml` <br> - Populate worklog, proposal, and workshop content; configure GitHub Actions (`hugo.yml`) <br> - Review, polish, write the recap article, and submit the report | 04/09/2026 | 04/09/2026 | |

### Week 5 Achievements:

* Deployed the Library Management System backend as a Fargate service in `library-cluster` behind the `library-alb` load balancer.
* Connected the application to Amazon RDS `library-db`, completed the schema migration and loaded seed data.
* Secured the database credentials inside the ECS task via the Secrets Manager secret `library-db-credentials`.
* Verified the complete flow through the ALB: register → login → browse books → borrow → return within the 14-day rule.
* Designed a documented final-phase automation (EventBridge → Lambda → SES reminders, CloudWatch alarms, CodeBuild CI/CD) framed honestly as design with a status table.
* Completed all workshop lab write-ups and the resource teardown documentation.
* Built the Hugo internship report site and deployed it to GitHub Pages via GitHub Actions.
* Pointed the repository to `Thanhvan-nut1309/Thanhvanworkshop` with the correct `baseURL` and author settings.
* Wrote the event recap article and completed the self-evaluation and feedback sections.
* Successfully deployed the report website and submitted the final report.