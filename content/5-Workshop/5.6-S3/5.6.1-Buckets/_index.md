---
title: "Create Two Amazon S3 Buckets"
weight: 1
chapter: false
pre: "<b>5.6.1.</b> "
---

## Step 1 – Open Amazon S3

In the **AWS Console**, open **Amazon S3 → General purpose buckets** in **Asia Pacific (Singapore) – `ap-southeast-1`**. Select **Create bucket**.

## Step 2 – Create the Frontend and Images bucket

Under **General configuration**, use:

| Field | Value |
|---|---|
| AWS Region | `ap-southeast-1` |
| Bucket type | General purpose |
| Bucket namespace | Global namespace |
| Bucket name | `techvina-frontend-<AWS_ACCOUNT_ID>` |
| Object Ownership | ACLs disabled (Bucket owner enforced) |
| Block all public access | Keep all enabled |
| Bucket Versioning | Disable |
| Default encryption | SSE-S3 |
| Object Lock | Disable |

Select **Create bucket**. This bucket stores the React/Vite build (`index.html`, `assets/`) and the product images (`images/`). Do not enable S3 static website hosting. CloudFront will use **Origin Access Control (OAC)** to read the private bucket.

## Step 3 – Create the Backend Artifacts bucket

Return to **General purpose buckets → Create bucket**. Retain the security settings from Step 2 and use:

```text
techvina-backend-artifacts-<AWS_ACCOUNT_ID>
```

Select **Create bucket**. This bucket will store JAR files and CI/CD artifacts under `releases/`. Its name must match the `s3:GetObject` ARN attached to the EC2 Role in section 5.3.2.

![Two TechVina S3 buckets](/Workshop/images/5-Workshop/5.6-S3/5.6.1-Buckets/01-two-buckets.png)

## Step 4 – Create the images prefix in the Frontend bucket

Open **`techvina-frontend-<AWS_ACCOUNT_ID>` → Objects → Create folder**. Enter `images` as the **Folder name** (without `/`), retain **Don't specify an encryption key** to use default SSE-S3, and select **Create folder**.

![Images prefix in the Frontend bucket](/Workshop/images/5-Workshop/5.6-S3/5.6.1-Buckets/02-images-folder.png)

## Step 5 – Create the releases prefix in Backend Artifacts

Open **`techvina-backend-artifacts-<AWS_ACCOUNT_ID>` → Objects → Create folder**. Enter `releases`, retain default encryption, and select **Create folder**.

![Releases prefix in Backend Artifacts](/Workshop/images/5-Workshop/5.6-S3/5.6.1-Buckets/03-releases-folder.png)

## Step 6 – Verify the buckets

The two buckets now contain these prefixes:

```text
techvina-frontend-<AWS_ACCOUNT_ID>/
└── images/

techvina-backend-artifacts-<AWS_ACCOUNT_ID>/
└── releases/
```

Keep **Block all public access = On** for both buckets. The folders are S3 key prefixes; the actual images and JARs are uploaded in the Frontend/CI/CD stages of section 5.8. CloudFront S3 read permissions are configured in **5.7.1**.
