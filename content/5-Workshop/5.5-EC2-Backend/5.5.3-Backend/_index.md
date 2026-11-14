---
title: "Run Spring Boot on EC2"
weight: 3
chapter: false
pre: "<b>5.5.3.</b> "
---

## Step 1 – Connect to EC2 with Session Manager

1. In **AWS Console → EC2 → Instances**, select `techvina-ec2-backend`.
2. Choose **Connect → SSM Session Manager → Connect** to open a terminal.
3. Switch to the `ubuntu` user to access the source code and configuration in `/home/ubuntu`:

```bash
sudo -iu ubuntu
```

**Actual result:**

```text
ubuntu@ip-10-20-1-19:~$
```

The Backend source was cloned and built in **5.5.1**. The **63 MB** JAR is already available at `/home/ubuntu/projects/TechVina/backend/target/backend-0.0.1-SNAPSHOT.jar`. The `backend/.env` file already contains the RDS configuration and `JWT_SECRET`.

## Step 2 – Configure Cognito for the Backend

Open the environment file:

```bash
nano ~/projects/TechVina/backend/.env
```

Update the three Cognito variables using the User Pool, App Client, and domain created in **5.5.2**:

```dotenv
COGNITO_ISSUER_URI=https://cognito-idp.ap-southeast-1.amazonaws.com/<USER_POOL_ID>
COGNITO_CLIENT_ID=<APP_CLIENT_ID>
COGNITO_DOMAIN=https://<COGNITO_DOMAIN_PREFIX>.auth.ap-southeast-1.amazoncognito.com
```

Keep `DB_URL`, `DB_USERNAME`, `DB_PASSWORD`, and `JWT_SECRET` unchanged. Press **Ctrl + O → Enter → Ctrl + X** to save and exit.

Check the Cognito values:

```bash
grep '^COGNITO_' ~/projects/TechVina/backend/.env
```

Confirm that all seven expected variables have nonempty values without displaying the database password or JWT key:

```bash
grep -Ec '^(DB_URL|DB_USERNAME|DB_PASSWORD|JWT_SECRET|COGNITO_ISSUER_URI|COGNITO_CLIENT_ID|COGNITO_DOMAIN)=.+' ~/projects/TechVina/backend/.env
```

**Actual result:**

```text
7
```

## Step 3 – Create the Backend systemd service

Open the service definition:

```bash
sudo nano /etc/systemd/system/techvina-backend.service
```

Enter the following configuration. The service runs as `ubuntu` and loads variables directly from `.env`:

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

Save using **Ctrl + O → Enter → Ctrl + X**. The `--logging.level...` options reduce SQL, Security, and Request Mapping logging compared with the development configuration in `application.properties`.

## Step 4 – Load the service and start Spring Boot

Reload systemd definitions:

```bash
sudo systemctl daemon-reload
```

Validate the service definition:

```bash
sudo systemd-analyze verify /etc/systemd/system/techvina-backend.service
```

The validation completed **without output**. Start the Backend:

```bash
sudo systemctl start techvina-backend
```

Check the service status:

```bash
sudo systemctl status techvina-backend --no-pager
```

**Actual output from the first start:**

```text
● techvina-backend.service - TechVina Spring Boot Backend
     Loaded: loaded (/etc/systemd/system/techvina-backend.service; disabled; preset: enabled)
     Active: active (running) since Fri 2026-11-13 03:28:43 UTC; 37s ago
   Main PID: 10625 (java)
      Tasks: 38 (limit: 1741)
     Memory: 393.4M (peak: 393.5M)
```

The logs show **Tomcat started** and `Started BackendApplication` after approximately **18.9 seconds**.

## Step 5 – Check Spring Boot health

Inspect the HTTP response:

```bash
curl -i http://127.0.0.1:8080/actuator/health
```

**Actual result (main headers):**

```text
HTTP/1.1 200
Content-Type: application/vnd.spring-boot.actuator.v3+json
```

Inspect the application health value:

```bash
curl -sS http://127.0.0.1:8080/actuator/health
```

**Actual result:**

```json
{"status":"UP"}
```

## Step 6 – Check the product API backed by Amazon RDS

Locate the product controllers:

```bash
find ~/projects/TechVina/backend/src/main/java -type f -iname '*SanPham*Controller.java'
```

**Result:**

```text
/home/ubuntu/projects/TechVina/backend/src/main/java/com/phoneshop/controller/SanPhamController.java
/home/ubuntu/projects/TechVina/backend/src/main/java/com/phoneshop/controller/SanPhamChiTietController.java
```

