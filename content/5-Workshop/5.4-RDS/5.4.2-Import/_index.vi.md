---
title : "Tạo một S3 Interface endpoint"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.4.2 </b> "
---

## Step 1 – Kiểm tra file SQL trên GitHub

1. Mở **GitHub** và truy cập repository **TechVina** trong tài khoản GitHub của bạn.
2. Chọn nhánh **`master`**. Tại trang **Code**, lần lượt mở thư mục **`database` → `full`**.
3. Nhấn vào file **`phone_store.sql`** để xem nội dung trực tiếp trên GitHub.
4. Nhấn **Ctrl + F**, tìm `CREATE DATABASE` và xác nhận dòng lệnh có tên **`phone_store`**. Sau đó tìm `USE `phone_store`;` để xác nhận file chọn đúng database. Đây là các lệnh có sẵn trong file SQL, bạn không cần chạy chúng ở bước này.
5. Tìm tiếp `CREATE TABLE` và xác nhận file có các bảng như **`sanpham`**, **`danhmuc`**, **`phienban`**, **`anh`** và **`refreshtoken`**.

**Kết quả mong đợi:** File `database/full/phone_store.sql` tồn tại trong repository và chứa cấu trúc cùng dữ liệu mẫu của database `phone_store`.

![File SQL nguồn trong TechVina](/Workshop/images/5-Workshop/5.4-RDS/5.4.2-Import/01-sql-source.png)

## Step 2 – Lấy thông tin kết nối Amazon RDS

1. Trong **AWS Console**, chọn Region **Asia Pacific (Singapore) – `ap-southeast-1`**.
2. Mở **Aurora and RDS → Databases**, chọn DB instance **`techvina-mysql`** đã tạo ở mục 5.4.1.
3. Kiểm tra **Status** là **Available**. Nếu chưa phải Available, chưa thực hiện các bước kết nối.
4. Mở tab **Connectivity & security**, tìm phần **Endpoint & port**. Sao chép giá trị **Endpoint** và ghi lại **Port** (mặc định `3306`). Endpoint là tên miền máy chủ RDS, **không phải** tên database `phone_store`.
5. Mở tab **Configuration** và kiểm tra **Master username**. Sử dụng mật khẩu master mà bạn đã đặt khi tạo RDS; mật khẩu này không hiển thị lại dưới dạng văn bản trong trang thông tin DB instance.
6. Trong tab **Connectivity & security**, xác nhận **VPC** là `techvina-vpc`, **Publicly accessible** là **No**, và **VPC security group** có `techvina-rds-sg`.

Các thông tin sẽ sử dụng khi kết nối MySQL:

| Thông tin | Giá trị |
|---|---|
| RDS endpoint | Sao chép từ **Endpoint & port** |
| Port | `3306` |
| Master username | `admin` (nếu bạn đã đặt theo Step 2 của mục 5.4.1) |
| Master password | Mật khẩu đã tạo cho RDS |
| Database name | `phone_store` |

**Kết quả mong đợi:** RDS có trạng thái **Available** và đã xác định đúng endpoint, port, username cùng tên database.

Dữ liệu từ `phone_store.sql` sẽ được nhập qua **EC2 trong cùng VPC** ở **5.5.1 – Step 3**, sau khi EC2 được tạo và kết nối bằng Session Manager.