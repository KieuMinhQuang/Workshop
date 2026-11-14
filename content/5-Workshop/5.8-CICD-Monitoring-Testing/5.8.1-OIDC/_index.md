---
title: "Connect GitHub Actions to AWS with OIDC"
weight: 1
chapter: false
pre: "<b>5.8.1.</b> "
---

## Step 1 – Create the GitHub OIDC Identity Provider

1. Open **AWS Console → IAM → Identity providers → Add provider**.
2. Select **OpenID Connect** as the provider type.
3. Enter the following values:

   | Field | Value |
   |---|---|
   | Provider URL | `https://token.actions.githubusercontent.com` |
   | Audience | `sts.amazonaws.com` |

4. Select **Add provider**. Under **Identity providers**, verify that `token.actions.githubusercontent.com` appears with type **OpenID Connect**.

![GitHub OIDC identity provider created in AWS IAM](/Workshop/images/5-Workshop/5.8-CICD-Monitoring-Testing/5.8.1-OIDC/01-oidc-provider.png)

## Step 2 – Create Two GitHub Actions IAM Roles

1. Open **IAM → Roles → Create role**.
2. Select **Web identity**, choose provider `token.actions.githubusercontent.com` and audience `sts.amazonaws.com`.
3. Create the following two roles:

   | Role | Purpose |
   |---|---|
   | `techvina-github-actions-frontend-role` | Upload the React build to S3 and create a CloudFront invalidation |
   | `techvina-github-actions-backend-role` | Upload the JAR/deployment script to S3 and deploy to EC2 through SSM |

4. Open each role and choose **Trust relationships → Edit trust policy** to restrict access to the intended GitHub repository and branch.

![IAM role for the GitHub Actions Frontend created successfully](/Workshop/images/5-Workshop/5.8-CICD-Monitoring-Testing/5.8.1-OIDC/02-github-roles.png)

## Step 3 – Configure the Trust Policy Using the GitHub OIDC Subject

The TechVina repository was created after **15 July 2026**, so its GitHub OIDC token uses an immutable `sub` claim that includes both the **Owner ID** and **Repository ID**:

```text
repo:<GITHUB_OWNER>@<OWNER_ID>/<REPOSITORY>@<REPO_ID>:ref:refs/heads/master
```

For **both IAM roles**, open **Trust relationships → Edit trust policy** and enter the following JSON. Replace the values enclosed in `<...>` with the actual AWS account and GitHub repository values:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::<AWS_ACCOUNT_ID>:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "token.actions.githubusercontent.com:aud": "sts.amazonaws.com",
          "token.actions.githubusercontent.com:sub": "repo:<GITHUB_OWNER>@<OWNER_ID>/TechVina@<REPO_ID>:ref:refs/heads/master"
        }
      }
    }
  ]
}
```

The TechVina deployment branch is `master`. The `StringEquals` conditions restrict role assumption to the configured repository and branch.

Match the `sub` condition to the actual claim emitted by GitHub Actions. Older repositories may emit the earlier subject format without immutable IDs; the trust policy must use the value that the repository actually issues.

![Backend IAM role trust policy configured with the actual OIDC subject format](/Workshop/images/5-Workshop/5.8-CICD-Monitoring-Testing/5.8.1-OIDC/03-backend-trust-policy.png)

Select **Update policy**, and repeat for the Frontend IAM role.

## Step 4 – Grant Permissions to the Frontend IAM Role

1. Open `techvina-github-actions-frontend-role`.
2. Choose **Add permissions → Create inline policy → JSON**.
3. Enter the following policy, replacing `<AWS_ACCOUNT_ID>` and `<CF_DISTRIBUTION_ID>` with the actual resource identifiers:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListFrontendBucket",
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::techvina-frontend-<AWS_ACCOUNT_ID>"
    },
    {
      "Sid": "UploadFrontendFiles",
      "Effect": "Allow",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::techvina-frontend-<AWS_ACCOUNT_ID>/*"
    },
    {
      "Sid": "InvalidateCloudFront",
      "Effect": "Allow",
      "Action": "cloudfront:CreateInvalidation",
      "Resource": "arn:aws:cloudfront::<AWS_ACCOUNT_ID>:distribution/<CF_DISTRIBUTION_ID>"
    }
  ]
}
```

4. Save the inline policy. The S3 permissions allow `aws s3 sync` to upload the Frontend build; the CloudFront permission is scoped to the TechVina distribution.

## Step 5 – Grant Permissions to the Backend IAM Role

1. Open `techvina-github-actions-backend-role`.
2. Choose **Add permissions → Create inline policy → JSON**.
3. Configure S3 artifact upload and SSM deployment permissions:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "UploadBackendArtifacts",
      "Effect": "Allow",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::techvina-backend-artifacts-<AWS_ACCOUNT_ID>/releases/*"
    },
    {
      "Sid": "RunDeployCommandOnEC2",
      "Effect": "Allow",
      "Action": "ssm:SendCommand",
      "Resource": [
        "arn:aws:ssm:ap-southeast-1::document/AWS-RunShellScript",
        "arn:aws:ec2:ap-southeast-1:<AWS_ACCOUNT_ID>:instance/<EC2_INSTANCE_ID>"
      ]
    },
    {
      "Sid": "ReadSSMCommandResult",
      "Effect": "Allow",
      "Action": "ssm:GetCommandInvocation",
      "Resource": "*"
    }
  ]
}
```

4. Name the inline policy `techvina-github-actions-backend-policy` and save it. The `ssm:SendCommand` permission is restricted to the `AWS-RunShellScript` document and the designated Backend EC2 instance.

![Inline policy created for the Backend IAM role](/Workshop/images/5-Workshop/5.8-CICD-Monitoring-Testing/5.8.1-OIDC/04-backend-inline-policy.png)

## Step 6 – Grant Artifact Read Permissions to the EC2 IAM Role

Under **IAM → Roles → `techvina-ec2-ssm-role` → Permissions**, verify the inline policy allowing the EC2 host to download the JAR/deployment script from the Backend Artifacts bucket:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::techvina-backend-artifacts-<AWS_ACCOUNT_ID>/releases/*"
    }
  ]
}
```

EC2 uses the IAM instance profile attached to `techvina-ec2-ssm-role` when executing `aws s3 cp`; no AWS Access Keys are stored on the host. This role also has permissions for AWS Systems Manager management.

## Step 7 – Enable GitHub Actions OIDC Permissions

The two workflow files are:

```text
.github/workflows/deploy-frontend.yml
.github/workflows/deploy-backend.yml
```

Each workflow grants permission to request an OIDC identity token:

```yaml
permissions:
  contents: read
  id-token: write
```

In the AWS authentication step, configure `aws-actions/configure-aws-credentials@v6.3.0` with the corresponding IAM role ARN:

```yaml
- name: Configure AWS credentials
  uses: aws-actions/configure-aws-credentials@v6.3.0
  with:
    role-to-assume: arn:aws:iam::<AWS_ACCOUNT_ID>:role/techvina-github-actions-frontend-role
    aws-region: ap-southeast-1
```

The Backend workflow uses `techvina-github-actions-backend-role` instead. After configuring credentials, the workflows verify AWS identity with:

```bash
aws sts get-caller-identity
```

Successful OIDC authentication is confirmed by the Frontend and Backend GitHub Actions runs documented in **5.8.2** and **5.8.3**.
