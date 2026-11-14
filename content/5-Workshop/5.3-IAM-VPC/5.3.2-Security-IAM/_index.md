---
title : "Test the Gateway Endpoint"
date : 2024-01-01 
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---

## Step 1 – Create Security Groups

In **AWS Console**, select **Asia Pacific (Singapore) – `ap-southeast-1`**, then open **VPC → Security groups → Create security group**. Create the two security groups below in order.

### Step 1.1 – Create the EC2 Backend Security Group

1. Under **Basic details**, enter:

   | Console field | Value |
   |---|---|
   | **Security group name** | `techvina-ec2-sg` |
   | **Description** | `Security group for TechVina Backend EC2` |
   | **VPC** | Select `techvina-vpc` (`10.20.0.0/16`), **not the Default VPC** |

   `techvina-vpc` is the **VPC name**, not the security group name. A security group name cannot be changed after creation.

2. Under **Inbound rules**, add **two** rules for HTTPS on Nginx and Let's Encrypt:

   | Type | Port | Source | Description |
   |---|---|---|---|
   | `HTTP` | `80` | `0.0.0.0/0` | `LetsEncrypt-HTTP` |
   | `HTTPS` | `443` | AWS-managed prefix list `com.amazonaws.global.cloudfront.origin-facing` | `Allow-CloudFront-HTTPS` |

   Set **Source type = Custom**, search for `cloudfront.origin-facing`, and select the AWS-managed prefix list (for example `pl-31a34658` in Singapore). Do not copy a prefix list ID from another Region. **Do not expose TCP 8080 publicly.** Nginx will proxy HTTPS traffic on port 443 to Spring Boot at `127.0.0.1:8080`.

   Port `80` is used for HTTP-01 certificate issuance/renewal and HTTP-to-HTTPS redirects. Port `443` accepts HTTPS origin traffic only from CloudFront. **5.7.1** configures DuckDNS, Nginx, and the certificate after EC2 is launched.

   Security Group **Description** values must contain only accepted characters; `LetsEncrypt-HTTP` and `Allow-CloudFront-HTTPS` are valid descriptions.

3. Under **Outbound rules**, leave the AWS default rule unchanged:

   | Type | Destination |
   |---|---|
   | `All traffic` | `0.0.0.0/0` |

4. Click **Create security group**. On its details page, confirm the name `techvina-ec2-sg`, VPC `techvina-vpc`, and the inbound **TCP 80** rule from `0.0.0.0/0` and **TCP 443** from the CloudFront prefix list. Do not add an inbound SSH `22` rule; this lab uses **AWS Systems Manager Session Manager** to access EC2.

### Step 1.2 – Create the Amazon RDS MySQL Security Group

1. Return to **VPC → Security groups → Create security group**. Under **Basic details**, enter:

   | Field | Value |
   |---|---|
   | **Security group name** | `techvina-rds-sg` |
   | **Description** | `Security group for TechVina RDS MySQL` |
   | **VPC** | `techvina-vpc` |

2. Under **Inbound rules**, choose **Add rule**:

   | Field | Value |
   |---|---|
   | **Type** | `MYSQL/Aurora` (automatically sets Port `3306`), or `Custom TCP` with Port `3306` |
   | **Source type** | `Custom` |
   | **Source** | Search for `techvina-ec2-sg` and select its **Security Group ID (`sg-...`)** within `techvina-vpc` |
   | **Description – optional** | `MySQL access from TechVina EC2` |

   **Do not choose `Anywhere-IPv4` or `0.0.0.0/0`** for MySQL. The source must be the EC2 security group, not your computer's IP address.

3. Leave **Outbound rules** at `All traffic → 0.0.0.0/0`, then click **Create security group**.

### Step 1.3 – Verify Security Groups

In **VPC → Security groups**, confirm that both security groups belong to `techvina-vpc`:

