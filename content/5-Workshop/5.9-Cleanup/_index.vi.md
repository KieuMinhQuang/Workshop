---
title: "Dọn dẹp tài nguyên AWS"
weight: 9
chapter: false
pre: "<b>5.9.</b> "
---

## Step 1 – Chuẩn bị và xác định tài nguyên cần xóa

Hệ thống TechVina được triển khai tại Region **Asia Pacific (Singapore) – `ap-southeast-1`**, gồm **Amazon EC2, Amazon RDS MySQL, Amazon S3, Amazon CloudFront, Amazon Cognito, Amazon VPC, Amazon CloudWatch, Amazon SNS và IAM**. Sau khi hoàn thành triển khai và kiểm thử, tiến hành dọn dẹp các tài nguyên đã tạo.

1. Đăng nhập **AWS Management Console**, chọn Region **Singapore (`ap-southeast-1`)**. Đối chiếu tên và ID của tài nguyên trước khi xóa.
2. Mở repository **GitHub → `TaiHoang69/TechVina` → Actions**. Vô hiệu hóa các workflow triển khai Frontend và Backend để những lần đẩy mã nguồn tiếp theo không kích hoạt lại quy trình triển khai lên AWS.
3. Lưu về máy cá nhân các dữ liệu cần giữ, gồm mã nguồn, bản sao SQL của MySQL, hình ảnh sản phẩm, tài liệu Workshop và các tệp triển khai cần thiết. Kiểm tra dữ liệu đã lưu trước khi xóa bản trên AWS.
4. Xác định các tài nguyên chính của hệ thống:

   | Dịch vụ | Tài nguyên TechVina |
   |---|---|
   | Amazon EC2 | `techvina-ec2-backend` |
   | Amazon RDS | Cơ sở dữ liệu MySQL và `techvina-db-subnet-group` |
   | Amazon S3 | `techvina-frontend-668668940018`, `techvina-backend-artifacts-668668940018` |
   | Amazon CloudFront | Distribution `E12UZ3V9Z7AGAU` và Origin Access Control (OAC) |
   | Amazon Cognito | `techvina-user-pool` |
   | Amazon CloudWatch | Alarm `techvina-backend-high-cpu`, Log Group `/techvina/backend/application` |
   | Amazon SNS | Topic `techvina-backend-cpu-alerts` |
   | Amazon VPC | `techvina-vpc` và các thành phần mạng thuộc VPC |
   | AWS IAM | Các IAM Roles của EC2, GitHub Actions và GitHub OIDC Provider |

## Step 2 – Xóa Amazon CloudFront

1. Truy cập **Amazon CloudFront → Distributions** và chọn Distribution của TechVina (`E12UZ3V9Z7AGAU`).
2. Chọn **Disable** và xác nhận. Sau khi trạng thái cập nhật hoàn tất, chọn **Delete** để xóa Distribution.
3. Truy cập **CloudFront → Functions**. Nếu còn Function `techvina-spa-routing`, kiểm tra Function không còn liên kết với Distribution rồi xóa.
4. Truy cập **CloudFront → Origin access**, tìm OAC đã sử dụng để truy cập S3 Frontend, xác nhận OAC không còn được Distribution khác sử dụng rồi xóa.
5. Quay lại **Distributions** và xác nhận không còn Distribution của TechVina.

## Step 3 – Xóa EC2 Backend và các tài nguyên liên quan

1. Truy cập **Amazon EC2 → Instances**, chọn instance `techvina-ec2-backend` (`i-0de916c9bd054d655`).
2. Mở tab **Storage** để kiểm tra EBS Volume đi kèm. Chọn **Instance state → Terminate (delete) instance** và xác nhận.
3. Sau khi instance chuyển sang trạng thái **Terminated**, truy cập **EC2 → Volumes**. Nếu Volume của TechVina vẫn tồn tại và không còn được sử dụng, chọn **Actions → Delete volume**.
4. Truy cập **EC2 → Snapshots** và **AMIs**, chọn bộ lọc **Owned by me**. Xóa Snapshot hoặc AMI được tạo riêng cho TechVina nếu còn tồn tại; hủy đăng ký AMI trước khi xóa Snapshot liên quan khi cần.
5. Truy cập **EC2 → Elastic IPs**. Nếu có Elastic IP được cấp riêng cho TechVina, hủy liên kết rồi chọn **Release Elastic IP address**.
6. Kiểm tra **EC2 → Network Interfaces**; bảo đảm không còn Network Interface thuộc EC2 Backend. Network Interface do dịch vụ khác quản lý phải được xử lý từ dịch vụ tương ứng.