Inspect the mappings in `SanPhamController.java`:

```bash
grep -nE '@(RequestMapping|GetMapping)' ~/projects/TechVina/backend/src/main/java/com/phoneshop/controller/SanPhamController.java
```

**Result:**

```text
16:@RequestMapping("/api/san-pham")
34:     @GetMapping("/noi-bat")
```

Check the API status:

```bash
curl -sS -o /dev/null -w 'HTTP Status: %{http_code}\n' http://127.0.0.1:8080/api/san-pham/noi-bat
```

**Actual result:**

```text
HTTP Status: 200
```

Read the JSON response:

```bash
curl -sS http://127.0.0.1:8080/api/san-pham/noi-bat | head -c 1500
```

**Actual result:**

```json
[{"maSP":21,"tenSP":"Xiaomi 15","maPB":21,"tenHang":"Xiaomi","tenDanhMuc":"Điện thoại","anhDaiDien":"/images/products/xiaomi/xiaomi-series/xiaomi-15/image_1262703568.webp","giaGoc":24540000.00,"giaSauGiam":16990000.00,"giaTriGiam":7550000.00,"phanTramGiam":31,"ram":"12GB","rom":"256GB","soLuongTon":20},{"maSP":22,"tenSP":"Xiaomi 15 Ultra","maPB":31,"tenHang":"Xiaomi","tenDanhMuc":"Điện thoại","anhDaiDien":"/images/products/xiaomi/xiaomi-series/xiaomi-15-ultra/photo_2025-04-16_11-45-51.webp","giaGoc":34360000.00,"giaSauGiam":25490000.00,"giaTriGiam":8870000.00,"phanTramGiam":26,"ram":"16GB","rom":"512GB","soLuongTon":10},{"maSP":23,"tenSP":"Xiaomi 15T","maPB":35,"tenHang":"Xiaomi","tenDanhMuc":"Điện thoại","anhDaiDien":"/images/products/xiaomi/xiaomi-series/xiaomi-15t/_i_n_tho_i_xiaomi_15t_12gb_256gb.webp","giaGoc":13990000.00,"giaSauGiam":11890000.00,"giaTriGiam":2100000.00,"phanTramGiam":15,"ram":"12GB","rom":"256GB","soLuongTon":16},{"maSP":24,"tenSP":"iPhone Duo","maPB":41,"tenHang":"Apple","tenDanhMuc":"Điện thoại","anhDaiDien":"/images/products/apple/iphone-duo/iphone-duo-star-white-pdp-image-position-1-vi-vn-015908720.webp","giaGoc":64990000.00,"giaSauGiam":64990000.00,"giaTriGiam":0,"phanTramGiam":0,"ram":"12GB","rom":"256GB","soLuongTon":11}]
```

The API returns **four featured products**, including names, pricing, RAM/ROM, available stock, and image paths from the RDS `phone_store` database.

## Step 7 – Enable the Backend to start with EC2

```bash
sudo systemctl enable techvina-backend
```

**Actual output:**

```text
Created symlink /etc/systemd/system/multi-user.target.wants/techvina-backend.service → /etc/systemd/system/techvina-backend.service.
```

Verify:

```bash
sudo systemctl is-enabled techvina-backend
```

**Result:**

```text
enabled
```

## Step 8 – Apply logging configuration and verify after restart

If the service file does not yet include the `RequestMappingHandlerMapping` logging override, reopen it:

```bash
sudo nano /etc/systemd/system/techvina-backend.service
```

Ensure the end of the `ExecStart=` line contains:

```text
--logging.level.org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping=INFO
```

Then validate and apply the service changes, one command at a time:

```bash
sudo systemd-analyze verify /etc/systemd/system/techvina-backend.service
```

```bash
sudo systemctl daemon-reload
```

```bash
sudo systemctl restart techvina-backend
```

Check the status:

```bash
sudo systemctl status techvina-backend --no-pager
```

**Actual status after restart:**

```text
● techvina-backend.service - TechVina Spring Boot Backend
     Loaded: loaded (/etc/systemd/system/techvina-backend.service; enabled; preset: enabled)
     Active: active (running) since Fri 2026-11-13 03:39:45 UTC; 21s ago
   Main PID: 10855 (java)
      Tasks: 39 (limit: 1741)
     Memory: 313.5M (peak: 314.9M)
```

Run the final Health Check:

```bash
curl -sS -w '\nHTTP Status: %{http_code}\n' http://127.0.0.1:8080/actuator/health
```

**Actual result:**

```text
{"status":"UP"}
HTTP Status: 200
```
