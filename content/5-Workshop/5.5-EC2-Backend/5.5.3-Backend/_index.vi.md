---
title: "Chạy Spring Boot trên EC2"
weight: 3
chapter: false
pre: "<b>5.5.3.</b> "
---

## Step 1 – Kết nối EC2 bằng Session Manager

1. Trong **AWS Console → EC2 → Instances**, chọn `techvina-ec2-backend`.
2. Nhấn **Connect → SSM Session Manager → Connect** để mở terminal.
3. Chuyển sang người dùng `ubuntu` để sử dụng mã nguồn và file cấu hình trong `/home/ubuntu`:

```bash
sudo -iu ubuntu
```

**Kết quả:**

```text
ubuntu@ip-10-20-1-19:~$
```

Mã nguồn Backend đã được clone và build ở **5.5.1**. File JAR hiện có tại `/home/ubuntu/projects/TechVina/backend/target/backend-0.0.1-SNAPSHOT.jar` với dung lượng **63 MB**. File `backend/.env` đã có cấu hình RDS và `JWT_SECRET`.

## Step 2 – Cấu hình Cognito trong Backend

Mở file môi trường:

```bash
nano ~/projects/TechVina/backend/.env
```

Cập nhật ba biến Cognito theo User Pool, App Client và domain đã tạo tại **5.5.2**:

```dotenv
COGNITO_ISSUER_URI=https://cognito-idp.ap-southeast-1.amazonaws.com/<USER_POOL_ID>
COGNITO_CLIENT_ID=<APP_CLIENT_ID>
COGNITO_DOMAIN=https://<COGNITO_DOMAIN_PREFIX>.auth.ap-southeast-1.amazoncognito.com
```

Giữ nguyên các biến `DB_URL`, `DB_USERNAME`, `DB_PASSWORD` và `JWT_SECRET`. Nhấn **Ctrl + O → Enter → Ctrl + X** để lưu và thoát.

Kiểm tra ba giá trị Cognito:

```bash
grep '^COGNITO_' ~/projects/TechVina/backend/.env
```

Kiểm tra đủ bảy biến không rỗng mà không in mật khẩu hoặc khóa JWT:

```bash
grep -Ec '^(DB_URL|DB_USERNAME|DB_PASSWORD|JWT_SECRET|COGNITO_ISSUER_URI|COGNITO_CLIENT_ID|COGNITO_DOMAIN)=.+' ~/projects/TechVina/backend/.env
```

**Kết quả thực tế:**

```text
7
```

## Step 3 – Tạo systemd service cho Backend

Mở file dịch vụ:

```bash
sudo nano /etc/systemd/system/techvina-backend.service
```

Nhập cấu hình sau. Dịch vụ chạy bằng tài khoản `ubuntu` và nạp biến môi trường trực tiếp từ file `.env`:

```ini
[Unit]
Description=TechVina Spring Boot Backend
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=ubuntu
Group=ubuntu
WorkingDirectory=/home/ubuntu/projects/TechVina/backend
EnvironmentFile=/home/ubuntu/projects/TechVina/backend/.env
ExecStart=/usr/bin/java -jar /home/ubuntu/projects/TechVina/backend/target/backend-0.0.1-SNAPSHOT.jar --spring.jpa.show-sql=false --logging.level.org.hibernate.orm.jdbc.bind=OFF --logging.level.org.hibernate.SQL=WARN --logging.level.org.springframework.security=INFO --logging.level.org.springframework.web=INFO --logging.level.org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping=INFO
Restart=on-failure
RestartSec=5
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target
```

Lưu file bằng **Ctrl + O → Enter → Ctrl + X**. Các tham số `--logging.level...` giảm log SQL, Security và Request Mapping so với cấu hình phát triển trong `application.properties`.

## Step 4 – Nạp cấu hình và khởi động Spring Boot

Nạp cấu hình systemd:

```bash
sudo systemctl daemon-reload
```

Kiểm tra file service:

```bash
sudo systemd-analyze verify /etc/systemd/system/techvina-backend.service
```

Lệnh kiểm tra hoàn tất **không có output**. Khởi động Backend:

```bash
sudo systemctl start techvina-backend
```

Kiểm tra trạng thái:

```bash
sudo systemctl status techvina-backend --no-pager
```

**Kết quả thực tế khi khởi động lần đầu:**

```text
● techvina-backend.service - TechVina Spring Boot Backend
     Loaded: loaded (/etc/systemd/system/techvina-backend.service; disabled; preset: enabled)
     Active: active (running) since Fri 2026-11-13 03:28:43 UTC; 37s ago
   Main PID: 10625 (java)
      Tasks: 38 (limit: 1741)
     Memory: 393.4M (peak: 393.5M)
```

Log ghi nhận **Tomcat đã khởi động** và `Started BackendApplication` sau khoảng **18,9 giây**.

## Step 5 – Kiểm tra Spring Boot Health Check

Kiểm tra HTTP response:

```bash
curl -i http://127.0.0.1:8080/actuator/health
```

**Kết quả thực tế (các header chính):**

```text
HTTP/1.1 200
Content-Type: application/vnd.spring-boot.actuator.v3+json
```

Kiểm tra trạng thái ứng dụng:

```bash
curl -sS http://127.0.0.1:8080/actuator/health
```

**Kết quả thực tế:**

```json
{"status":"UP"}
```

