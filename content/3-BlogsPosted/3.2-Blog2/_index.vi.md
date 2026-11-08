---
title: "Blog 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---


# BLOG 2: XÂY DỰNG HỆ THỐNG ĐO LƯỜNG SỬ DỤNG SAAS TRÊN AWS MARKETPLACE VỚI METERING AGENT SKILL

### Ứng dụng AI Agent và AWS Serverless để tự động hóa quá trình ghi nhận mức sử dụng dịch vụ SaaS

Chào mọi người, hôm nay mình muốn chia sẻ một giải pháp khá thú vị của AWS về việc ứng dụng AI Agent trong quá trình phát triển hệ thống SaaS. AWS Marketplace Metering Agent Skill giúp nhà phát triển xây dựng, triển khai và kiểm tra hệ thống đo lường mức sử dụng dịch vụ, từ đó hỗ trợ tính phí khách hàng chính xác hơn.

#### 3.2.1 Bài toán gặp phải

Đối với các sản phẩm SaaS trên AWS Marketplace, doanh nghiệp cần ghi nhận lượng tài nguyên hoặc dịch vụ mà khách hàng đã sử dụng để tính phí theo mức tiêu thụ.

Tuy nhiên, việc tự xây dựng hệ thống đo lường (Metering) đòi hỏi phải xử lý nhiều vấn đề như đăng ký khách hàng, thu thập dữ liệu sử dụng, tổng hợp và gửi thông tin thanh toán. Điều này khiến quá trình phát triển trở nên phức tạp và tốn nhiều thời gian.

#### 3.2.2 Giải pháp của AWS

![Kiến trúc hệ thống đo lường SaaS trên AWS Marketplace](/Workshop/images/blog2/saas-metering-architecture.png)

*Hình 3.2: Kiến trúc hệ thống đo lường mức sử dụng SaaS trên AWS Marketplace.*

AWS giới thiệu Metering Agent Skill, một công cụ hỗ trợ AI Agent xây dựng hệ thống đo lường SaaS ngay trong môi trường lập trình có tích hợp AI.

Giải pháp hoạt động thông qua một số thành phần chính:

- **AI Agent và AWS MCP Server:** Hỗ trợ tìm kiếm hướng dẫn, phân tích yêu cầu và tạo mã nguồn tích hợp.
- **AWS SAM và CloudFormation:** Tự động tạo và triển khai các tài nguyên cần thiết trên AWS.
- **Amazon API Gateway và AWS Lambda:** Tiếp nhận thông tin đăng ký khách hàng và xử lý dữ liệu.
- **Amazon EventBridge và Amazon SQS:** Điều phối sự kiện, lập lịch và truyền tải dữ liệu sử dụng.
- **Amazon DynamoDB:** Lưu trữ thông tin khách hàng, giấy phép và dữ liệu sử dụng.
- **BatchMeterUsage API:** Gửi dữ liệu sử dụng theo từng đợt lên AWS Marketplace để phục vụ tính phí.

#### 3.2.3 Vì sao cách làm này hiệu quả?

Điểm nổi bật của giải pháp là sử dụng AI Agent để hỗ trợ tự động hóa nhiều công đoạn phát triển hệ thống. Thay vì phải tự viết toàn bộ mã nguồn và cấu hình từng dịch vụ AWS, nhà phát triển có thể mô tả yêu cầu để Agent hỗ trợ tạo mã và mẫu triển khai.

Ngoài ra, hệ thống còn hỗ trợ chế độ kiểm thử Dry-run giúp xác minh quy trình mà không thực hiện gửi dữ liệu thanh toán thực tế. Điều này giúp giảm rủi ro trước khi đưa hệ thống vào vận hành.

#### 3.2.4 Một số lưu ý khi triển khai

Khi sử dụng Metering Agent Skill, nhà phát triển cần có tài khoản AWS Marketplace Seller và sản phẩm SaaS đã cấu hình mô hình tính phí theo mức sử dụng.

Bên cạnh đó, mã nguồn do AI tạo ra cần được kiểm tra về bảo mật và tuân thủ trước khi triển khai chính thức. Doanh nghiệp cũng cần tự xây dựng phần ghi nhận dữ liệu sử dụng từ ứng dụng SaaS vào DynamoDB, vì thành phần này không được Agent tự động triển khai.

#### 3.2.5 Đánh giá cá nhân & Kết luận

Theo mình, đây là một giải pháp hữu ích cho các doanh nghiệp đang phát triển sản phẩm SaaS trên AWS Marketplace. Việc kết hợp AI Agent với các dịch vụ Serverless giúp đơn giản hóa quá trình xây dựng hệ thống đo lường và giảm bớt công việc cấu hình thủ công.

Qua bài viết, mình hiểu thêm về cách kết hợp Amazon EventBridge, AWS Lambda, Amazon SQS và DynamoDB để xây dựng một hệ thống xử lý dữ liệu theo kiến trúc hướng sự kiện. Đồng thời, mình cũng thấy được vai trò của AI Agent trong việc hỗ trợ lập trình và triển khai các ứng dụng thực tế trên nền tảng điện toán đám mây.

*Tác giả: Kiều Minh Quang*

**Nguồn tham khảo:** [AWS – Xây dựng hệ thống đo lường SaaS trên AWS Marketplace với Metering Agent Skill](https://aws.amazon.com/vi/blogs/awsmarketplace/build-aws-marketplace-saas-metering-with-the-metering-agent-skill/)