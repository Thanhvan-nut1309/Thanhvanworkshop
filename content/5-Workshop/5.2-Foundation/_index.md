---
title: "Application foundation"
date: 2026-08-17
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

## Goal

Build and run the **backend application locally** before touching any cloud infrastructure — a Dockerized Node.js/Express + MySQL stack that later becomes the exact image deployed to ECS.

## Stack

- **Backend:** Node.js 20 + Express, Docker image base `node:20-alpine`
- **Database:** MySQL (compatible with Amazon RDS MySQL later)
- **Auth:** register/login issuing a **JWT**, roles `admin` / `user`
- **Features:** book CRUD + search (title/author/category), borrow/return with a **14-day due date**, stock check and transactional writes

### Database design (`schema.sql`)

| Table | Purpose |
|-------|---------|
| `books` | `id, title, author, category, cover_image_url, total_copies, available_copies` |
| `users` | `id, name, email, password_hash, role` |
| `borrow_records` | `id, book_id, user_id, borrow_date, due_date, return_date, status` |

`status` uses `borrowing` / `returned` / `overdue`.

## Step 1 — Run locally with Docker (recommended) or natively

```powershell
# Option A — Docker (single command, no local Node/MySQL needed)
docker compose up --build
# open http://localhost:3000/health  →  {"status":"ok"}

# Option B — native (requires Node.js and MySQL already installed)
mysql -u root -p < schema.sql      # cmd; PowerShell uses: Get-Content schema.sql | mysql -u root -p
cp .env.example .env
npm install
npm run dev
```

The `docker-compose.yml` runs two containers (**app** on host port `3000`, **mysql** on host port `3307`). Inside the Docker network the app connects via `DB_HOST=mysql` / `DB_PORT=3306` — remapping the host port does **not** affect app↔db traffic.

## Step 2 — Smoke-test the API

```powershell
curl.exe -X POST http://localhost:3000/api/auth/register -H "Content-Type: application/json" -d "{\"name\":\"Test User\",\"email\":\"test@example.com\",\"password\":\"123456\"}"
```

Use `curl.exe` (not the PowerShell `curl` alias) so the `-d` syntax works in both PowerShell and cmd.

## Step 3 — Verify the core borrowing logic

1. Register/login → obtain JWT (`admin` account to create books).
2. Create a book with `total_copies` and `available_copies`.
3. Borrow as a `user` → a `borrow_records` row is created with `due_date = borrow_date + 14 days` and `available_copies` decremented inside a transaction.
4. Return the book → `status = returned`, copies restored.
5. Search by title/author/category returns the expected subset.

### Screenshots for this section

{{< screenshot src="/images/5-Workshop/5.2-Foundation/local-health-check.png" lang="en" caption="Docker Compose up output with the app and mysql containers running; browser shows http://localhost:3000/health returning status ok." >}}

{{< screenshot src="/images/5-Workshop/5.2-Foundation/register-api.png" lang="en" caption="Register smoke test: curl POST /api/auth/register returns the new user and a JWT." >}}

{{< screenshot src="/images/5-Workshop/5.2-Foundation/borrow-record.png" lang="en" caption="Borrow API result: borrow_records row with due_date = borrow_date + 14 days and available_copies decremented." >}}

## Expected outcome

- `GET /health` returns `{"status":"ok"}`
- Register/login, book CRUD, borrow/return and search all work against MySQL
- The same `Dockerfile` + `docker-compose.yml` will be reused for ECR/ECS later

## Troubleshooting

| Issue | Check |
|-------|--------|
| `npm: command not found` in PowerShell | Node.js not installed (or PATH not refreshed) — install Node LTS and reopen the terminal |
| `The '<' operator is reserved` in PowerShell | PowerShell doesn't support `<` redirects; pipe instead: `Get-Content schema.sql | mysql -u root -p` |
| `port 3306 already in use` | Another local MySQL container (`mysql-local`) owns the host port; remap the **host** side to `3307` in `docker-compose.yml` — the container-internal port stays `3306` |
| `curl` returns errors | PowerShell's built-in `curl` is an alias for `Invoke-WebRequest`; always use `curl.exe` |