| Security Group | Inbound rule | Outbound rule |
|---|---|---|
| `techvina-ec2-sg` | TCP `80` from `0.0.0.0/0`; TCP `443` from the CloudFront origin-facing prefix list | `All traffic` |
| `techvina-rds-sg` | TCP `3306` from `techvina-ec2-sg` | `All traffic` |

![TechVina security groups](/Workshop/images/5-Workshop/5.3-IAM-VPC/5.3.2-Security-IAM/01-security-groups.png)

## Step 2 – Create the EC2 IAM Role

### Step 2.1 – Create the Role and attach SSM and CloudWatch permissions

1. Open **IAM → Roles → Create role**. Under **Trusted entity type**, choose **AWS service**. Choose **EC2** for **Service or use case** and **Use case**, then select **Next**.
2. Under **Add permissions**, find and select both **`AmazonSSMManagedInstanceCore`** and **`CloudWatchAgentServerPolicy`**. Select **Next**.
3. Set **Role name** to **`techvina-ec2-ssm-role`**. Verify that both managed policies are selected, then choose **Create role**.

| Policy | Purpose |
|---|---|
| `AmazonSSMManagedInstanceCore` | Allows EC2 to use AWS Systems Manager, Session Manager, and receive SSM commands |
| `CloudWatchAgentServerPolicy` | Allows the CloudWatch Agent to send EC2 logs and metrics to CloudWatch |

### Step 2.2 – Add read-only access to the Backend Artifacts S3 bucket

1. Open **IAM → Roles → `techvina-ec2-ssm-role`**.
2. On the **Permissions** tab, choose **Add permissions → Create inline policy**, then open the **JSON** editor.
3. Paste the following policy. Replace **`<AWS_ACCOUNT_ID>`** with the AWS account ID of the lab account. Find it in the AWS Console account menu or run `aws sts get-caller-identity --query Account --output text` in CloudShell:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ReadTechVinaBackendReleases",
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::techvina-backend-artifacts-<AWS_ACCOUNT_ID>/releases/*"
    }
  ]
}
```

4. Select **Next**, enter the policy name **`techvina-ec2-backend-s3-read-policy`**, and choose **Create policy**.

The ARN references the **`techvina-backend-artifacts-<AWS_ACCOUNT_ID>`** bucket created in **5.6.1**. An IAM policy can be created before its referenced bucket exists. The bucket name in 5.6.1 must match this ARN exactly. If the bucket name changes because of a naming conflict, update the IAM policy ARN accordingly. The `s3:GetObject` permission is restricted to release objects under `releases/`.

### Step 2.3 – Grant manual frontend S3 upload permissions to EC2

Under **IAM → Roles → `techvina-ec2-ssm-role` → Permissions → Add permissions → Create inline policy → JSON**, grant `s3:ListBucket` and `s3:PutObject` for the frontend bucket. These permissions are required to build on EC2 and upload `dist/` in **5.7.3**.

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
      "Sid": "UploadFrontendObjects",
      "Effect": "Allow",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::techvina-frontend-<AWS_ACCOUNT_ID>/*"
    }
  ]
}
```

Replace `<AWS_ACCOUNT_ID>` and name the policy **`techvina-frontend-s3-upload`**. Choose **Create policy**. This policy deliberately does not include `s3:DeleteObject`, because the manual sync in 5.7.3 does not use `--delete`.

### Step 2.4 – Verify the IAM Role

Open **IAM → Roles → `techvina-ec2-ssm-role` → Permissions** and verify:

| Policy type | Policy name |
|---|---|
| AWS managed | `AmazonSSMManagedInstanceCore` |
| AWS managed | `CloudWatchAgentServerPolicy` |
| Inline | `techvina-ec2-backend-s3-read-policy` |
| Inline | `techvina-frontend-s3-upload` |

![IAM instance role for EC2](/Workshop/images/5-Workshop/5.3-IAM-VPC/5.3.2-Security-IAM/02-instance-role.png)

## Step 3 – Validate

Both Security Groups must belong to the correct VPC. Do not allow public MySQL access. Confirm that `techvina-ec2-ssm-role` has both managed policies and both inline policies listed in Step 2.4.