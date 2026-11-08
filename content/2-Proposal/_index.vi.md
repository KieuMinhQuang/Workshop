---
title: "Bản đề xuất"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---


# Xây dựng ứng dụng web bán điện thoại trên nền tảng điện toán đám mây AWS

### 1. Tóm tắt điều hành

Dự án hướng đến xây dựng và triển khai một ứng dụng web bán điện thoại trên nền tảng AWS, phục vụ hai nhóm người dùng chính: khách hàng và quản trị viên. Khách hàng có thể xem sản phẩm, tìm kiếm điện thoại, đăng ký, đăng nhập và đặt hàng. Quản trị viên có thể quản lý sản phẩm, hình ảnh, đơn hàng và người dùng trong phạm vi chức năng của ứng dụng.

Ứng dụng sử dụng ReactJS để xây dựng giao diện, Java Spring Boot để xử lý nghiệp vụ và MySQL để lưu trữ dữ liệu. Hệ thống được triển khai trên AWS với Amazon S3 và Amazon CloudFront phục vụ giao diện và hình ảnh, Amazon EC2 chạy máy chủ ứng dụng, Amazon RDS quản lý cơ sở dữ liệu, Amazon Cognito xác thực người dùng và Amazon CloudWatch giám sát hoạt động. Amazon VPC và AWS IAM hỗ trợ cấu hình mạng và phân quyền truy cập.

Mục tiêu là hoàn thành một hệ thống bán điện thoại có thể truy cập và kiểm thử trực tuyến, đồng thời thực hành quy trình triển khai một ứng dụng web nhiều thành phần trên AWS.

### 2. Tuyên bố vấn đề

**Vấn đề hiện tại**

Một ứng dụng bán điện thoại cần quản lý tập trung thông tin sản phẩm, tài khoản và đơn hàng. Khi chỉ chạy ứng dụng trên máy cá nhân, nhóm khó kiểm thử việc truy cập từ Internet, kết nối giữa các thành phần và vận hành hệ thống trong môi trường triển khai thực tế.

**Giải pháp đề xuất**

Nhóm xây dựng ứng dụng theo mô hình giao diện, máy chủ xử lý nghiệp vụ và cơ sở dữ liệu. ReactJS cung cấp giao diện cho khách hàng và quản trị viên; Spring Boot cung cấp các giao diện lập trình để xử lý nghiệp vụ; MySQL lưu trữ dữ liệu. Các thành phần được triển khai trên AWS và kết nối thông qua cấu hình mạng, phân quyền phù hợp.

Việc sử dụng Amazon Cognito giúp triển khai chức năng đăng ký, đăng nhập và xác thực người dùng. Amazon CloudWatch được sử dụng để theo dõi trạng thái máy chủ và nhật ký hoạt động.

### 3. Kiến trúc giải pháp

Hệ thống được thiết kế với các thành phần chính như sau: người dùng truy cập ứng dụng qua Amazon CloudFront; giao diện ReactJS và hình ảnh sản phẩm được lưu trên Amazon S3; các yêu cầu nghiệp vụ được chuyển đến ứng dụng Spring Boot trên Amazon EC2; ứng dụng kết nối đến cơ sở dữ liệu MySQL trên Amazon RDS. Amazon Cognito thực hiện xác thực người dùng, còn Amazon CloudWatch hỗ trợ giám sát.

![Sơ đồ kiến ​​trúc AWS](/Workshop/images/2-Proposal/SoDoKienTrucAWS.drawio.png)

**Các dịch vụ AWS sử dụng**

| Dịch vụ | Vai trò |
|---|---|
| Amazon VPC | Tạo môi trường mạng cho máy chủ ứng dụng và cơ sở dữ liệu. |
| AWS IAM | Quản lý quyền truy cập tài nguyên AWS. |
| Amazon EC2 | Chạy ứng dụng Java Spring Boot và các giao diện lập trình. |
| Amazon RDS | Lưu trữ và quản lý cơ sở dữ liệu MySQL. |
| Amazon S3 | Lưu trữ giao diện ReactJS sau khi biên dịch và hình ảnh sản phẩm. |
| Amazon CloudFront | Phân phối giao diện, hình ảnh và chuyển tiếp yêu cầu đến máy chủ ứng dụng. |
| Amazon Cognito | Đăng ký, đăng nhập và xác thực người dùng bằng mã xác thực JWT. |
| Amazon CloudWatch | Thu thập chỉ số, nhật ký và hỗ trợ theo dõi hệ thống. |

**Thiết kế thành phần**

- **Giao diện:** Hai giao diện khách hàng và quản trị viên được phát triển bằng ReactJS, HTML, CSS, JavaScript và Bootstrap; bản biên dịch được lưu trong S3 và phân phối qua CloudFront.
- **Máy chủ ứng dụng:** Spring Boot chạy trên một EC2 trong mạng con công khai, tiếp nhận các yêu cầu tại đường dẫn `/api/*` qua CloudFront và xử lý nghiệp vụ.
- **Cơ sở dữ liệu:** RDS MySQL được đặt trong mạng con riêng tư, chỉ cho phép máy chủ ứng dụng kết nối. Cấu hình cơ sở dữ liệu sử dụng một vùng sẵn sàng để phù hợp phạm vi thực hành.
- **Hình ảnh sản phẩm:** Hình ảnh được lưu trên S3 và phân phối qua CloudFront; máy chủ ứng dụng được cấp quyền phù hợp để quản lý tệp.
- **Xác thực và giám sát:** Cognito cấp mã xác thực cho người dùng; Spring Boot kiểm tra mã khi xử lý các yêu cầu được bảo vệ. CloudWatch theo dõi chỉ số EC2 và nhật ký ứng dụng.

### 4. Triển khai kỹ thuật

