---
title: "Worklog Tuần 4"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.4. </b> "
---


### Mục tiêu tuần 4:

* Tìm hiểu cách cấp quyền cho ứng dụng truy cập các dịch vụ AWS thông qua IAM.
* Thực hành sử dụng Access Key và Secret Access Key để kết nối ứng dụng Python với dịch vụ Amazon S3.
* Tìm hiểu và thực hành sử dụng IAM Role trên EC2 nhằm tăng cường bảo mật và hạn chế việc lưu trữ thông tin xác thực trong mã nguồn.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | Chuẩn bị môi trường thực hành bằng cách tạo EC2 Instance (Amazon Linux) và S3 Bucket trên AWS Console. | 24/08/2026 | 24/08/2026 | <https://000048.awsfcaj.com/> |
| 3 | Tìm hiểu và thực hành tạo IAM User, cấp quyền truy cập S3 và tạo Access Key, Secret Access Key để xác thực ứng dụng. | 25/08/2026 | 25/08/2026 | <https://000048.awsfcaj.com/> |
| 4 | Thực hành sử dụng Access Key trong ứng dụng Python với thư viện Boto3 để tải tệp lên S3; tìm hiểu các rủi ro bảo mật khi lưu thông tin xác thực trực tiếp trong mã nguồn. | 26/08/2026 | 26/08/2026 | <https://000048.awsfcaj.com/> |
| 5 | Tìm hiểu và thực hành tạo IAM Role, gán Role cho EC2 Instance và sử dụng IAM Role để cấp quyền cho ứng dụng truy cập S3 mà không cần khai báo Access Key trong mã nguồn. | 27/08/2026 | 27/08/2026 | <https://000048.awsfcaj.com/> |
| 6 | Kiểm tra kết quả tải tệp lên S3 bằng IAM Role, tìm hiểu cơ chế cấp phát Temporary Credentials thông qua AWS STS và thực hành dọn dẹp các tài nguyên AWS sau Workshop. | 28/08/2026 | 28/08/2026 | <https://000048.awsfcaj.com/> |

### Kết quả đạt được tuần 4:

* Hiểu được cách chuẩn bị môi trường thực hành và kết nối giữa EC2 Instance với Amazon S3.
* Thực hành tạo IAM User, Access Key và sử dụng thư viện Boto3 để ứng dụng Python tương tác với S3.
* Hiểu được rủi ro bảo mật khi lưu Access Key và Secret Access Key trực tiếp trong mã nguồn.
* Thực hành tạo và gán IAM Role cho EC2 Instance, giúp ứng dụng truy cập dịch vụ AWS thông qua Temporary Credentials.
* Phân biệt được phương pháp xác thực bằng Access Key và IAM Role, hiểu được lợi ích bảo mật của IAM Role và quy trình dọn dẹp tài nguyên AWS.