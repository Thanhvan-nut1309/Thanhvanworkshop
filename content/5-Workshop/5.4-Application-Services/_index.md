---
title: "Application services — RDS, S3, Secrets Manager"
date: 2026-08-21
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

## Goal

Provision the three **managed data services** the application depends on: **Amazon RDS MySQL** (relational data), **Amazon S3** (book cover images) and **AWS Secrets Manager** (database credentials).

## Step 1 — Create the RDS MySQL instance

1. **RDS → Create database → choose "Full configuration"** from the dropdown (not "Express configuration", which hides network options we need).
   > Ensure **Engine type = MySQL**. For Aurora engines the "Full configuration" option is greyed out — the two must match.
2. **Templates:** `Free tier` (availability locked to `Single-AZ`).
3. **Settings:** DB instance identifier `library-db`; Master username `admin`; set a strong master password and store it **only** in Secrets Manager (never in code).
4. **Instance configuration:** `db.t3.micro` (fixed by Free tier). **Storage:** 20 GB default.
5. **Connectivity:**
   - **VPC:** `library-vpc`
   - **Public access:** `No`
   - **VPC security group:** `Create new` → `library-db-sg`
   - DB subnet group: leave default (AWS picks a private subnet)
6. **Additional configuration → Initial database name:** `library_db` — this is critical; if left blank, AWS won't create the database and you must create it manually later.
7. **Create database** and wait **5–10 minutes** for the status to reach `Available` (it passes `Configuring-enhanced-monitoring` and `Backing-up` first).

## Step 2 — Create the S3 covers bucket

1. **S3 → Create bucket** — name `library-covers-thanhvan-2026` (bucket names are globally unique; replace the suffix with your own if taken).
2. Keep defaults: General purpose, ACLs **disabled**, **Block Public Access** all on (demo stays private — covers are served by the app).
3. Encryption: default `SSE-S3`.
4. **Create bucket** and note the name — it is referenced by the application for cover uploads.

## Step 3 — Store credentials in Secrets Manager

1. **Secrets Manager → Store a new secret → Credentials for Amazon RDS database.**
2. **User name:** `admin`; **password:** the master password from Step 1.
3. Select the **`library-db`** database so the secret is linked.
4. **Secret name:** `library-db-credentials`.
5. **Automatic rotation:** keep `Disable` (not needed for the workshop).
6. **Store**, then copy the **Secret ARN** — used when configuring the ECS task definition ([5.6](5.6-Deploy/)).

### Screenshots for this section

{{< screenshot src="/images/5-Workshop/5.4-Application-Services/rds-create-config.png" lang="en" caption="RDS Create database (Full configuration): Engine MySQL, Free tier, identifier library-db, initial database name library_db." >}}

{{< screenshot src="/images/5-Workshop/5.4-Application-Services/rds-available.png" lang="en" caption="RDS console: library-db in Available state, Public access = No, VPC = library-vpc." >}}

{{< screenshot src="/images/5-Workshop/5.4-Application-Services/s3-bucket-created.png" lang="en" caption="S3 console listing bucket library-covers-thanhvan-2026 with Block Public Access on." >}}

{{< screenshot src="/images/5-Workshop/5.4-Application-Services/secret-created.png" lang="en" caption="Secrets Manager: secret library-db-credentials linked to the library-db database." >}}

## Expected outcome

- `library-db` in `Available` state, private inside `library-vpc`, no public access
- S3 bucket `library-covers-thanhvan-2026` ready
- Secret `library-db-credentials` created and linked to the RDS instance

## Troubleshooting

| Issue | Check |
|-------|--------|
| `Full configuration` greyed out | You picked an Aurora/PostgreSQL engine — switch Engine type back to **MySQL** |
| RDS stuck in `Configuring`/`Backing-up` | Normal final steps; refresh and wait a few more minutes |
| No database after RDS is available | `Initial database name` was left empty during creation — run `schema.sql` manually (see [5.7](5.7-Database-Migration/)) |