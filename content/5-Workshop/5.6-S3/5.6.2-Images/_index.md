---
title: "Prepare and upload product images to S3"
weight: 2
chapter: false
pre: "<b>5.6.2.</b> "
---

## Step 1 – Identify image files

Open `frontend/public/images/` in the TechVina repository on the `master` branch:

```text
frontend/public/images/
├── banners/
├── categories/
└── products/
    ├── apple/
    └── xiaomi/
        └── xiaomi-series/
```

The `phone_store` database stores relative image paths, for example:

```text
/images/products/xiaomi/xiaomi-series/xiaomi-15/image_1262703568.webp
```

The matching S3 object key must be `images/products/xiaomi/xiaomi-series/xiaomi-15/image_1262703568.webp` in the frontend bucket. Do not change the paths stored in MySQL.

## Step 2 – Upload images to Amazon S3

1. Open **Amazon S3 → Buckets → `techvina-frontend-<AWS_ACCOUNT_ID>`**.
2. Stay at the **bucket root**, not inside `images/`, and choose **Upload → Add folder**.
3. Select the entire **`frontend/public/images`** folder on your computer, including `banners`, `categories`, and `products`.
4. Verify that each destination path starts with `images/`, **not** `images/images/`.
5. Choose **Upload** and review the **Succeeded** count.

The actual TechVina deployment uploaded **66 files totaling 2.4 MB, with no failed files**.

![Successful upload of 66 images to the frontend bucket](/Workshop/images/5-Workshop/5.6-S3/5.6.2-Images/03-images-upload-success.png)

## Step 3 – Verify S3 object keys

Open `techvina-frontend-<AWS_ACCOUNT_ID> → images/` and verify these prefixes:

```text
images/banners/
images/categories/
images/products/
```

Check an object under `images/products/` and compare its **Object key** with the database path. Keep **Block all public access** enabled. Do not apply `public-read` ACLs or enable S3 Website Hosting.

## Step 4 – Match paths in the Vite build

When `npm run build` runs in **5.7.3**, Vite copies `frontend/public/images/` to `frontend/dist/images/`:

```text
frontend/dist/
├── index.html
├── assets/
├── favicon.svg
└── images/
    ├── banners/
    ├── categories/
    └── products/
```

Later frontend deployments use `aws s3 sync` **without `--delete`** so the uploaded images are retained. In **5.7.1**, CloudFront OAC provides private-bucket read access; users view images through CloudFront rather than direct S3 URLs.
