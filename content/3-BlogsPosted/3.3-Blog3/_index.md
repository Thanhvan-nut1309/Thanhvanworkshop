---
title: "Blog 3"
date: 2026-05-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# HOW TO SEND YOUR FIRST EMAIL ON AMAZON SES

Summarized from the AWS Messaging Blog article by Dustin Taylor, this post covers the three actions needed to send your first email with **Amazon Simple Email Service (Amazon SES)**, and how to move from testing in the sandbox to production sending.

### Key Steps:

- **Verify an identity:** verify an email address or, preferably, a **domain**. Using a domain enables **SPF, DKIM and DMARC** alignment, which increases recipient trust and improves deliverability.

- **Request production access:** new SES accounts start in the **sandbox**, which only allows sending to verified addresses. Requesting production access raises the sending quotas so you can email any recipient.

- **Send the first email:** send a test email straight from the SES Console, then move to the **SES API / AWS SDK** for programmatic sending — the scalable path for production.

### Benefits of the Architecture:

- **Cost-effective and scalable:** SES processes billions of messages a year at per-message pricing, with no email servers to run yourself.
- **Good deliverability:** domain verification, SPF/DKIM/DMARC and bounce/complaint feedback protect your sender reputation.
- **Developer friendly:** the article references the SES Console, the SMTP interface and the AWS SDKs for any language.

---

### Links and References:

- **Facebook Post:** [AWS Study Group Facebook Group](https://www.facebook.com/groups/awsstudygroupfcj)
- **Reference Article:** [How to send your first email on SES](https://aws.amazon.com/blogs/messaging-and-targeting/how-to-send-your-first-email-on-ses)