## Step 4 – Xóa Amazon RDS MySQL và tài nguyên cơ sở dữ liệu

1. Truy cập **Amazon RDS → Databases** tại Region Singapore và chọn cơ sở dữ liệu MySQL của TechVina.
2. Nếu đang bật **Deletion protection**, chọn **Modify** để tắt trước khi xóa.
3. Chọn **Actions → Delete**. Sau khi đã sao lưu dữ liệu cần thiết ra ngoài AWS, chọn không tạo **Final snapshot** và không giữ lại **Automated backups**, sau đó xác nhận.
4. Truy cập **RDS → Snapshots → Manual**, kiểm tra và xóa các Snapshot thuộc TechVina nếu còn tồn tại.
5. Truy cập **RDS → Automated backups**, kiểm tra lần lượt các tab **Current Region**, **Retained** và **Replicated**. Xóa các bản sao lưu còn lại của TechVina nếu có.
6. Truy cập **RDS → Subnet groups**, chọn `techvina-db-subnet-group` và nhấn **Delete** sau khi cơ sở dữ liệu đã được xóa.
7. Kiểm tra **RDS → Parameter groups** và **Option groups**. Chỉ xóa các nhóm tùy chỉnh được tạo riêng cho TechVina; giữ nguyên các nhóm mặc định của AWS.

Kết quả kiểm tra: **DB Subnet groups (0)**, các tab **Automated backups** đều hiển thị **0** và không còn Manual Snapshot của TechVina.

## Step 5 – Xóa hai S3 Buckets

Truy cập **Amazon S3 → General purpose buckets**. Hệ thống sử dụng hai bucket:

| Bucket | Nội dung |
|---|---|
| `techvina-frontend-668668940018` | `index.html`, `assets/`, `images/` và các tệp Frontend |
| `techvina-backend-artifacts-668668940018` | Tệp JAR và script triển khai Backend trong `releases/` |

Với mỗi bucket, thực hiện:

1. Chọn bucket → **Empty** và xác nhận xóa toàn bộ Objects. Nếu có Versioning, kiểm tra cả các phiên bản Object và Delete Markers.
2. Sau khi bucket rỗng, quay lại danh sách S3 Buckets, chọn bucket → **Delete**.
3. Nhập chính xác tên bucket theo yêu cầu xác nhận và nhấn **Delete bucket**.

Kết quả kiểm tra: thông báo xóa bucket thành công và danh sách **General purpose buckets (0)**.

## Step 6 – Xóa CloudWatch và Amazon SNS

### 6.1. Xóa CloudWatch Alarm

1. Truy cập **Amazon CloudWatch → Alarms** tại Region Singapore.
2. Chọn Alarm `techvina-backend-high-cpu` → **Actions → Delete** → xác nhận.
3. Kiểm tra danh sách hiển thị **Alarms (0)**.

### 6.2. Xóa CloudWatch Log Group

1. Truy cập **CloudWatch → Logs → Log management → Log groups**.
2. Chọn `/techvina/backend/application` → **Actions → Delete log group(s)**.
3. Xác nhận xóa và kiểm tra **Log groups (0)**.

### 6.3. Xóa SNS Topic và Subscription

1. Truy cập **Amazon SNS → Topics**.
2. Chọn Topic `techvina-backend-cpu-alerts` → **Delete**.
3. Nhập `delete me` trong hộp xác nhận và nhấn **Delete**. Kiểm tra **Topics (0)**.
4. Truy cập **SNS → Subscriptions**, xóa Subscription email còn liên quan đến Topic TechVina nếu vẫn xuất hiện.
5. Xác nhận danh sách **Subscriptions (0)**.

