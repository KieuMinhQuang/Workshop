---
title : "Create an S3 Interface endpoint"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.4.2 </b> "
---

## Step 1 – Check the SQL file on GitHub

1. Open **GitHub** and go to the **TechVina** repository in your own GitHub account.
2. Select the **`master`** branch. On the **Code** tab, open **`database` → `full`**.
3. Select **`phone_store.sql`** to view the SQL file directly in GitHub.
4. Press **Ctrl + F** and search for `CREATE DATABASE`. Confirm that the statement contains the database name **`phone_store`**. Then find `USE `phone_store`;` to verify that the file selects the same database. These statements are already in the SQL file; do not execute them at this step.
5. Search for `CREATE TABLE` and confirm that the file defines tables such as **`sanpham`**, **`danhmuc`**, **`phienban`**, **`anh`** and **`refreshtoken`**.

**Expected result:** `database/full/phone_store.sql` exists in the repository and contains the schema and sample data for the `phone_store` database.

![TechVina SQL source file](/Workshop/images/5-Workshop/5.4-RDS/5.4.2-Import/01-sql-source.png)

## Step 2 – Retrieve Amazon RDS connection details

1. In the **AWS Console**, select **Asia Pacific (Singapore) – `ap-southeast-1`**.
2. Open **Aurora and RDS → Databases** and select the **`techvina-mysql`** DB instance created in Section 5.4.1.
3. Confirm that the **Status** is **Available**. Do not proceed with connection steps until the instance is available.
4. Open **Connectivity & security** and locate **Endpoint & port**. Copy the **Endpoint** and note the **Port** (normally `3306`). The endpoint is the RDS server hostname; it is **not** the `phone_store` database name.
5. Open **Configuration** and check the **Master username**. Use the master password set when you created the RDS instance; it is not displayed again as plain text on the DB instance details page.
6. In **Connectivity & security**, verify that **VPC** is `techvina-vpc`, **Publicly accessible** is **No**, and the attached **VPC security group** includes `techvina-rds-sg`.

Use the following values for the subsequent MySQL connection:

| Setting | Value |
|---|---|
| RDS endpoint | Copy from **Endpoint & port** |
| Port | `3306` |
| Master username | `admin` (if you followed Section 5.4.1, Step 2) |
| Master password | Password configured for RDS |
| Database name | `phone_store` |

**Expected result:** RDS is **Available**, and the endpoint, port, username, and database name have been identified.

You will import `phone_store.sql` from **EC2 in the same VPC** in **Section 5.5.1 – Step 3**, after launching EC2 and connecting through Session Manager.