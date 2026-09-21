---
title: "Proposal"
date: 2026-05-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Cloud-Native Library Management System on AWS

## A Scalable, Secure and Fully Managed Web Application for Managing Books, Readers and Borrowing

---

### 1. Executive Summary

This project presents a cloud-native backend architecture for a **Library Management System (LMS)** built entirely on Amazon Web Services (AWS). The application lets a librarian manage books, readers and borrow records, while readers can browse the catalog, borrow books and track due dates — all through a web interface backed by **Amazon ECS Fargate**, **Amazon RDS for MySQL**, **Amazon S3** and **AWS Secrets Manager**.

Instead of running the application on always-on virtual machines, the system runs a containerized **Node.js/Express** backend on **AWS Fargate** (serverless compute for containers) behind an **Application Load Balancer**, with the MySQL database isolated in **private subnets** so it is never exposed to the public internet. The design follows the **least-privilege**, **zero-hardcoded-secret** and **private-network** principles emphasized during the First Cloud AI Journey (FCAJ) internship.

A standout feature is the **automated due-date reminder**: an **Amazon EventBridge** schedule triggers an **AWS Lambda** function every day, which scans borrow records and sends **Amazon SES** emails to readers whose books are about to fall due or are already overdue. This event-driven, serverless piece of automation demonstrates both reliability and near-zero running cost.

---

### 2. Problem Statement

#### What's the Problem?

Small libraries and schools usually manage borrowing with spreadsheets or desktop software. This causes three practical problems:

- **Manual and error-prone tracking** — due dates are computed by hand; overdue books are often forgotten, losing revenue and books.
- **No reliable access** — the system lives on one machine, cannot be reached by readers outside the building, and has no backup or audit trail.
- **Hard to scale or maintain** — a traditional always-on server wastes money during quiet hours and makes deployment, upgrades and disaster recovery painful.

#### The Solution

A cloud-native web application with a simple core design rule: **keep the database private, keep credentials out of the code, and automate the boring parts**.

- A **Node.js/Express** backend (JWT authentication, role-based `admin`/`user`, book CRUD, search, borrow/return with a **14-day due date**) runs in a Docker container on **AWS Fargate**.
- **MySQL on Amazon RDS** stores relational data (`books`, `users`, `borrow_records`) in **private subnets**, reachable only from the application.
- **Amazon S3** stores book cover images; **AWS Secrets Manager** stores the database credentials, injected into the application at runtime.
- A serverless **EventBridge → Lambda → SES** pipeline automatically emails readers about upcoming or overdue due dates — the highlight of the project.
- **AWS CodeBuild** (with **ECR**) automates image build and push on every commit; **Amazon CloudWatch** monitors logs, metrics and alerts.

#### Benefits and Return on Investment (ROI)

- **Always-on availability at near-zero cost**: Fargate scales to zero-friendly pricing; the free-tier RDS instance and serverless components keep the monthly bill in the single digits (USD).
- **Security by design**: the database has **no public access**, credentials never appear in code or images, and IAM follows least privilege.
- **Automation that saves real work**: the due-date reminder replaces a manual daily check, and the CI/CD pipeline removes manual deployment steps.
- **Portable and reproducible**: the whole stack is Dockerized; the same image runs locally, in CI and on AWS.

---

### 3. Solution Architecture

![Library Management System architecture](/images/2-Proposal/architecture.svg)

The architecture is a classic **three-tier web application** hardened with AWS best practices.

#### Runtime Request-Flow Diagram

![Runtime request flow of the Library Management System](/images/2-Proposal/runtime-request-flow.png)

This diagram shows the **runtime request path** of the system, step by step:

1. A reader or librarian opens the web app in a browser and signs in. The frontend is served from **Amazon S3 + CloudFront**, and API calls are routed over HTTPS.
2. Each API request first hits the **Application Load Balancer** in the **public subnets** of the VPC, which terminates the connection and performs the `/health` health check.
3. The ALB forwards the request to the **Node.js/Express container running on ECS Fargate** (port 3000) inside the **private subnets**.
4. The backend authenticates the user (JWT, `admin`/`user` roles) and reads/writes **Amazon RDS MySQL (`library_db`)** — `books`, `users`, `borrow_records` — always remaining within the private network.
5. Database credentials are **not hardcoded**: at startup the container calls **AWS Secrets Manager** (`library-db-credentials`) using the `library-ecs-task-role`, and book cover images are loaded from **Amazon S3**.
6. Every request is captured by **Amazon CloudWatch** (application logs, metrics) for monitoring and troubleshooting.

