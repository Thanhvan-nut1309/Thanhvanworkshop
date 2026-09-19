---
title: "Automation & monitoring roadmap"
date: 2026-08-29
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

## Goal

Add the **signature feature** of the project — a fully serverless **due-date reminder** pipeline — plus basic monitoring. This is the piece the mentor highlighted as the most valuable: it demonstrates both **serverless** and **event-driven** design, and very few people include it.

## Design — daily due-date reminders

```
Amazon EventBridge (cron, daily)
        │  invokes
        ▼
AWS Lambda ──scans──► Amazon RDS (borrow_records)
        │  builds recipient list
        ▼
Amazon SES ──email──► readers (due within 1–2 days, or overdue)
```

## Step 1 — Create the Lambda function

1. **Lambda → Create function → Author from scratch.**
2. Name: `library-due-date-reminder`; Runtime: Node.js (match project).
3. Execution role: allow `dynamodb`-style read on RDS (or use your own helper) plus `ses:SendEmail`. In this project the function connects to MySQL using the same Secrets Manager secret (`library-db-credentials`).
4. Logic outline:

```js
// pseudocode
const due = await findDueRecords(secret);   // due_date within 1-2 days OR status='overdue'
for (const r of due) {
  await ses.sendEmail(r.email, `"${r.title}" is due on ${r.due_date}`);
}
```

## Step 2 — Schedule it with EventBridge

1. **Amazon EventBridge → Rules → Create rule**, event source **Schedule**.
2. Cron expression (UTC): `0 1 * * ? *` — runs **01:00 daily**.
3. Target: the Lambda function above.
4. Create the rule and test with a manual **Invoke** from the Lambda console.

## Step 3 — Verify a reminder

1. Borrow a book whose `due_date` falls within the window (or temporarily set one to `overdue`).
2. Run the Lambda manually (or wait for the schedule).
3. Check **CloudWatch Logs** for the Lambda and confirm the recipient received the SES email.

## Step 4 — Monitoring

1. **CloudWatch → Log groups**: ECS task logs (`/ecs/task-library-management`), Lambda logs.
2. **CloudWatch → Alarms**: e.g. alarm when the ALB target returns `5xx` for 5+ minutes, or when the ECS service CPU exceeds a threshold.
3. **AWS Budgets** (optional): a few-dollar alert covering the NAT Gateway and RDS hourly charges during the internship.

## Step 5 — CI/CD extension (optional)

Wire the deploy into automation: **GitHub → AWS CodeBuild → Amazon ECR → ECS** so every `git push` to `main` rebuilds and redeploys the `library-app` image (same pattern used to deploy this report site with GitHub Actions + GitHub Pages).

## Expected outcome

- Readers automatically receive due-date reminders — no manual daily checks
- Serverless pipeline costs practically nothing (Lambda + EventBridge + SES)
- CloudWatch gives visibility into the app, DB and Lambda

## Status

| Item | Status |
|------|--------|
| EventBridge + Lambda + SES design | Documented above (final-phase extension) |
| ECS/RDS CloudWatch monitoring | Enabled during deployment |
| CI/CD rebuild pipeline | Recommended next step after the report |