---
title: "Blog 2"
date: 2026-05-01
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# BUILDING, DEPLOYING, AND OPERATING CONTAINERIZED APPLICATIONS WITH AWS FARGATE

Summarized from the AWS Compute Blog article, this post walks through an end-to-end workflow for running containerized applications with **Amazon ECS** and **AWS Fargate**, including a CI/CD pipeline with **AWS CodeBuild** and **AWS CodePipeline** that builds, deploys and operates the application automatically.

### Key Architectural Components:

- **Amazon ECS Cluster & Task Definition:** a cluster groups the workloads, and a task definition is the blueprint describing the container image, the `awsvpc` network mode and the CPU/memory requirements for the task.

- **AWS Fargate launch type:** Fargate runs containers without provisioning or managing EC2 instances — you specify the image, CPU and memory, and Fargate provides the compute with per-second billing.

- **Application Load Balancer & ECS service:** the service keeps the desired number of tasks running, registers them with the ALB, and spreads tasks across Availability Zones for high availability.

- **CI/CD pipeline:** **AWS CodeBuild** compiles the application and pushes images to **Amazon ECR**, and **AWS CodePipeline** automates the build → deploy → operate loop.

### Benefits of the Architecture:

- **No infrastructure management:** there are no EC2 instances or cluster capacity to operate; Fargate provisions compute exactly as the containers need.
- **High availability:** the ECS service maintains the desired task count and distributes tasks across AZs behind the load balancer.
- **Automated delivery:** the CodePipeline integration turns every code change into a deployed, containerized release.

---

### Links and References:

- **Facebook Post:** [AWS Study Group Facebook Group](https://www.facebook.com/groups/awsstudygroupfcj)
- **Reference Article:** [Building, deploying, and operating containerized applications with AWS Fargate](https://aws.amazon.com/blogs/compute/building-deploying-and-operating-containerized-applications-with-aws-fargate)