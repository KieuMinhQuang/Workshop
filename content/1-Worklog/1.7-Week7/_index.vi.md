---
title: "Worklog Tuần 7"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.7. </b> "
---



### Mục tiêu tuần 7:

* Tìm hiểu AWS Backup và cách xây dựng hệ thống sao lưu, khôi phục dữ liệu tự động trên AWS.
* Thực hành tạo Backup Plan, cấu hình Backup Vault và thiết lập lịch sao lưu cho các tài nguyên AWS.
* Tìm hiểu và kết hợp AWS SNS, AWS Lambda để tự động hóa quá trình thông báo, kiểm tra và khôi phục dữ liệu.
* Kiểm tra kết quả Backup/Restore và tìm hiểu quy trình quản lý, dọn dẹp tài nguyên sau khi thực hành.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | Tìm hiểu tổng quan AWS Backup và thực hành tạo S3 Bucket để lưu trữ CloudFormation Template, Lambda Function phục vụ quá trình triển khai hệ thống sao lưu. | 14/09/2026 | 14/09/2026 | <https://000013.awsfcaj.com/> |
| 3 | Thực hành sử dụng AWS CloudFormation để triển khai hạ tầng gồm EC2 Instance, SNS Topic và Lambda Function; kiểm tra ứng dụng và xác nhận đăng ký nhận thông báo qua email. | 15/09/2026 | 15/09/2026 | <https://000013.awsfcaj.com/> |
| 4 | Tìm hiểu và thực hành tạo AWS Backup Plan, cấu hình Backup Rule với lịch sao lưu hằng ngày, tạo Backup Vault và thiết lập Resource Assignment cho các tài nguyên cần sao lưu. | 16/09/2026 | 16/09/2026 | <https://000013.awsfcaj.com/> |
| 5 | Tìm hiểu và cấu hình AWS SNS để gửi thông báo khi Backup Job và Restore Job hoàn thành; thực hành sử dụng AWS CLI để thiết lập và kiểm tra trạng thái thông báo. | 17/09/2026 | 17/09/2026 | <https://000013.awsfcaj.com/> |
| 6 | Thực hành tạo On-demand Backup, kiểm tra quá trình khôi phục dữ liệu tự động bằng AWS Lambda, theo dõi trạng thái qua CloudWatch Logs và thực hiện dọn dẹp các tài nguyên AWS sau Workshop. | 18/09/2026 | 18/09/2026 | <https://000013.awsfcaj.com/> |

### Kết quả đạt được tuần 7:

* Hiểu được vai trò của AWS Backup trong việc quản lý và tự động hóa quá trình sao lưu, khôi phục dữ liệu.
* Thực hành tạo S3 Bucket và sử dụng CloudFormation để triển khai hạ tầng phục vụ hệ thống Backup.
* Nắm được cách tạo Backup Plan, cấu hình Backup Rule, Backup Vault và phân công tài nguyên cần sao lưu.
* Hiểu và thực hành kết hợp AWS SNS, AWS Lambda để tự động gửi thông báo và kiểm tra quá trình Backup/Restore.
* Biết cách kiểm tra kết quả khôi phục thông qua CloudWatch Logs và thực hiện dọn dẹp tài nguyên AWS sau khi hoàn thành Workshop.