**Các giai đoạn triển khai**

1. Phân tích yêu cầu của ứng dụng bán điện thoại; xác định chức năng khách hàng, quản trị viên và luồng dữ liệu.
2. Thiết kế giao diện ReactJS, mô hình dữ liệu MySQL và các giao diện lập trình Spring Boot.
3. Cấu hình môi trường AWS gồm IAM, VPC, nhóm bảo mật, EC2, RDS, S3, CloudFront, Cognito và CloudWatch.
4. Triển khai giao diện lên S3 và CloudFront; triển khai Spring Boot lên EC2; kết nối RDS và tích hợp Cognito.
5. Kiểm thử truy cập trang web, xác thực, quản lý sản phẩm, đặt hàng, kết nối cơ sở dữ liệu và kiểm tra nhật ký hệ thống.

**Phân chia công việc**

| Thành viên | Nội dung phụ trách |
|---|---|
| Mai Văn Tường | Thiết kế giao diện, phát triển ReactJS cho khách hàng và quản trị viên, tích hợp giao diện lập trình, phân tích và thiết kế hệ thống. |
| Hoàng Thanh Tài | Cấu hình IAM, VPC, EC2, RDS, S3, CloudFront; triển khai hệ thống và giám sát bằng CloudWatch. |
| Kiều Minh Quang | Thiết kế sơ đồ dữ liệu, quản lý MySQL, chuẩn bị dữ liệu và kiểm thử giao diện lập trình bằng Postman. |
| Đỗ Mạnh Long | Phát triển Spring Boot, JPA, Repository, Service, Controller và các giao diện lập trình. |
| Dương Quang Huy | Triển khai Amazon Cognito, đăng ký, đăng nhập, xác thực JWT và phân quyền người dùng. |

### 5. Lộ trình và mốc triển khai

- **Mốc 1 – Phân tích và thiết kế:** Hoàn thành yêu cầu chức năng, thiết kế giao diện, sơ đồ cơ sở dữ liệu và kiến trúc AWS.
- **Mốc 2 – Phát triển ứng dụng:** Hoàn thành các giao diện ReactJS, chức năng Spring Boot, cơ sở dữ liệu MySQL và tích hợp xác thực.
- **Mốc 3 – Chuẩn bị hạ tầng AWS:** Cấu hình mạng, phân quyền, máy chủ, cơ sở dữ liệu và lưu trữ.
- **Mốc 4 – Triển khai và tích hợp:** Đưa giao diện, máy chủ ứng dụng và cơ sở dữ liệu lên AWS; kết nối các thành phần.
- **Mốc 5 – Kiểm thử và hoàn thiện:** Kiểm thử chức năng, xác thực, kết nối dữ liệu; kiểm tra nhật ký, chỉ số và hoàn thiện tài liệu workshop.

### 6. Ước tính ngân sách

**Chi phí hạ tầng AWS dự kiến trong 1 tháng**

| Dịch vụ / Tài nguyên | Chi phí (USD/tháng) |
|---|---:|
| Amazon EC2 và ổ đĩa EBS | 21,19 |
| Amazon RDS MySQL và dung lượng lưu trữ | 21,01 |
| Amazon S3 | 0,18 |
| Amazon CloudFront | 0,00 |
| Amazon Cognito | 0,00 |
| Amazon CloudWatch | 0,00 |
| Địa chỉ IPv4 công khai | 3,65 |
| AWS IAM và Amazon VPC | 0,00 |
| **Tổng chi phí dự kiến** | **46,03 USD/tháng** |

### 7. Đánh giá rủi ro

**Ma trận rủi ro**

| Rủi ro | Mức độ ảnh hưởng | Khả năng xảy ra |
|---|---|---|
| Lỗi cấu hình mạng hoặc quyền truy cập khiến các thành phần không kết nối được | Cao | Trung bình |
| Máy chủ hoặc cơ sở dữ liệu gián đoạn trong khi kiểm thử | Trung bình | Trung bình |
| Sai sót trong xác thực và phân quyền người dùng | Cao | Trung bình |
| Chi phí thực tế vượt dự toán hoặc credit không còn đủ | Trung bình | Trung bình |

**Biện pháp giảm thiểu**

- Kiểm tra quy tắc mạng và quyền truy cập theo từng bước; chỉ cho phép EC2 kết nối vào RDS.
- Kiểm thử chức năng và kết nối sau mỗi giai đoạn triển khai; theo dõi nhật ký và chỉ số qua CloudWatch.
- Kiểm tra mã xác thực JWT và quyền truy cập tương ứng với khách hàng, quản trị viên.
- Theo dõi chi phí bằng bảng quản lý chi phí AWS và xóa tài nguyên không còn sử dụng.

**Phương án dự phòng**

- Khi một dịch vụ gặp lỗi, kiểm tra nhật ký, cấu hình và khởi động lại thành phần liên quan khi cần thiết.
- Duy trì mã nguồn và tệp sao lưu dữ liệu cần thiết để khôi phục môi trường thực hành.

### 8. Kết quả kỳ vọng

- Hoàn thành ứng dụng web bán điện thoại có giao diện cho khách hàng và quản trị viên.
- Triển khai thành công ReactJS trên S3 và CloudFront, Spring Boot trên EC2 và MySQL trên RDS.
- Tích hợp Cognito để đăng ký, đăng nhập, xác thực và phân quyền.
- Kiểm thử được luồng xem sản phẩm, quản lý sản phẩm, đặt hàng và lưu trữ dữ liệu.
- Theo dõi được trạng thái máy chủ và nhật ký thông qua CloudWatch.
- Hoàn thiện hướng dẫn workshop có sơ đồ kiến trúc, các bước triển khai, hình ảnh kiểm thử và quy trình dọn dẹp tài nguyên.