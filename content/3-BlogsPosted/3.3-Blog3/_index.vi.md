---
title: "Blog 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---

# BLOG 3: TÍCH HỢP QUY TRÌNH MUA SẮM VỚI AWS MARKETPLACE THÔNG QUA CÁC API

### Xây dựng hệ thống mua sắm phần mềm tự động bằng AWS Marketplace APIs và kiến trúc Serverless

Chào mọi người, hôm nay mình muốn chia sẻ một giải pháp khá thú vị của AWS về việc tích hợp quy trình mua sắm phần mềm vào các hệ thống doanh nghiệp. Thông qua AWS Marketplace APIs, doanh nghiệp có thể tìm kiếm sản phẩm, quản lý hợp đồng, đăng ký dịch vụ và theo dõi chi phí ngay trên nền tảng nội bộ mà không cần thường xuyên truy cập AWS Management Console.

#### 3.3.1 Bài toán gặp phải

Trong các doanh nghiệp lớn, quá trình tìm kiếm, lựa chọn và quản lý phần mềm thường liên quan đến nhiều phòng ban như IT, tài chính và bộ phận mua sắm.

Nếu mọi thao tác đều phải thực hiện thủ công trên AWS Management Console, người dùng sẽ mất nhiều thời gian để tìm kiếm sản phẩm, so sánh giá, quản lý hợp đồng và tổng hợp báo cáo chi phí.

Vì vậy, doanh nghiệp cần một giải pháp giúp tích hợp các hoạt động mua sắm này vào hệ thống làm việc hiện có.

#### 3.3.2 Giải pháp của AWS

![Kiến trúc hệ thống MP-Buyer Portal trên AWS](/images/blog3/mp-buyer-portal-architecture.png)

*Hình 3.3: Kiến trúc Serverless của hệ thống MP-Buyer Portal tích hợp AWS Marketplace APIs.*

AWS giới thiệu mô hình MP-Buyer Portal, một ứng dụng tham chiếu Serverless giúp tích hợp các chức năng của AWS Marketplace vào một giao diện web riêng.

Giải pháp sử dụng các thành phần chính:

- **AWS Marketplace Discovery API:** Hỗ trợ tìm kiếm sản phẩm, lọc và so sánh các lựa chọn phần mềm.
- **AWS Marketplace Agreement API:** Quản lý hợp đồng, thông tin giá và quy trình đăng ký sản phẩm.
- **Amazon CloudFront và Amazon S3:** Phân phối và lưu trữ giao diện web.
- **Amazon API Gateway và AWS Lambda:** Tiếp nhận yêu cầu và xử lý các nghiệp vụ mua sắm.
- **Amazon DynamoDB:** Lưu trữ và đồng bộ dữ liệu hợp đồng để hỗ trợ truy xuất báo cáo nhanh hơn.
- **Amazon Cognito:** Xác thực người dùng và bảo vệ quyền truy cập hệ thống.
- **Amazon EventBridge:** Tự động kích hoạt quá trình đồng bộ dữ liệu định kỳ 6 giờ một lần.

Ngoài ra, hệ thống có thể kết hợp Strands Agents và Amazon Bedrock để phân tích dữ liệu, tạo báo cáo và đưa ra các đề xuất tối ưu chi phí.

#### 3.3.3 Lợi ích và bảo mật

Điểm nổi bật của giải pháp là khả năng tích hợp quy trình mua sắm vào hệ thống nội bộ, giúp người dùng tìm kiếm sản phẩm, quản lý đăng ký và theo dõi chi phí thuận tiện hơn.

Bên cạnh đó, kiến trúc Serverless giúp giảm công việc quản lý hạ tầng và tối ưu chi phí khi hệ thống không hoạt động.

Về bảo mật, giải pháp sử dụng Amazon Cognito để xác thực người dùng, HTTPS thông qua CloudFront, S3 Private Bucket và IAM Least Privilege để giới hạn quyền truy cập. AWS cũng khuyến nghị kết hợp AWS WAF, giới hạn CORS và thiết lập cảnh báo CloudWatch khi triển khai thực tế.

#### 3.3.4 Đánh giá cá nhân & Kết luận

Theo mình, đây là một giải pháp hữu ích cho các doanh nghiệp muốn tự động hóa quy trình mua sắm và quản lý phần mềm. Việc sử dụng AWS Marketplace APIs giúp giảm các thao tác thủ công, đồng thời cho phép tích hợp chức năng mua sắm vào những hệ thống mà doanh nghiệp đang sử dụng.

Qua bài viết, mình hiểu thêm về cách kết hợp Amazon CloudFront, API Gateway, Lambda, DynamoDB và Cognito để xây dựng một ứng dụng Serverless hoàn chỉnh. Đây cũng là cơ hội để tìm hiểu sâu hơn về việc tích hợp API, quản lý dữ liệu và bảo mật trong kiến trúc điện toán đám mây.

*Tác giả: Kiều Minh Quang*

**Nguồn tham khảo:** [AWS – How you can embed procurement into your workflows with AWS Marketplace APIs](https://aws.amazon.com/vi/blogs/awsmarketplace/how-you-can-embed-procurement-into-your-workflows-with-aws-marketplace-apis/)