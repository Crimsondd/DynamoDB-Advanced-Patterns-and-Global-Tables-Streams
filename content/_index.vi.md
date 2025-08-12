---
title : "Workshop DynamoDB Advanced Patterns"
date : "2025-08-11"
weight : 1 
chapter : false
---

# DynamoDB Advanced Patterns Xây dựng Kiến trúc Đa Vùng với Global Tables và Streams

#### Tổng quan
Trong workshop toàn diện này, bạn sẽ xây dựng một **Nền tảng E-commerce Đa Vùng** sử dụng DynamoDB Advanced Patterns và AWS Free Tier. Bạn sẽ học **Single Table Design**, triển khai **Global Tables** cho replication đa vùng, và xây dựng **real-time stream processing** với Lambda. 
Cuối cùng, chúng ta sẽ triển khai các chiến lược **monitoring và optimization** toàn diện trong khi duy trì kiểm soát chi phí nghiêm ngặt trong giới hạn Free Tier.

#### Single Table Design
**Single Table Design** là một phương pháp modeling DynamoDB nơi bạn lưu trữ nhiều loại entity trong một bảng sử dụng composite keys. Pattern này tối ưu cho hiệu suất và chi phí bằng cách giảm số lượng requests và tận dụng kiến trúc partition-based của DynamoDB. Khi triển khai đúng cách, nó cung cấp hiệu suất truy vấn sub-millisecond trong khi giảm thiểu tiêu thụ capacity.

![DynamoDB Architecture](/images/1/0001.png?featherlight=false&width=90pc)

{{% notice note %}}
Như một best practice, hãy thiết kế access patterns của bạn trước khi tạo table structure. Single Table Design yêu cầu lập kế hoạch cẩn thận cho partition keys (PK) và sort keys (SK) để hỗ trợ tất cả query patterns của bạn một cách hiệu quả. Workshop này sử dụng một mô hình dữ liệu e-commerce đã được chứng minh hỗ trợ 6 access patterns được tối ưu trong khi ở trong giới hạn Free Tier.
{{% /notice %}}

#### Global Tables Đa Vùng
**Global Tables** cung cấp replication database đa vùng, multi-active được quản lý hoàn toàn. Dữ liệu được ghi vào bất kỳ vùng nào sẽ được tự động replicate đến tất cả các vùng khác trong vài giây. Điều này cho phép bạn xây dựng các ứng dụng phân tán toàn cầu với truy cập read và write local, cải thiện hiệu suất và cung cấp khả năng disaster recovery.

#### DynamoDB Streams & Lambda
**DynamoDB Streams** capture các events modification dữ liệu trong table của bạn trong thời gian gần thực. Khi kết hợp với **AWS Lambda**, bạn có thể xây dựng kiến trúc event-driven tự động xử lý thay đổi, update derived data, gửi notifications, hoặc trigger business workflows. Pattern này rất quan trọng để xây dựng các ứng dụng reactive, scalable.

#### Global Secondary Indexes (GSI)
**Global Secondary Indexes** cho phép bạn query dữ liệu của mình sử dụng các access patterns khác với main table. GSIs có partition và sort keys riêng, cho phép các truy vấn hiệu quả qua các dimensions khác nhau của dữ liệu bạn. Thiết kế GSI đúng cách là crucial cho optimization hiệu suất và kiểm soát chi phí.

#### Monitoring & Cost Optimization
**CloudWatch monitoring** cung cấp visibility thời gian thực vào hiệu suất DynamoDB, capacity utilization, và costs. Kết hợp với **billing alerts** và **Free Tier tracking**, bạn có thể đảm bảo hiệu suất tối ưu trong khi duy trì kiểm soát chi phí nghiêm ngặt. Workshop này triển khai comprehensive monitoring dashboards và automated alerting.

#### Tuân thủ Free Tier
**AWS Free Tier** cung cấp giới hạn hào phóng cho học tập và thử nghiệm. Workshop này được thiết kế để chỉ sử dụng 60% tài nguyên Free Tier có sẵn, đảm bảo zero cost trong khi cung cấp trải nghiệm học tập enterprise-grade. Tất cả participants sẽ triển khai các patterns production-ready mà không phát sinh bất kỳ khoản phí nào.

#### Nội dung Chính

1. [Thiết lập & Triển khai Hạ tầng](1-setup-infrastructure/)
2. [Triển khai Single Table Design](2-single-table-design/)
3. [Thiết lập Global Tables Đa Vùng](3-global-tables-setup/)
4. [DynamoDB Streams & Lambda Processing](4-streams-lambda-processing/)
5. [Monitoring & Performance Optimization](5-monitoring-optimization/)
6. [Advanced Patterns & Best Practices](6-advanced-patterns/)
7. [Dọn dẹp & Quản lý Tài nguyên](7-cleanup-resources/)
