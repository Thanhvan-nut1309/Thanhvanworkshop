---
title: "Week 4 Worklog"
date: 2026-08-22
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives:

* Hand off the client with login flow to the team (netcode + auth sketch complete).
* Implement the CI/CD pipeline using GitHub and GitHub Actions.
* Implement IAM permission boundaries and validate CodeDeploy deployment capability.

**Period:** 22/08/2026 – 28/08/2026

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | ---------- | --------------- | ------------------ |
| 2 | - Finalize client login UI and session handling for team integration <br> - Hand off client codebase with documented auth + netcode interfaces | 24/08/2026 | 24/08/2026 | |
| 3 | - Set up GitHub repository structure and branching conventions <br> - **CI/CD:** Create initial GitHub Actions workflow for build and deploy | 25/08/2026 | 25/08/2026 | GitHub Actions docs |
| 4 | - Configure S3 static website hosting for the browser client <br> - Study IAM Permission Boundaries and least-privilege deploy patterns | 26/08/2026 | 26/08/2026 | AWS IAM docs |
| 5 | - Register GitHub OIDC identity provider in IAM (no long-lived access keys) <br> - Create deploy role with trust policy scoped to the GitHub repo | 27/08/2026 | 27/08/2026 | |
| 6 | - **Test:** Push to GitHub → verify S3 sync, Lambda update, and CodeDeploy job success | 28/08/2026 | 28/08/2026 | |

### Week 4 Achievements:

* Delivered a working client handoff package with login flow and netcode stubs for the team.
* Created the project repository and established branching conventions.
* Implemented the first GitHub Actions workflow for automated client builds.
* Created the S3 assets bucket with static website hosting, public-read policy, and CORS configuration.
* Eliminated static AWS access keys from CI by implementing GitHub OIDC → IAM role authentication.
* Applied IAM permission boundaries to constrain deploy role capabilities.
* Verified end-to-end deploy: GitHub push triggers Actions → S3 client sync + CodeDeploy for Lambda and EC2 fleet.