## Step 6 – Kiểm tra API sản phẩm từ Amazon RDS

Xác định Controller của API sản phẩm:

```bash
find ~/projects/TechVina/backend/src/main/java -type f -iname '*SanPham*Controller.java'
```

**Kết quả:**

```text
/home/ubuntu/projects/TechVina/backend/src/main/java/com/phoneshop/controller/SanPhamController.java
/home/ubuntu/projects/TechVina/backend/src/main/java/com/phoneshop/controller/SanPhamChiTietController.java
```

Kiểm tra mapping trong `SanPhamController.java`:

```bash
grep -nE '@(RequestMapping|GetMapping)' ~/projects/TechVina/backend/src/main/java/com/phoneshop/controller/SanPhamController.java
```

**Kết quả:**

```text
16:@RequestMapping("/api/san-pham")
34:     @GetMapping("/noi-bat")
```

Kiểm tra HTTP status của API:

```bash
curl -sS -o /dev/null -w 'HTTP Status: %{http_code}\n' http://127.0.0.1:8080/api/san-pham/noi-bat
```

**Kết quả thực tế:**

```text
HTTP Status: 200
```

Đọc phản hồi JSON:

```bash
curl -sS http://127.0.0.1:8080/api/san-pham/noi-bat | head -c 1500
```

**Kết quả thực tế:**

```json
[{"maSP":21,"tenSP":"Xiaomi 15","maPB":21,"tenHang":"Xiaomi","tenDanhMuc":"Điện thoại","anhDaiDien":"/images/products/xiaomi/xiaomi-series/xiaomi-15/image_1262703568.webp","giaGoc":24540000.00,"giaSauGiam":16990000.00,"giaTriGiam":7550000.00,"phanTramGiam":31,"ram":"12GB","rom":"256GB","soLuongTon":20},{"maSP":22,"tenSP":"Xiaomi 15 Ultra","maPB":31,"tenHang":"Xiaomi","tenDanhMuc":"Điện thoại","anhDaiDien":"/images/products/xiaomi/xiaomi-series/xiaomi-15-ultra/photo_2025-04-16_11-45-51.webp","giaGoc":34360000.00,"giaSauGiam":25490000.00,"giaTriGiam":8870000.00,"phanTramGiam":26,"ram":"16GB","rom":"512GB","soLuongTon":10},{"maSP":23,"tenSP":"Xiaomi 15T","maPB":35,"tenHang":"Xiaomi","tenDanhMuc":"Điện thoại","anhDaiDien":"/images/products/xiaomi/xiaomi-series/xiaomi-15t/_i_n_tho_i_xiaomi_15t_12gb_256gb.webp","giaGoc":13990000.00,"giaSauGiam":11890000.00,"giaTriGiam":2100000.00,"phanTramGiam":15,"ram":"12GB","rom":"256GB","soLuongTon":16},{"maSP":24,"tenSP":"iPhone Duo","maPB":41,"tenHang":"Apple","tenDanhMuc":"Điện thoại","anhDaiDien":"/images/products/apple/iphone-duo/iphone-duo-star-white-pdp-image-position-1-vi-vn-015908720.webp","giaGoc":64990000.00,"giaSauGiam":64990000.00,"giaTriGiam":0,"phanTramGiam":0,"ram":"12GB","rom":"256GB","soLuongTon":11}]
```

API trả về **4 sản phẩm nổi bật** với tên, giá, cấu hình RAM/ROM, tồn kho và đường dẫn ảnh từ database `phone_store` trên RDS.

## Step 7 – Cho phép Backend tự khởi động cùng EC2

```bash
sudo systemctl enable techvina-backend
```

**Kết quả thực tế:**

```text
Created symlink /etc/systemd/system/multi-user.target.wants/techvina-backend.service → /etc/systemd/system/techvina-backend.service.
```

Xác nhận:

```bash
sudo systemctl is-enabled techvina-backend
```

**Kết quả:**

```text
enabled
```

## Step 8 – Áp dụng cấu hình log và xác nhận sau restart

Nếu file service ban đầu chưa có tham số giảm log của `RequestMappingHandlerMapping`, mở lại:

```bash
sudo nano /etc/systemd/system/techvina-backend.service
```

Đảm bảo cuối dòng `ExecStart=` có tham số:

```text
--logging.level.org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping=INFO
```

Sau đó kiểm tra và nạp lại cấu hình, lần lượt:

```bash
sudo systemd-analyze verify /etc/systemd/system/techvina-backend.service
```

```bash
sudo systemctl daemon-reload
```

```bash
sudo systemctl restart techvina-backend
```

Kiểm tra service:

```bash
sudo systemctl status techvina-backend --no-pager
```

**Kết quả thực tế sau restart:**

```text
● techvina-backend.service - TechVina Spring Boot Backend
     Loaded: loaded (/etc/systemd/system/techvina-backend.service; enabled; preset: enabled)
     Active: active (running) since Fri 2026-11-13 03:39:45 UTC; 21s ago
   Main PID: 10855 (java)
      Tasks: 39 (limit: 1741)
     Memory: 313.5M (peak: 314.9M)
```

Kiểm tra Health Check lần cuối:

```bash
curl -sS -w '\nHTTP Status: %{http_code}\n' http://127.0.0.1:8080/actuator/health
```

**Kết quả thực tế:**

```text
{"status":"UP"}
HTTP Status: 200
```
