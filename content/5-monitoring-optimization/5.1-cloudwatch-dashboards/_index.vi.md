---
title : "5.1 Giám sát cơ bản với CloudWatch"
date : "2025-08-11"
weight : 51
chapter : false
pre : " <b> 5.1 </b> "
---

# Giám sát cơ bản với CloudWatch

📊 **Xem các chỉ số cơ bản của DynamoDB trong CloudWatch**

## Tổng quan

CloudWatch tự động thu thập các chỉ số cơ bản từ DynamoDB. Chúng ta sẽ khám phá cách xem các chỉ số này mà không cần tạo bảng điều khiển phức tạp.

## Xem các chỉ số DynamoDB

### Bước 1: Truy cập chỉ số CloudWatch

1. **AWS Console**: Tìm kiếm "CloudWatch"
2. **Metrics**: Nhấp vào "Metrics" trong thanh bên trái
3. **AWS/DynamoDB**: Nhấp vào namespace này
4. **Table Metrics**: Duyệt qua các chỉ số có sẵn

![5.1.1](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/5/5.1.1.png?featherlight=false&width=90pc)

### Bước 2: Kiểm tra các chỉ số chính

**Giám sát sử dụng cơ bản**:

1. **ConsumedReadCapacityUnits**: Kiểm tra mức sử dụng đọc
2. **ConsumedWriteCapacityUnits**: Kiểm tra mức sử dụng ghi
3. **ItemCount**: Số lượng mục trong bảng
4. **TableSizeBytes**: Dung lượng lưu trữ

![5.1.2](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/5/5.1.2.png?featherlight=false&width=90pc)

### Bước 3: Xem chi tiết chỉ số

**Nhấp vào bất kỳ chỉ số nào để xem chi tiết**:

1. **Chọn bảng của bạn**: `demo-ecommerce-freetier`
2. **Xem biểu đồ**: Hiển thị mức sử dụng theo thời gian
3. **Kiểm tra giá trị**: Đảm bảo nằm trong giới hạn Free Tier

![5.1.3](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/5/5.1.3.png?featherlight=false&width=90pc)

## Các chỉ số chính cần kiểm tra

| Chỉ số | Mục đích | Giới hạn Free Tier |
|--------|----------|--------------------|
| **ConsumedReadCapacityUnits** | Sử dụng đọc | 25 RCU/tháng |
| **ConsumedWriteCapacityUnits** | Sử dụng ghi | 25 WCU/tháng |
| **ItemCount** | Số lượng mục | Không giới hạn |
| **TableSizeBytes** | Dung lượng lưu trữ | 25 GB |

{{% notice info %}}
**Free Tier**: Tất cả các chỉ số cơ bản của CloudWatch cho DynamoDB đều miễn phí. Không cần tạo bảng điều khiển.
{{% /notice %}}

## Mẹo giám sát đơn giản

- **Kiểm tra chỉ số hàng ngày**: Đảm bảo nằm trong giới hạn Free Tier
- **Tìm kiếm các đột biến**: Các mẫu sử dụng bất thường
- **Giám sát lưu trữ**: Theo dõi sự tăng trưởng kích thước bảng
- **Không cần cảnh báo**: Kiểm tra trực quan là đủ cho workshop

{{% notice success %}}
**Giám sát cơ bản**: Bây giờ bạn có thể xem các chỉ số DynamoDB quan trọng trong CloudWatch!
{{% /notice %}}

## Bước tiếp theo

Xem chỉ số cơ bản là đủ cho workshop này. Chuyển sang module tiếp theo khi sẵn sàng.