#### Automation & Monitoring Diagram

![Automation and monitoring architecture of the Library Management System](/images/2-Proposal/automation-monitoring-architecture.png)

This diagram covers the **event-driven automation** and **observability** layers built on top of the running system:

1. **Automation — due-date reminders:** an **Amazon EventBridge** rule fires on a **daily cron schedule**, invoking the **AWS Lambda** function `library-due-reminder`.
2. The Lambda function reads the database credentials from **AWS Secrets Manager**, then scans **Amazon RDS** `borrow_records` for books due within 1–2 days or already overdue, and builds the recipient list.
3. For each reader in the list, the Lambda calls **Amazon SES** to send the reminder email — completely serverless and nearly free.
4. **Monitoring:** **Amazon CloudWatch** collects logs and metrics from ECS, Lambda and the ALB; **CloudWatch Alarms** notify when CPU or error rates exceed thresholds.
5. **CI/CD (optional extension):** a push to **GitHub** triggers **AWS CodeBuild** to rebuild the Docker image and push it to **Amazon ECR**, after which the ECS service rolls to the new revision automatically.

#### Architectural Flow Breakdown

##### **Flow 1: User Request Path (Web)**

- A reader or librarian opens the web app and signs in (JWT issued by the backend, `admin` vs `user` roles).
- Requests reach the **Application Load Balancer** in the **public subnets** and are forwarded to the **ECS Fargate** container (Node.js/Express, port **3000**) in the **private subnets**.
- The backend reads/writes **Amazon RDS MySQL (`library_db`)** across tables `books`, `users` and `borrow_records`. Borrowing creates a record with `due_date = borrow_date + 14 days` inside a transaction, checking stock first.

##### **Flow 2: Credential Handling**

- No database password is stored in code, images or environment files at rest.
- At startup, the container calls **AWS Secrets Manager** (secret `library-db-credentials`) via the `library-ecs-task-role` to obtain the connection details.
- One-time database migration (running `schema.sql`) was performed from a workstation over a temporarily opened (then closed) inbound rule, documented in the workshop.

##### **Flow 3: Automation (Due-Date Reminders)**

- **Amazon EventBridge** runs a **daily cron** rule.
- **AWS Lambda** queries `borrow_records` for books due within 1–2 days or already overdue, and hands the list to **Amazon SES**.
- Readers receive reminder emails automatically — fully serverless, no extra servers, virtually free.

##### **Flow 4: Delivery (CI/CD)**

- Developers push code to **GitHub**; **AWS CodeBuild** builds and tests the Docker image and pushes it to **Amazon ECR** (`library-management`).
- The ECS service is then updated to the new image revision, giving repeatable, auditable deployments without manual login to servers.

#### AWS Services Used

- **Amazon ECS (Fargate)**: serverless container runtime for the backend application.
- **Application Load Balancer**: entry point for HTTP(S) traffic, health checks on `/health`.
- **Amazon RDS for MySQL**: relational storage for `books`, `users`, `borrow_records`.
- **Amazon S3**: durable storage for book cover images.
- **AWS Secrets Manager**: secure storage of database credentials.
- **AWS Lambda + Amazon EventBridge + Amazon SES**: scheduled due-date email reminders (event-driven).
- **Amazon ECR + AWS CodeBuild**: container registry and CI pipeline.
- **Amazon CloudWatch**: logs, metrics and alarms.
- **VPC (public/private subnets) + NAT Gateway**: network isolation and controlled egress.

---

### 4. Technical Implementation

#### Implementation Phases

