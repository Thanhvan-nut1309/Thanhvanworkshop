---
title: "Live demo & verification"
date: 2026-09-01
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

## Live system

Once the deployment is healthy, the application is reachable at the **ALB DNS name**:

```
http://library-alb-<random>.ap-southeast-2.elb.amazonaws.com
```

`GET /health` on it returns `{"status":"ok"}` — the same check the load balancer uses for its target group.

## Local end-to-end walkthrough (verified in Docker)

### Register / login

```powershell
curl.exe -X POST http://localhost:3000/api/auth/register -H "Content-Type: application/json" -d "{\"name\":\"Demo Reader\",\"email\":\"demo@example.com\",\"password\":\"123456\"}"
# → creates the user with role "user"; then POST /api/auth/login returns a JWT
```

### Admin creates a book

```powershell
curl.exe -X POST http://localhost:3000/api/books -H "Authorization: Bearer <admin-jwt>" -H "Content-Type: application/json" -d "{\"title\":\"Dế Mèn phiêu lưu ký\",\"author\":\"Tô Hoài\",\"category\":\"Văn học\",\"total_copies\":2}"
```

### A reader borrows and returns

1. `POST /api/borrow` (reader JWT) → creates a `borrow_records` row with `due_date = +14 days`, `available_copies` decremented.
2. `GET /api/borrow/me` → the reader sees the active loan and its due date.
3. `POST /api/borrow/:id/return` → `status = returned`, copies restored.

### Search

`GET /api/books?q=tô+hoài` → filters by title/author/category.

## Deployed-system verification

After ECS + ALB deployment and schema migration:

| Check | Expected | Result |
|-------|----------|--------|
| `GET /health` via ALB | `{"status":"ok"}` | ✅ |
| ALB target health | `Healthy` | ✅ |
| Register API via ALB (`curl.exe ... /api/auth/register`) | 201 / user created | ✅ |
| Login + list books via ALB | JWT + book list | ✅ |
| RDS has 3 tables | `books`, `users`, `borrow_records` | ✅ |
| RDS `Public access` | `No` (reverted after migration) | ✅ |

## Expected outcome

- The same application code runs **locally (Docker) and in production (ECS Fargate)** from the same image
- All core flows — register, login, books CRUD, borrow, return, search — verified end-to-end
- Database remains private throughout the demo