---
title: "Clean Up AWS Resources"
weight: 9
chapter: false
pre: "<b>5.9.</b> "
---

## Step 1 – Prepare and identify resources for deletion

TechVina was deployed in **Asia Pacific (Singapore) – `ap-southeast-1`**, using **Amazon EC2, Amazon RDS for MySQL, Amazon S3, Amazon CloudFront, Amazon Cognito, Amazon VPC, Amazon CloudWatch, Amazon SNS, and IAM**. After deployment and testing are complete, remove the resources created for the workshop.

1. Sign in to the **AWS Management Console** and select **Singapore (`ap-southeast-1`)**. Verify each resource's name and ID before deletion.
2. Open **GitHub → `TaiHoang69/TechVina` → Actions**. Disable the Frontend and Backend deployment workflows to prevent later pushes from triggering new AWS deployments.
3. Download the data that must be preserved, including source code, MySQL SQL exports, product images, workshop documents, and required deployment files. Verify the local copies before deleting data in AWS.
4. Identify the main resources:

   | Service | TechVina resources |
   |---|---|
   | Amazon EC2 | `techvina-ec2-backend` |
   | Amazon RDS | MySQL database and `techvina-db-subnet-group` |
   | Amazon S3 | `techvina-frontend-668668940018`, `techvina-backend-artifacts-668668940018` |
   | Amazon CloudFront | Distribution `E12UZ3V9Z7AGAU` and Origin Access Control (OAC) |
   | Amazon Cognito | `techvina-user-pool` |
   | Amazon CloudWatch | Alarm `techvina-backend-high-cpu`, Log Group `/techvina/backend/application` |
   | Amazon SNS | Topic `techvina-backend-cpu-alerts` |
   | Amazon VPC | `techvina-vpc` and its networking components |
   | AWS IAM | EC2 and GitHub Actions IAM roles and GitHub OIDC provider |

## Step 2 – Delete Amazon CloudFront resources

1. Open **Amazon CloudFront → Distributions** and select the TechVina distribution (`E12UZ3V9Z7AGAU`).
2. Choose **Disable** and confirm. When the update has finished deploying, choose **Delete** to remove the distribution.
3. Open **CloudFront → Functions**. If `techvina-spa-routing` exists, verify that it is no longer associated with any distribution, then delete it.
4. Open **CloudFront → Origin access**. Locate the OAC used for the Frontend S3 origin. Once no other distribution uses it, delete the OAC.
5. Return to **Distributions** and confirm the TechVina distribution is no longer listed.

## Step 3 – Terminate the Backend EC2 instance and remove related resources

1. Open **Amazon EC2 → Instances**, select `techvina-ec2-backend` (`i-0de916c9bd054d655`).
2. Inspect its EBS volume on the **Storage** tab. Choose **Instance state → Terminate (delete) instance** and confirm.
3. When the instance becomes **Terminated**, open **EC2 → Volumes**. If a TechVina volume remains and is no longer in use, select **Actions → Delete volume**.
4. Open **EC2 → Snapshots** and **AMIs** and use the **Owned by me** filter. Delete TechVina-specific snapshots or AMIs if any remain; deregister an AMI before deleting dependent snapshots where necessary.
5. Open **EC2 → Elastic IPs**. Disassociate and **release** any Elastic IP specifically allocated for TechVina.
6. Inspect **EC2 → Network Interfaces** to confirm that no interface belonging to the Backend instance remains. Interfaces owned by another AWS service should be removed through that service.

## Step 4 – Delete the RDS MySQL database and related resources

1. Open **Amazon RDS → Databases** in Singapore and select the TechVina MySQL database.
2. If **Deletion protection** is enabled, use **Modify** to turn it off.
3. Choose **Actions → Delete**. Once any necessary data has been exported outside AWS, choose not to create a **Final snapshot** and not to retain **Automated backups**, then confirm.
4. Open **RDS → Snapshots → Manual** and delete any remaining snapshots owned by TechVina.
5. Open **RDS → Automated backups** and inspect **Current Region**, **Retained**, and **Replicated**. Remove any remaining TechVina backups.
6. Open **RDS → Subnet groups**, select `techvina-db-subnet-group`, and choose **Delete** once the database has been removed.
7. Inspect **RDS → Parameter groups** and **Option groups**. Remove only custom groups created for TechVina, not AWS default groups.

Verification results: **DB Subnet groups (0)**, all three **Automated backups** tabs show **0**, and no TechVina manual snapshots remain.

## Step 5 – Empty and delete both S3 buckets

Open **Amazon S3 → General purpose buckets**. TechVina uses these two buckets:

| Bucket | Contents |
|---|---|
| `techvina-frontend-668668940018` | `index.html`, `assets/`, `images/`, and Frontend files |
| `techvina-backend-artifacts-668668940018` | Backend JAR files and deployment scripts in `releases/` |