## Step 7 – Xóa Amazon Cognito và AWS IAM

### 7.1. Xóa Cognito User Pool

1. Truy cập **Amazon Cognito → User pools**.
2. Chọn `techvina-user-pool` (User Pool ID `ap-southeast-1_Gt4JGeuQM`) → **Delete**.
3. Đánh dấu các điều kiện xác nhận liên quan đến Cognito Domain và Deletion protection nếu giao diện yêu cầu.
4. Nhập `techvina-user-pool` và nhấn **Delete**.
5. Kiểm tra **User pools (0)** và **Identity pools (0)**.

### 7.2. Xóa IAM Roles

Truy cập **IAM → Roles** và xóa ba Role thuộc hệ thống TechVina:

- `techvina-ec2-ssm-role`
- `techvina-github-actions-backend-role`
- `techvina-github-actions-frontend-role`

Chọn các Role, nhấn **Delete**, nhập `delete` để xác nhận. Kiểm tra các Role đã biến mất khỏi danh sách; không xóa **Service-linked Roles**, `AdminRole` hoặc Role của hệ thống khác.

### 7.3. Xóa GitHub OIDC Provider

1. Truy cập **IAM → Identity providers**.
2. Chọn `token.actions.githubusercontent.com` sau khi xác nhận không còn IAM Role hoặc repository khác phụ thuộc vào Provider này.
3. Chọn **Delete**, nhập `confirm` và xác nhận.
4. Kiểm tra **Identity providers (0)**.

## Step 8 – Xóa Amazon VPC và các thành phần mạng

1. Truy cập **Amazon VPC → Your VPCs** tại Region Singapore. Xác định `techvina-vpc` (`vpc-04e52f7fe815354f2`), không chọn VPC mặc định.
2. Kiểm tra **VPC → NAT gateways**; kết quả thực tế không còn NAT Gateway nào.
3. Sau khi EC2, RDS và DB Subnet Group đã được xóa, chọn `techvina-vpc` → **Actions → Delete VPC**.
4. Trong hộp xác nhận, kiểm tra các tài nguyên AWS sẽ xóa cùng VPC, gồm các Subnet, Route Table, Internet Gateway và Security Group không mặc định thuộc VPC. Nhập `delete` và nhấn **Delete**.
5. Kiểm tra thông báo xóa thành công. Danh sách **Your VPCs** chỉ còn **Default VPC** (`vpc-074982beea4ca3bfa`).
6. Truy cập **EC2 → Security Groups**, **Network Interfaces**, **Elastic IPs** để xác nhận không còn tài nguyên mạng TechVina. Security Group mặc định của Default VPC được giữ nguyên.

## Step 9 – Kiểm tra kết quả dọn dẹp

Kiểm tra lại các dịch vụ đã sử dụng trong Workshop tại `ap-southeast-1`; riêng CloudFront và IAM được quản lý ở phạm vi toàn cục.

| Dịch vụ | Kết quả xác nhận |
|---|---|
| Amazon CloudFront | Distribution TechVina đã xóa |
| Amazon EC2 | EC2 Backend đã terminate; không còn Elastic IP, Network Interface, Snapshot hoặc AMI của TechVina |
| Amazon RDS | DB MySQL và DB Subnet Group đã xóa; không còn Manual/Retained/Replicated Backups của TechVina |
| Amazon S3 | General purpose buckets: **0** |
| Amazon CloudWatch | Alarms: **0**; Log groups: **0** |
| Amazon SNS | Topics: **0**; Subscriptions: **0** |
| Amazon Cognito | User pools: **0**; Identity pools: **0** |
| AWS IAM | Ba IAM Roles TechVina và GitHub OIDC Provider đã xóa |
| Amazon VPC | `techvina-vpc` và các thành phần phụ thuộc đã xóa; Default VPC vẫn tồn tại |

Hệ thống TechVina đã được gỡ khỏi môi trường AWS thực hành. Repository GitHub và dữ liệu đã sao lưu ngoài AWS được giữ lại để phục vụ phát triển và triển khai lại khi cần.
