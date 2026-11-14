---
title : "Create a gateway endpoint"
date : 2024-01-01 
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---

## Step 1 – Create the VPC

1. Open **VPC Console → Your VPCs → Create VPC**.
2. Choose **VPC only**, name `techvina-vpc`, IPv4 CIDR `10.20.0.0/16`, tenancy `Default`.
3. Select **Create VPC**. Under **Actions → Edit VPC settings**, enable **DNS resolution** and **DNS hostnames**.

![VPC and subnet configuration](/Workshop/images/5-Workshop/5.3-IAM-VPC/5.3.1-VPC/01-vpc-create.png)

## Step 2 – Create three subnets

Under **VPC → Subnets → Create subnet**, select the VPC and create:

| Subnet | Availability Zone | IPv4 CIDR | Purpose |
|---|---|---|---|
| `techvina-public-a` | `ap-southeast-1a` | `10.20.1.0/24` | EC2 |
| `techvina-private-a` | `ap-southeast-1a` | `10.20.11.0/24` | RDS |
| `techvina-private-b` | `ap-southeast-1b` | `10.20.12.0/24` | RDS |

For the public subnet, enable **auto-assign public IPv4**. Leave it disabled for both private subnets.

![Three TechVina VPC subnets](/Workshop/images/5-Workshop/5.3-IAM-VPC/5.3.1-VPC/02-subnets.png)

## Step 3 – Internet Gateway and routing

1. Go to **Internet gateways → Create internet gateway**, name `techvina-igw`, and **Attach to VPC**.
2. Select **Route tables → Create route table**, name it `techvina-public-rt`, and select the VPC.
3. Add route `0.0.0.0/0 → techvina-igw` under **Routes → Edit routes**.
4. Associate the route table with `techvina-public-a`.
5. Create `techvina-private-rt` for both private subnets. Keep only the local VPC route; do not add a default Internet route to private subnets.

![Configure route tables and Internet gateway](/Workshop/images/5-Workshop/5.3-IAM-VPC/5.3.1-VPC/03-route-table.png)

## Step 4 – Validate

Inspect **VPC → Resource map**: the public subnet has an IGW route while the private subnets have no Internet default route. Record the **VPC ID** and all **Subnet IDs** for EC2 and RDS.