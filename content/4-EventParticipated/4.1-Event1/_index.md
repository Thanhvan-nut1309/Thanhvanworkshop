---
title: "Event 1"
date: 2026-07-25
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# AWS Vietnam Community Meetup — Attendance Report

**Event:** AWS Vietnam Community Meetup  
**Date & time:** Saturday, 25 July 2026 · 08:30 – 12:00 (check-in from 08:30, program starts 09:00)  
**Location:** AWS Hanoi — 7th floor, Grand Terra Tower, 36 Cát Linh, Đống Đa, Hanoi  
**Role:** Attendee

## Event overview

A half-day AWS Vietnam Community Meetup focused on how **AI is changing the way teams build products, run businesses and solve real problems** on AWS. The agenda combined a community update with five talks from AWS Community Heroes, Community Builders and practitioners deploying AI in production, followed by tea break, networking, a Kahoot quiz and a lucky draw.

## Agenda & speakers

| # | Speaker | Session |
|---|---------|---------|
| 1 | **Hồ Việt Anh & Phong Phạm** | Community Update — latest activities of the AWS Vietnam community |
| 2 | **Tuấn Vũ** | OpenClaw — The Rise and Practice of Open-Source AI Agents |
| 3 | **Nguyễn Thu & Nam La** | From AI Trends to Business Value |
| 4 | **Henry (Đức) Bùi** | Ship Fast with AI |
| 5 | **Dũng Lương** | Selecting the Right AI Agent Pattern on AWS |

The program also included **Tea Break & Networking** (meeting speakers and expanding your AWS + AI network) and a closing segment with **Kahoot Quiz & Lucky Draw** for exclusive AWS Community merch (community shirts, caps, tumblers, tote bags, mousepads and stickers), plus the **"Share bài – Nhận quà"** minigame awarding two special combos to the two public posts with the most likes, comments and shares.

## Session summaries

### Hồ Việt Anh & Phong Phạm — Community Update

Openers recapped the AWS Vietnam community's newest activities — study groups, builder programs and upcoming events — and emphasized the community as a flywheel: share what you learn, connect with builders, and opportunities compound over time. The update set the tone for a morning of knowledge sharing and networking.

### Tuấn Vũ — OpenClaw: The Rise and Practice of Open-Source AI Agents

Tuấn Vũ walked through the rise of **open-source AI agents**, using **OpenClaw** (the open-source, local-first personal agent platform) as the worked example. Topics covered how open-weight models and the growing **MCP (Model Context Protocol)** ecosystem let developers build agents that own their tools, memory and data; the practical side of running agents on your own infrastructure (privacy, cost control, full control over behavior); and real practice patterns — prompting, tool wiring, memory and guardrails — for moving an agent from demo to something you use daily. The through-line: open-source agents are making personal AI assistants accessible to individual developers, not just large companies.

### Nguyễn Thu & Nam La — From AI Trends to Business Value

This session shifted the conversation from AI hype to **measurable business value**. The speakers mapped current AI trends onto concrete enterprise outcomes — cutting manual work, accelerating decisions, and improving customer experience — and stressed selecting use cases with clear ROI, estimating cost honestly (infrastructure + data + review), and governing AI adoption (quality control and human accountability). A useful reminder that the business question — *what problem does this actually solve, and at what cost?* — comes before the technology question.

### Henry (Đức) Bùi — Ship Fast with AI

A practical talk on **delivering software faster with AI**: using AI assistants across the development loop (specification, implementation, testing, review and documentation) to raise throughput without dropping quality or shipping blindly. Themes included keeping a tight feedback loop, making AI-written code reviewable, and investing velocity where it actually matters to the product.

### Dũng Lương — Selecting the Right AI Agent Pattern on AWS

Dũng Lương provided a decision framework for **choosing the right agent pattern on AWS** — from simple single-agent loops to orchestrator–worker and multi-agent designs. The talk compared patterns on criteria like task structure, tool access (MCP/tools), latency, cost and reliability, and mapped them to AWS primitives (serverless compute, orchestration, event-driven schedules, model access via Bedrock AgentCore/AWS-provided options). This section resonated directly with my own internship work: even my small **EventBridge → Lambda → SES** due-date reminder is a deliberately picked, event-driven "pipeline" pattern — the same habit of choosing the simplest reliable automation.

---

## Takeaways

- **Open-source agents + MCP** are lowering the barrier to building your own AI assistants — locally owned, privacy-preserving and customizable.
- **Value first:** AI adoption should be tied to measurable business outcomes and honest cost estimates, not technology hype.
- **Pattern selection matters:** reliability and cost come from choosing the right agent pattern — often the minimal one — and adding guardrails.
- **Community compounding:** meetups connect classroom learning to how practitioners actually deploy AI, and actively strengthen careers through open sharing.

## Personal reflection

This meetup was my first AWS Vietnam Community event, and it tied naturally into the culture of the FCAJ internship program I was part of — the same emphasis on *build real things and share openly*, now with a dedicated AI-agent focus. Hearing practitioners reason about open-source agents, business value and production patterns helped me frame my own capstone choices — serverless automation, secrets management and cost control — in business terms, not just service names. The tea break and networking gave me the chance to talk with community Heroes and builders directly, and the Kahoot quiz was a fun way to close the morning.