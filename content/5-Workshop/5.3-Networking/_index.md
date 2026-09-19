---
title: "Networking — VPC"
date: 2026-08-19
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

## Goal

Create the **three-tier network boundary** for the application: **public subnets** for the Application Load Balancer and **private subnets** for ECS Fargate and RDS. A dedicated VPC keeps the report clean, avoids conflicts with other lab VPCs (`window-vpc`, `Linux-vpc`, default VPC) and makes final cleanup straightforward.

## Step 1 — Create the VPC with the wizard

1. **VPC → Your VPCs → Create VPC → VPC and more** (wizard mode).
2. Name tag: `library-vpc`.
3. IPv4 CIDR: keep the wizard default (e.g. `10.0.0.0/16`).
4. **Number of AZs:** `2`; **Number of public subnets:** `2`; **Number of private subnets:** `2`.
5. NAT Gateway: **`Regional - new`** (one gateway shared across AZs — cheaper than a Zonal gateway and enough for the app's outbound needs). Do **not** choose `None` (private resources then can't reach the internet to pull updates/images) and not `Zonal` (unnecessary cost).
6. VPC endpoints: keep the default **S3 Gateway** endpoint.
7. Create and wait for the VPC to reach **Available**.

> ⚠️ **Cost note:** the NAT Gateway is the only always-on, hourly-billed resource (~$0.045/h). It is removed from the account during cleanup after the demo.

## Step 2 — Verify the network layout

Check **Your VPCs** shows `library-vpc` with **State = Available**, and **Subnets** lists **2 public + 2 private** subnets attached to it (public ones route to the Internet Gateway; private ones route via NAT).

## Step 3 — Create the database security group

1. **VPC → Security Groups → Create security group**, name `library-db-sg`.
2. VPC: `library-vpc`.
3. No inbound rule yet — the inbound MySQL rule is opened only briefly during the one-time schema migration ([5.7](5.7-Database-Migration/)), then removed.

## Expected outcome

- A dedicated `library-vpc` with public/private subnet separation
- Internet Gateway for public traffic; NAT Gateway (Regional) for private egress
- S3 Gateway endpoint available for private subnet access to S3
- `library-db-sg` ready to attach to RDS

## Troubleshooting

| Issue | Check |
|-------|--------|
| VPC stuck in `Pending` | Wait a few minutes; NAT Gateway creation can take time |
| No internet from private subnet | NAT Gateway attached? Route table points private subnets at the NAT gateway? |
| Wizard blocked `Full configuration` options | Confusing UI states are normal in the console — the "VPC and more" wizard handles all subnets/route tables in one flow |