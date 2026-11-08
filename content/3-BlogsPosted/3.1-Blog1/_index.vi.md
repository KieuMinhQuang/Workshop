---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---


#  TĂNG TỐC XỬ LÝ CẢNH BÁO BẢO MẬT VỚI ARCTIC WOLF AURORA AGENTIC SOC

Chào mọi người, hôm nay mình muốn chia sẻ một giải pháp khá thú vị của AWS và Arctic Wolf về việc ứng dụng AI Agent trong lĩnh vực an ninh mạng. Giải pháp Aurora Agentic SOC giúp doanh nghiệp xử lý các cảnh báo bảo mật nhanh hơn bằng cách kết hợp trí tuệ nhân tạo với đội ngũ chuyên gia bảo mật.

3.1.1 Bài toán gặp phải

Trong quá trình vận hành hệ thống, các doanh nghiệp thường nhận được rất nhiều cảnh báo bảo mật từ các công cụ như Amazon GuardDuty và AWS Security Hub. Tuy nhiên, đội ngũ bảo mật không phải lúc nào cũng có đủ thời gian và nhân lực để kiểm tra, phân tích từng cảnh báo.

Điều này khiến việc phát hiện và xử lý các mối đe dọa có thể bị chậm trễ, làm tăng nguy cơ mất an toàn hệ thống.

3.1.2 Giải pháp của Arctic Wolf

![Kiến trúc hệ thống Arctic Wolf Aurora Agentic SOC](/Workshop/images/Blog1/arctic-wolf-architecture.png)

*Hình 3.1: Kiến trúc hệ thống Arctic Wolf Aurora Agentic SOC trên AWS.*

Arctic Wolf phát triển Aurora Agentic SOC, sử dụng AI Agent kết hợp với chuyên gia bảo mật để tự động hóa quá trình xử lý cảnh báo.

Hệ thống hoạt động thông qua một quy trình:

* Thu thập cảnh báo: AWS Security Hub tổng hợp các phát hiện từ nhiều dịch vụ bảo mật.

* Truyền dữ liệu: Amazon EventBridge phát hiện sự kiện mới, Amazon Data Firehose chuyển tiếp dữ liệu và AWS Lambda xử lý, bổ sung thông tin.

* Phân tích: AI Agent trên nền tảng Aurora hỗ trợ phân loại cảnh báo, điều tra và xác định mức độ nguy hiểm.

* Xử lý: Chuyên gia bảo mật kiểm tra các trường hợp phức tạp và phối hợp đưa ra phương án xử lý phù hợp.

3.1.3 Vì sao cách làm này hiệu quả?

Điểm nổi bật của giải pháp là sự kết hợp giữa AI và con người. AI Agent có thể xử lý các công việc lặp lại, phân tích lượng lớn cảnh báo và hỗ trợ điều tra liên tục. Trong khi đó, chuyên gia bảo mật tập trung vào những tình huống phức tạp cần đánh giá chuyên sâu.

Ngoài ra, kiến trúc hướng sự kiện (Event-driven Architecture) giúp các cảnh báo được chuyển đến hệ thống xử lý ngay khi phát sinh, thay vì phải liên tục kiểm tra dữ liệu theo chu kỳ.

* 3.1.4 Kết quả đạt được

Theo bài viết AWS, khi CloudHesive triển khai Arctic Wolf, kết quả ghi nhận bao gồm:

* Giảm 50% số lượng cảnh báo cần phân tích ban đầu.

* Tốc độ điều tra và khắc phục nhanh hơn 99%.

* Tiết kiệm chi phí gấp 10 lần so với ước tính tự xây dựng một trung tâm vận hành bảo mật (SOC) nội bộ.

Đây là những kết quả cho thấy tiềm năng của việc kết hợp AI Agent với đội ngũ chuyên gia trong hoạt động an ninh mạng.

3.1.5 Cảm nhận cá nhân & Kết luận

Theo mình, bài viết mang đến một góc nhìn thực tế về việc ứng dụng AI vào lĩnh vực bảo mật. AI không chỉ hỗ trợ trả lời câu hỏi mà còn có thể tham gia vào quá trình phân tích, điều tra và phối hợp xử lý sự cố.

Qua bài viết, mình hiểu thêm về vai trò của các dịch vụ AWS như Security Hub, EventBridge, Lambda và S3 trong việc xây dựng hệ thống giám sát bảo mật tự động. Đây cũng là kiến thức hữu ích giúp mình hiểu rõ hơn về kiến trúc điện toán đám mây và xu hướng ứng dụng AI trong doanh nghiệp.

Tác giả: Kiều Minh Quang

**Nguồn tham khảo:** [AWS – Resolve security signals faster with Arctic Wolf’s Aurora Agentic SOC](https://aws.amazon.com/vi/blogs/awsmarketplace/resolve-security-signals-faster-with-arctic-wolfs-aurora-agentic-soc/)