1. **Phase 1: Application Foundation (Week 1–2)**  
   Build the Node.js/Express backend — JWT auth with `admin`/`user` roles, book CRUD with search, borrow/return API with 14-day due date and stock check — plus `schema.sql` and a `Dockerfile`. Containerize the stack with `docker-compose` (app + MySQL) and verify locally.
2. **Phase 2: Networking (Week 3)**  
   Create the `library-vpc` with two public and two private subnets, an Internet Gateway and a NAT Gateway ("Regional") via the VPC wizard.
3. **Phase 3: Application Services (Week 3–4)**  
   Provision Amazon RDS MySQL (`library-db`, free tier, private subnet), the S3 covers bucket (`library-covers-thanhvan-2026`), and the Secrets Manager secret (`library-db-credentials`).
4. **Phase 4: Containerize & Deploy (Week 4–5)**  
   Push the image to Amazon ECR, create the Fargate cluster, task definition and ALB-backed service, migrate the schema into RDS, and verify end-to-end health.
5. **Phase 5: Automation, Monitoring & Cleanup (Week 5)**  
   Design/canary the EventBridge–Lambda–SES reminder pipeline, enable CloudWatch monitoring, document the dependency-ordered teardown, and write up the workshop.

#### Technical Requirements

- **Application stack**: Node.js 20 + Express + MySQL (Docker image `node:20-alpine`).
- **Database**: MySQL 8 — tables `books`, `users`, `borrow_records` (schema in `schema.sql`).
- **Network**: VPC with public subnets (ALB) and private subnets (ECS + RDS); NAT Gateway for updates and outbound calls.
- **Secrets**: `USE_SECRETS_MANAGER=true` at runtime; credentials fetched from Secrets Manager, never hardcoded.
- **Security**: RDS `Public access = No`; database security group restricted; least-privilege IAM task role.

---

### 5. Timeline & Milestones

| Week | Milestone |
| --- | --- |
| **Week 1–2** | Foundation: working local app (auth, books, borrow/return) in Docker |
| **Week 3** | VPC created; RDS, S3 bucket and Secrets Manager provisioned |
| **Week 4** | Image pushed to ECR; ECS Fargate service + ALB live |
| **Week 5** | Schema migrated to RDS; automation & monitoring designed; cleanup documented; report delivered |

---

### 6. Budget Estimation & Cost Optimization

- **Compute**: ECS Fargate billed per vCPU/hour only while running; suitable for a small library's traffic.
- **Database**: Free-tier RDS MySQL (`db.t3.micro`, 20 GB) fully covers the demo period; billed hourly afterwards.
- **Serverless**: Lambda and EventBridge cost fractions of a cent per day; SES has a generous free tier for notification volume.
- **Networking**: NAT Gateway is the only always-on cost (~$0.045/hour) — removed from the account during cleanup after the demo.
- **Storage**: S3 standard tier for cover images is negligible at this scale.

---

### 7. Risk Assessment

| Risk Item | Impact | Probability | Mitigation Strategy |
| --- | --- | --- | --- |
| **RDS left publicly accessible** | High | Low | Public access is toggled off after the one-time migration; verified in console and re-checked at cleanup. |
| **Expired/lost credentials** | Medium | Low | Credentials live only in Secrets Manager; rotated by recreating the secret; never committed to Git. |
| **NAT Gateway cost forgotten** | Medium | Medium | Documented in cleanup checklist; alarm on estimated charges via AWS Budgets. |
| **Container starts but can't reach RDS** | High | Medium | Debugged with environment-variable verification in the task definition (documented in workshop 5.6). |
| **Email reminder misconfigurations** | Low | Low | SES sandbox limited to verified addresses first, then production access; logs in CloudWatch. |

---

### 8. Expected Outcomes

- **A working, deployable web application** — library staff and readers can browse books, borrow and return items, and receive due-date reminders automatically.
- **Secure by default** — private database, secrets managed centrally, least-privilege IAM, no public credentials.
- **Cost-efficient and scalable** — serverless compute and on-demand services keep the bill small while allowing the system to grow with the library.
- **A learning showcase** — the project demonstrates the full journey taught in FCAJ: networking, containers, managed services, serverless automation, CI/CD and monitoring.