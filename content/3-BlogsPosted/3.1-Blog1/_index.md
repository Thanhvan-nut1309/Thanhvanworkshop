---
title: "Blog 1"
date: 2026-05-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# INTEGRATING AMAZON EVENTBRIDGE INTO YOUR SERVERLESS APPLICATIONS

Event-driven architecture enables developers to create decoupled services across applications. In this blog — summarized from the AWS Compute Blog article by James Beswick — I show how **Amazon EventBridge** delivers matching events to targets such as **AWS Lambda**, so your business logic reacts to events instead of making synchronous calls.

### Key Architectural Components:

- **Event Producer & Consumer (AWS Lambda):** applications publish events to EventBridge with `putEvents`. In the walkthrough, a banking ATM application (the producer) emits transaction events, while several downstream Lambda functions (the consumers) process only the subset of events they subscribe to.

- **Amazon EventBridge Rules & Targets:** the default event bus matches incoming JSON events against rules; each rule defines an event pattern and the target to invoke. Rules are declared with the `AWS::Events::Rule` resource, and an `AWS::Lambda::Permission` grants EventBridge permission to invoke the target function.

- **Declarative setup with AWS SAM:** the whole sample deploys through the AWS Serverless Application Model (SAM). Two integration styles are covered — configuring the rule through the function's `Events` property, or declaring the rule as a standalone resource with explicit events pattern, targets and permissions.

### Benefits of the Architecture:

- **Decoupling:** services communicate through events rather than direct calls, so producers and consumers scale and evolve independently.
- **Minimal configuration:** EventBridge handles routing, at-least-once delivery with retries, and batching out of the box.
- **Cost efficient:** the example runs within the AWS Free Tier, and you only pay when an event is actually processed.

---

### Links and References:

- **Facebook Post:** [AWS Study Group Facebook Group](https://www.facebook.com/groups/awsstudygroupfcj)
- **Reference Article:** [Integrating Amazon EventBridge into your serverless applications](https://aws.amazon.com/blogs/compute/integrating-amazon-eventbridge-into-your-serverless-applications)