For each bucket:

1. Select the bucket → **Empty** and confirm the deletion of all objects. If Versioning was enabled, also inspect object versions and delete markers.
2. Once the bucket is empty, return to the S3 bucket list and select **Delete**.
3. Enter the exact bucket name as requested and choose **Delete bucket**.

Verification results: AWS confirms successful deletion and the list displays **General purpose buckets (0)**.

## Step 6 – Delete CloudWatch and Amazon SNS resources

### 6.1. Delete the CloudWatch alarm

1. Open **Amazon CloudWatch → Alarms** in Singapore.
2. Select `techvina-backend-high-cpu` → **Actions → Delete** and confirm.
3. Verify that the list displays **Alarms (0)**.

### 6.2. Delete the CloudWatch log group

1. Open **CloudWatch → Logs → Log management → Log groups**.
2. Select `/techvina/backend/application` → **Actions → Delete log group(s)**.
3. Confirm the deletion and verify **Log groups (0)**.

### 6.3. Delete the SNS topic and subscription

1. Open **Amazon SNS → Topics**.
2. Select `techvina-backend-cpu-alerts` → **Delete**.
3. Enter `delete me` in the confirmation dialog and choose **Delete**. Verify **Topics (0)**.
4. Open **SNS → Subscriptions** and delete any remaining email subscription associated with the TechVina topic.
5. Verify **Subscriptions (0)**.

## Step 7 – Delete Amazon Cognito and AWS IAM resources

### 7.1. Delete the Cognito User Pool

1. Open **Amazon Cognito → User pools**.
2. Select `techvina-user-pool` (User Pool ID `ap-southeast-1_Gt4JGeuQM`) → **Delete**.
3. Check the prerequisites concerning the Cognito Domain and deletion protection if prompted.
4. Enter `techvina-user-pool` and choose **Delete**.
5. Verify **User pools (0)** and **Identity pools (0)**.

### 7.2. Delete IAM roles

Open **IAM → Roles** and delete the three TechVina roles:

- `techvina-ec2-ssm-role`
- `techvina-github-actions-backend-role`
- `techvina-github-actions-frontend-role`

Select the roles, choose **Delete**, and enter `delete` to confirm. Verify they have disappeared from the list. Do not remove **service-linked roles**, `AdminRole`, or roles used by other workloads.

### 7.3. Delete the GitHub OIDC provider

1. Open **IAM → Identity providers**.
2. Select `token.actions.githubusercontent.com` after verifying that no other IAM roles or repositories depend on it.
3. Choose **Delete**, enter `confirm`, and confirm the action.
4. Verify **Identity providers (0)**.

## Step 8 – Delete the Amazon VPC and networking components

1. Open **Amazon VPC → Your VPCs** in Singapore. Locate `techvina-vpc` (`vpc-04e52f7fe815354f2`); do not select the default VPC.
2. Inspect **VPC → NAT gateways**. The actual result is an empty NAT gateway list.
3. After EC2, RDS, and the DB Subnet Group have been removed, select `techvina-vpc` → **Actions → Delete VPC**.
4. In the confirmation dialog, inspect the resources AWS will delete with the VPC, including subnets, route tables, the Internet Gateway, and non-default security groups belonging to it. Enter `delete` and choose **Delete**.
5. Verify the success notification. **Your VPCs** now contains only the **default VPC** (`vpc-074982beea4ca3bfa`).
6. Open **EC2 → Security Groups**, **Network Interfaces**, and **Elastic IPs** to confirm that no TechVina networking resources remain. Leave the default VPC's default security group intact.

## Step 9 – Verify cleanup results

Recheck the AWS services used by the workshop in `ap-southeast-1`. CloudFront and IAM are global services.

| Service | Verified result |
|---|---|
| Amazon CloudFront | TechVina distribution deleted |
| Amazon EC2 | Backend instance terminated; no TechVina Elastic IP, Network Interface, Snapshot, or AMI remains |
| Amazon RDS | MySQL DB and DB Subnet Group deleted; no TechVina manual, retained, or replicated backups remain |
| Amazon S3 | General purpose buckets: **0** |
| Amazon CloudWatch | Alarms: **0**; Log groups: **0** |
| Amazon SNS | Topics: **0**; Subscriptions: **0** |
| Amazon Cognito | User pools: **0**; Identity pools: **0** |
| AWS IAM | Three TechVina IAM roles and the GitHub OIDC provider deleted |
| Amazon VPC | `techvina-vpc` and its dependencies deleted; default VPC remains |

The TechVina deployment has been removed from the workshop AWS environment. The GitHub repository and data backed up outside AWS remain available for development and future deployments.
