---
title: "Week 5 Worklog"
date: 2026-08-29
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:

* Complete the capstone project (async processing, VPC hardening).
* Complete the AWS workshop lab content and deploy the internship report website.
* Finalize and submit the internship report.

**Period:** 29/08/2026 – 04/09/2026

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | ---------- | --------------- | ------------------ |
| 2 | - Implement async post-match processing: DynamoDB Streams → MatchAnalytics Lambda <br> - Install and configure CodeDeploy agent on the EC2 fleet | 31/08/2026 | 31/08/2026 | |
| 3 | - Move MatchMaker Lambda to private subnets with VPC endpoints (no NAT) <br> - Final integration testing: matchmaking → gameplay → match finish → analytics | 01/09/2026 | 01/09/2026 | |
| 4 | - Complete workshop sections; document resource cleanup procedure with screenshots <br> - Set up Hugo report site from the FCAJ template; customize `config.toml` | 02/09/2026 | 02/09/2026 | |
| 5 | - Populate worklog, proposal, and workshop content in Hugo <br> - Configure GitHub Actions (`hugo.yml`) for GitHub Pages deploy | 03/09/2026 | 03/09/2026 | |
| 6 | - Review and polish all report sections (worklog, proposal, workshop, self-evaluation) <br> - Write the event participation recap article (bài thu hoạch sự kiện) <br> - Submit report link to university advisor and company mentor | 04/09/2026 | 04/09/2026 | |

### Week 5 Achievements:

* Deployed the Flow E async pipeline: ActiveMatches DynamoDB Stream → MatchAnalytics Lambda → analytics table.
* Installed and configured the CodeDeploy agent on game server instances and created the EC2 deployment group.
* Reconfigured the MatchMaker into private subnets with DynamoDB gateway and EC2/CloudWatch interface endpoints.
* Verified the full game lifecycle: login → queue → match → WebSocket gameplay → finished match recorded in DynamoDB.
* Completed all workshop lab write-ups and documented teardown screenshots.
* Forked and customized the Hugo internship report template for personal use.
* Pointed the repository to `ThanhVan-nut1309/ThanhVanWorkshop` with the correct `baseURL` and author settings.
* Wrote the event recap article and completed the self-evaluation and feedback sections.
* Successfully deployed the report website via GitHub Actions to GitHub Pages and submitted the final report.