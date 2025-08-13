---
title : "4. Streams & Lambda Processing"
date : "2025-08-11"
weight : 4
chapter : true
pre : " <b> 4. </b> "
---

# DynamoDB Streams & Lambda Processing

⚡ **Thiết lập xử lý sự kiện thời gian thực cho bảng DynamoDB của bạn**

## Tổng quan Module

Tìm hiểu cách ghi lại và xử lý các thay đổi dữ liệu trong thời gian thực bằng cách sử dụng DynamoDB Streams và AWS Lambda.

#### Bạn sẽ học được gì

- **Bật Streams**: Bật theo dõi thay đổi cho bảng của bạn
- **Tạo Lambda**: Xây dựng một hàm để xử lý sự kiện  
- **Kiểm tra xử lý**: Xem các sự kiện được kích hoạt trong thời gian thực

#### Kiến trúc đơn giản

![Simple_Architecture](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/Simple_Architecture.png?featherlight=false&width=50pc)

#### Lợi ích chính

- **Thời gian thực**: Xử lý thay đổi ngay lập tức
- **Tự động**: Không cần polling
- **Mở rộng**: Lambda xử lý đồng thời
- **Hiệu quả chi phí**: Chỉ trả tiền cho thời gian sử dụng

## Nội dung Module

1. **[Cấu hình Stream](4.1-stream-configuration/)** - Bật streams trên bảng của bạn
2. **[Thiết lập hàm Lambda](4.2-lambda-function-setup/)** - Tạo và kết nối Lambda

{{% notice info %}}
**Free Tier**: Lambda cung cấp 1 triệu yêu cầu miễn phí mỗi tháng. Demo này sử dụng tài nguyên tối thiểu.
{{% /notice %}}

{{% children %}}

#### Hãy thêm xử lý thời gian thực vào bảng DynamoDB của bạn.

**Xử lý thời gian thực**
- Xử lý thay đổi dữ liệu trong vòng 100-500 mili giây
- Không cần polling - các sự kiện được đẩy tự động
- Mở rộng đến hàng triệu sự kiện mỗi giây

**Kiến trúc hướng sự kiện**  
- Tách biệt lưu trữ dữ liệu khỏi logic nghiệp vụ
- Kích hoạt nhiều hệ thống hạ nguồn
- Xây dựng các ứng dụng phản ứng nhanh, đáp ứng

**Hiệu quả chi phí**
- Chỉ trả tiền cho thời gian xử lý thực tế
- AWS Free Tier bao gồm 1 triệu lần gọi Lambda
- Không cần quản lý cơ sở hạ tầng

#### Các trường hợp sử dụng phổ biến

| Mẫu | Kích hoạt | Hành động |
|---------|---------|--------|
| **Audit Trail** | Bất kỳ thay đổi nào | Ghi nhật ký vào S3/CloudWatch |
| **Cache Invalidation** | Cập nhật mục | Xóa Redis/ElastiCache |
| **Notifications** | Đơn hàng được tạo | Gửi email/SMS |
| **Analytics** | Hoạt động người dùng | Cập nhật bảng điều khiển số liệu |
| **Search Index** | Thay đổi sản phẩm | Cập nhật Elasticsearch |
| **Workflow** | Thay đổi trạng thái | Kích hoạt Step Functions |

#### Mẫu xử lý Stream

**Mẫu Fan-out**: Một thay đổi kích hoạt nhiều hàm Lambda
```
DynamoDB Change → Stream → Lambda 1 (Email)
                       → Lambda 2 (Analytics)  
                       → Lambda 3 (Cache Update)
```

**Mẫu Pipeline**: Xử lý tuần tự qua nhiều giai đoạn
```
Order Created → Validate → Process Payment → Update Inventory → Ship
```

**Mẫu Aggregation**: Kết hợp nhiều thay đổi thành các bản tóm tắt
```
Sales Records → Real-time Revenue Dashboard
User Actions → Activity Analytics
```

#### Đặc điểm hiệu suất

- **Độ trễ**: Thường từ 100-500ms từ thay đổi đến xử lý
- **Thông lượng**: Tự động mở rộng với khối lượng dữ liệu của bạn  
- **Độ tin cậy**: Tự động thử lại và xử lý lỗi
- **Thứ tự**: Các thay đổi được xử lý theo thứ tự cho mỗi mục
- **Lưu giữ**: Bản ghi stream có sẵn trong 24 giờ

#### Cấu trúc Module

Module này được tổ chức thành các phần thực hành xây dựng dựa trên nhau:

1. **Cấu hình Stream** - Bật và cấu hình DynamoDB Streams
2. **Thiết lập hàm Lambda** - Tạo và triển khai các hàm xử lý stream  
3. **Thực hành xử lý sự kiện** - Kiểm tra với các thay đổi dữ liệu thực tế
4. **Giám sát & Gỡ lỗi** - Theo dõi hiệu suất và khắc phục sự cố

Mỗi phần bao gồm:
- ✅ **Hướng dẫn từng bước trên AWS Console**
- ✅ **Ví dụ mã và mẫu**
- ✅ **Chỗ dành sẵn cho ảnh chụp màn hình trong tài liệu**
- ✅ **Hướng dẫn khắc phục sự cố**
- ✅ **Kịch bản thực tế**

#### Điều kiện tiên quyết

Trước khi bắt đầu module này, hãy đảm bảo bạn đã:
- ✅ Hoàn thành Module 1 (thiết lập bảng DynamoDB)
- ✅ Hiểu cơ bản về AWS Lambda
- ✅ Quen thuộc với JSON và các khái niệm hướng sự kiện
- ✅ Truy cập AWS Console với quyền phù hợp

{{% notice info %}}
**Tối ưu hóa Free Tier**: Tất cả các bài tập được thiết kế để nằm trong giới hạn Free Tier của AWS, bao gồm các lần gọi Lambda và DynamoDB streams.
{{% /notice %}}

### Mục tiêu học tập

Sau khi hoàn thành module này, bạn sẽ:

- **Hiểu** kiến trúc DynamoDB Streams và luồng sự kiện
- **Cấu hình** các hàm Lambda để xử lý sự kiện stream
- **Triển khai** các mẫu hướng sự kiện phổ biến
- **Giám sát** hiệu suất xử lý stream
- **Gỡ lỗi** các vấn đề xử lý stream
- **Thiết kế** các ứng dụng hướng sự kiện có khả năng mở rộng

{{% notice success %}}
**Sẵn sàng xây dựng**: Biến cơ sở dữ liệu tĩnh của bạn thành hệ thống hướng sự kiện, phản ứng tự động với mọi thay đổi!
{{% /notice %}}

{{% children %}}

- Cấu hình DynamoDB Streams
- Các hàm xử lý stream Lambda
- Pipeline xử lý đơn hàng thời gian thực
- Xử lý lỗi và hàng đợi thư chết

Hãy triển khai xử lý stream thời gian thực cho nền tảng thương mại điện tử của bạn.
