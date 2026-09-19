---
title: "GitHub OIDC"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

## Goal

Authenticate **GitHub Actions** to AWS using **OIDC**—no long-lived `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` in repository secrets.

![GitHub OIDC → AWS (no long-lived access keys)](/images/5-Workshop/image9.png)

## Step 1 — Add GitHub as OIDC identity provider

1. **IAM → Identity providers → Add provider**.
2. Provider type: **OpenID Connect**.
3. Provider URL: `https://token.actions.githubusercontent.com`
4. Audience: `sts.amazonaws.com`
5. Create provider.

![Add GitHub as OIDC provider](/images/5-Workshop/image10.png)

## Step 2 — Create IAM role for GitHub Actions

1. **IAM → Roles → Create role**.
2. Trusted entity: **Web identity** → select the GitHub OIDC provider.
3. Condition (example):

```json
"StringEquals": {
  "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
},
"StringLike": {
  "token.actions.githubusercontent.com:sub": "repo:Thanhvan-nut1309/ThanhVanWorkshop:*"
}
```

4. Role name: `GitHubActionsFightingGameDeploy` (or your chosen name).
5. Attach permissions policy from [5.4 IAM](5.4-IAM/).

![Attach permissions policy to role](/images/5-Workshop/image11.png)

![Role trust policy](/images/5-Workshop/image12.png)

## Step 3 — Configure repository secrets

In **GitHub → Settings → Secrets and variables → Actions**, add:

| Secret | Purpose |
|--------|---------|
| `AWS_ROLE_ARN` | ARN of the OIDC role |
| `AWS_REGION` | `ap-southeast-1` |
| `ASSETS_BUCKET` | S3 bucket name |
| `COGNITO_*` | User pool / client IDs for generated `config.js` |
| `MATCHMAKER_API_BASE` | API Gateway base URL |
| `WS_SERVER` | WebSocket server hint for client |

Do **not** store static AWS access keys.

![Repository secrets](/images/5-Workshop/image13.png)

![Repository secrets (continued)](/images/5-Workshop/image14.png)

## Step 4 — Update GitHub Actions workflow

In `.github/workflows/deploy.yml`:

```yaml
permissions:
  id-token: write
  contents: read

- uses: aws-actions/configure-aws-credentials@v4
  with:
    role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
    aws-region: ap-southeast-1
```

## Step 5 — Verify OIDC login

1. Push a commit to trigger the workflow.
2. Confirm **Configure AWS credentials (OIDC)** step succeeds.
3. Confirm the full pipeline completes — MatchMaker Lambda deploy, S3 client sync, and game-server deploy via SSM (this was the **first CI/CD path before CodeDeploy** was added in [5.6](5.6-CodeDeploy/)).

![First CI/CD pipeline succeeded via OIDC (pre-CodeDeploy)](/images/5-Workshop/image18.png)

![MatchMaker `live` alias created after deploy](/images/5-Workshop/image19.png)

## Expected outcome

- CI uses short-lived credentials via `AssumeRoleWithWebIdentity`
- No static AWS keys in GitHub secrets
- Same role can be rotated or scoped by updating IAM only
