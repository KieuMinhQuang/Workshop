---
title : "Prerequiste"
date : 2024-01-01 
weight : 2 
chapter : false
pre : " <b> 5.2. </b> "
---

## Step 1 – Prepare the AWS account

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/) with a lab account allowed to create IAM, VPC, EC2, RDS, S3, CloudFront, Cognito, CloudWatch, and Systems Manager resources.
2. Open **Billing and Cost Management → Credits** to review credit balance and expiration. Create an email spending alert in **AWS Budgets → Create budget**.
3. Select **Asia Pacific (Singapore) – `ap-southeast-1`** in the Region selector. Use this Region for regional resources; CloudFront is global.
4. Verify the AWS Account ID and service permissions in Singapore before provisioning resources.

![Select the Singapore AWS Region](/Workshop/images/5-Workshop/5.2-Prerequisites/01-aws-region.png)

## Step 2 – Prepare the GitHub repository

1. Sign in to [GitHub](https://github.com/) and open the [TechVina source repository](https://github.com/TaiHoang69/TechVina).
2. Select **Fork → Create fork** to create a repository under your GitHub account. The fork URL follows `https://github.com/<GITHUB_OWNER>/TechVina`.
3. Open your repository and verify the **`master`** branch along with `.github/workflows/`, `.github/scripts/`, `.github/tests/`, `backend/`, `frontend/`, and `database/`.
4. Use the repository owned by your account when configuring GitHub Actions and GitHub OIDC in section **5.8**.

![TechVina repository on GitHub](/Workshop/images/5-Workshop/5.2-Prerequisites/02-github-fork.png)

## Step 3 – Install Git for Windows

1. Open the [Git for Windows download page](https://git-scm.com/downloads/win), download the appropriate Windows installer, and run it.
2. Complete the installation with **Git Bash** enabled. Git Bash provides a shell for Git commands and Bash scripts on Windows.
3. Open **Git Bash** from the Start Menu and verify Git:

```bash
git --version
```

The terminal should display the installed Git version. The CI/CD steps use Git Bash to run `git add`, `git commit`, `git push`, and the Backend rollback test script.

## Step 4 – Install Visual Studio Code

1. Visit [Visual Studio Code](https://code.visualstudio.com/Download) and install the Windows version.
2. Open **Visual Studio Code** and choose **Terminal → New Terminal**.
3. Use the arrow beside the Terminal **`+`** button to choose **Git Bash** as your shell. If it is unavailable, verify that Git for Windows was installed correctly.

Visual Studio Code is used to edit `.github/workflows/*.yml`, `.github/scripts/*.sh`, and project source files before committing changes to GitHub.

## Step 5 – Clone TechVina onto Windows

1. In Git Bash, navigate to the parent folder where the project should be stored. To create the project in `C:`, for example, run:

```bash
cd /c
git clone https://github.com/<GITHUB_OWNER>/TechVina.git
cd TechVina
git branch --show-current
git status --short
```

2. Replace `<GITHUB_OWNER>` with the account that owns your deployment repository. `git branch --show-current` should return `master` for the repository's `master` branch. `git status --short` should produce no lines for a clean working tree.
3. In Visual Studio Code, choose **File → Open Folder** and open the cloned project (for example, `C:\TechVina`). Verify that `frontend/`, `backend/`, and `.github/` appear in Explorer.

When editing workflows in section **5.8**, run Git commands and the Backend rollback test from Git Bash at the repository root. Do not run Bash scripts directly in Windows PowerShell unless `bash` is available in `PATH`.

## Step 6 – Prepare the AWS working environment

This workshop uses **AWS Management Console**, **AWS CloudShell**, **EC2 Session Manager**, **Visual Studio Code**, **Git Bash**, and **GitHub Actions**:

- **AWS Management Console:** provision and configure AWS resources.
- **AWS CloudShell:** verify the AWS account and run AWS CLI commands in the browser.
- **EC2 Session Manager:** install, inspect, and operate the Spring Boot application on Ubuntu EC2.
- **Visual Studio Code and Git Bash:** edit code, manage Git, and run the rollback test on Windows.
- **GitHub Actions:** install Node.js **22** for the Frontend and Java **21** for the Backend on hosted runners, then build and deploy the applications. Node.js, Java, Maven, and AWS CLI do not have to be installed locally on Windows for these workflows.

Record the following values as AWS resources are created:

| Variable | Value |
|---|---|
| `AWS_REGION` | `ap-southeast-1` |
| `AWS_ACCOUNT_ID` | Your 12-digit AWS account ID |
| `GITHUB_OWNER` | The GitHub account that owns the deployment repository |
| `EC2_INSTANCE_ID`, `EC2_PUBLIC_IP` | Record after creating EC2 |
| `RDS_ENDPOINT` | Record after creating RDS |
| `CF_DISTRIBUTION_ID`, `CF_DOMAIN` | Record after creating CloudFront |
| `COGNITO_POOL_ID`, `COGNITO_CLIENT_ID`, `COGNITO_DOMAIN` | Record after creating Cognito |

The project uses **two S3 buckets**: `techvina-frontend-<AWS_ACCOUNT_ID>` for Frontend assets and product images, and `techvina-backend-artifacts-<AWS_ACCOUNT_ID>` for Backend CI/CD JARs and scripts. Bucket names must be globally unique within Amazon S3.

## Step 7 – Open AWS CloudShell and verify the account

1. In **AWS Management Console**, select **Asia Pacific (Singapore) – `ap-southeast-1`**.
2. Select the **CloudShell (`>_`)** icon to open a browser-based terminal.
3. Run the following commands **inside CloudShell**:

```bash
aws --version
aws sts get-caller-identity
export AWS_REGION=ap-southeast-1
echo "$AWS_REGION"
```

4. Compare the `Account` value with your AWS account ID. CloudShell uses the authenticated AWS Console session, so local Windows AWS access keys are not required.

![Open AWS CloudShell in the browser](/Workshop/images/5-Workshop/5.2-Prerequisites/03-aws-cloudshell.png)