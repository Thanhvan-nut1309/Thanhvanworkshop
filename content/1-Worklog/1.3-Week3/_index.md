---
title: "Week 3 Worklog"
date: 2026-08-15
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:

* Begin the capstone project for the internship.
* Design the application architecture and database schema.
* Build and run the backend foundation (auth, books, borrow/return) locally.

**Period:** 15/08/2026 – 21/08/2026

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | ---------- | --------------- | ------------------ |
| 2 | - Attend project kickoff: architecture overview, team roles, deliverables <br> - Define project requirements and cloud deployment scope | 17/08/2026 | 17/08/2026 | FCAJ project brief |
| 3 | - Set up the `library-management` repository and backend skeleton (Express + MySQL) <br> - Write `schema.sql`: `books`, `users`, `borrow_records` tables | 18/08/2026 | 18/08/2026 | Node.js docs |
| 4 | - Implement authentication: register/login, JWT, `admin`/`user` roles <br> - Hash passwords before storing | 19/08/2026 | 19/08/2026 | |
| 5 | - Implement book CRUD with search (title/author/category) <br> - Implement borrow/return: 14-day due date, transaction, stock check | 20/08/2026 | 20/08/2026 | |
| 6 | - Dockerize the app (`Dockerfile` + `docker-compose.yml`: app + MySQL) <br> - Run and test locally: `/health`, register, borrow, return | 21/08/2026 | 21/08/2026 | Docker docs |

### Week 3 Achievements:

* Joined the capstone project as an active team member.
* Designed the cloud architecture and the relational schema behind the Library Management System.
* Built the backend foundation: JWT auth, book CRUD + search, borrow/return with a 14-day rule.
* Containerized the full stack and ran it locally with Docker Compose.
* Verified the core API flows end-to-end against MySQL on the local machine.
* Prepared the exact Docker image that would be pushed to ECR and deployed on ECS later.