---
title: "Containerize — Amazon ECR"
date: 2026-08-24
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

## Goal

Push the locally tested Docker image to **Amazon ECR** (Elastic Container Registry) so ECS can pull and run it. ECR is the private container registry inside our own AWS account.

## Step 1 — Create the ECR repository

1. **Amazon ECR → Repositories → Create repository.**
2. **Visibility settings:** `Private` (default).
3. **Repository name:** `library-management`.
4. Create the repository.

## Step 2 — Authenticate Docker to ECR

In a terminal inside the project folder (where the `Dockerfile` lives), run:

```bash
aws ecr get-login-password --region ap-southeast-2 \
  | docker login --username AWS --password-stdin 367764690039.dkr.ecr.ap-southeast-2.amazonaws.com
```

Expected output ends with `Login Succeeded`.

## Step 3 — Build, tag and push the image

```bash
# build from the project Dockerfile (note the trailing dot)
docker build -t library-management .

# tag with the ECR URI
docker tag library-management:latest \
  367764690039.dkr.ecr.ap-southeast-2.amazonaws.com/library-management:latest

# push (may take a few minutes on a slow connection)
docker push 367764690039.dkr.ecr.ap-southeast-2.amazonaws.com/library-management:latest
```

The final lines report the image digest and size — the image is now in ECR.

## Step 4 — Verify

In **ECR → Repositories → library-management**, the image `latest` appears with the same `sha256` digest as the local build.

## Expected outcome

- Private ECR repository `library-management` with the tested image
- No image is stored in a public registry
- The ECS task definition will reference the ECR image URI directly

## Troubleshooting

| Issue | Check |
|-------|--------|
| `no basic auth credentials` | Docker login against ECR failed — re-run step 2 and confirm `Login Succeeded` |
| Push hangs on a slow link | Expected for the first push (image layers); retry — already-uploaded layers are skipped |
| Wrong registry/account | Match the account ID `367764690039` and region in the push commands shown in the ECR console |