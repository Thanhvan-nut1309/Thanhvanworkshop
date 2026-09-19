---
title: "Database migration & hardening"
date: 2026-08-28
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

## Goal

Load the `schema.sql` into **RDS** (tables `books`, `users`, `borrow_records`) **without leaving the database publicly accessible**. The migration is a one-time job done from a workstation over a temporarily opened — then closed — inbound rule.

## Step 1 — Temporarily enable RDS public access

1. **RDS → Databases → library-db → Modify.**
2. **Connectivity → Public access:** `Yes`.
3. Select **Apply immediately** → **Continue** → **Modify**.
4. Wait for the status to return from `Modifying` to `Available`.

## Step 2 — Open port 3306 for your IP only

1. **VPC → Security Groups → `library-db-sg` → Inbound rules → Edit inbound rules → Add rule.**
2. Type: `MySQL/Aurora`; Source: **`My IP`** (AWS fills in your current public IP automatically).
3. Save rules.

## Step 3 — Connect with MySQL Workbench and run schema.sql

1. In MySQL Workbench create a new connection:
   - **Hostname:** `library-db.cb826ysqej0o.ap-southeast-2.rds.amazonaws.com`
   - **Port:** `3306`
   - **Username:** `admin`
   - **Password:** the master password you stored in Secrets Manager
2. **Test Connection** → must succeed → save.
3. **File → Open SQL Script →** select `schema.sql` from the project folder → **Execute** (lightning icon).
4. Confirm no red errors; the three tables appear in the schema.

## Step 4 — Re-enable privacy (critical)

1. **RDS → Modify → Public access:** `No` → **Apply immediately** → **Continue** → **Modify**.
2. In `library-db-sg`, **remove** the inbound 3306 rule.
3. Verify from the ECS side: the app still reads/writes the database (the ALB `/health` and borrow APIs keep working) — because ECS runs in the same VPC and uses the private endpoint.

> Do **not** skip this step. The temporary public window exists only for migration; leaving it open would expose the database to the internet.

### Screenshots for this section

{{< screenshot src="/images/5-Workshop/5.7-Database-Migration/sg-inbound-3306.png" lang="en" caption="library-db-sg inbound rules: MySQL/Aurora on port 3306 from My IP." >}}

## Expected outcome

- RDS contains the full schema (`books`, `users`, `borrow_records`)
- Database is again **private** (no public access, no inbound 3306 rule)
- Application (ECS, private) continues to work unaffected

## Troubleshooting

| Issue | Check |
|-------|--------|
| Workbench `Test Connection` fails | Public access enabled yet? SG inbound rule added for your IP? Status back to `Available`? |
| Zero database visible | The `Initial database name` may have been empty — connect and create `library_db` first, then run `schema.sql` |
| App breaks after migration | The revert step closed 3306 — ECS doesn't need it; confirm the task still reaches RDS on the private network |