---
title: "Cleanup"
date: 2026-09-02
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

After the capstone was deployed and demonstrated, I documented the teardown of the whole stack **in dependency order**, so nothing is left behind and no hourly charges keep accruing (especially the **NAT Gateway**).

## Teardown order

| # | Resource | Steps |
|---|----------|-------|
| 1 | **ECS service + cluster** | Scale the service to `Desired = 0`, then delete the service; delete cluster `library-cluster`. |
| 2 | **ALB** | Delete the load balancer (this also removes the listener and target group). |
| 3 | **ECR repository** | Delete repository `library-management` (delete images first, or force-delete). |
| 4 | **Amazon RDS** | Delete instance `library-db` (skip final snapshot if not needed) and wait for deletion. |
| 5 | **Secrets Manager** | Delete secret `library-db-credentials`. |
| 6 | **Amazon S3** | Empty bucket `library-covers-thanhvan-2026`, then delete it. |
| 7 | **NAT Gateway** ⚠️ | Delete it first — this is the **hourly-charged** resource. Release any attached Elastic IP. |
| 8 | **VPC** | Delete `library-vpc` — removes subnets, route tables, internet gateway and the remaining security groups in one action. |
| 9 | **IAM roles** | Delete `library-ecs-task-role` and any temporary roles created for the task. |

### Screenshots for this section

{{< screenshot src="/images/5-Workshop/5.10-Cleanup/service-desired-zero.png" lang="en" caption="ECS service scaled to Desired = 0 with the tasks stopped before deleting the service and cluster." >}}

{{< screenshot src="/images/5-Workshop/5.10-Cleanup/no-resources-remain.png" lang="en" caption="Verification after teardown: VPC, RDS, ECS and S3 consoles no longer list any library-* resources." >}}

## Verification

After teardown:

- [ ] **ECS** — no clusters, services or task definitions remaining
- [ ] **EC2** — no load balancers listed (Check "Load Balancers" under EC2)
- [ ] **RDS** — no `library-db` instance in any state
- [ ] **S3** — bucket deleted
- [ ] **VPC** — `library-vpc` no longer listed
- [ ] **IAM** — role `library-ecs-task-role` deleted
- [ ] **Cost Explorer / Billing** — no residual charges from the stack

> The database password and connection details lived only inside Secrets Manager, which is deleted here — so there are no leftover credentials anywhere.