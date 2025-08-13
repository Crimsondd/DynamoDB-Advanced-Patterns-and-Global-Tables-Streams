---
title : "4.1 Stream Configuration"
date : "2025-08-11"
weight : 41
chapter : false
pre : " <b> 4.1 </b> "
---

## Bật DynamoDB Streams

🔧 **Bật theo dõi thay đổi cho bảng DynamoDB của bạn**

### Tổng quan

DynamoDB Streams ghi lại mọi thay đổi đối với các mục trong bảng của bạn. Điều này cho phép xử lý thời gian thực với các hàm Lambda.

### Các loại Stream View

Chọn dữ liệu cần ghi lại:
- **NEW_AND_OLD_IMAGES**: Dữ liệu đầy đủ trước và sau (khuyến nghị cho demo)
- **NEW_IMAGE**: Chỉ dữ liệu sau khi cập nhật
- **OLD_IMAGE**: Chỉ dữ liệu trước khi cập nhật
- **KEYS_ONLY**: Chỉ các khóa

## Hướng dẫn từng bước: Bật Streams

### Bước 1: Mở DynamoDB Console

1. AWS Console → Tìm kiếm "DynamoDB"
2. Bảng → Nhấp vào bảng của bạn `demo-ecommerce-freetier`
3. Nhấp vào tab **"Exports and streams"**

### Bước 2: Bật Stream

1. Tìm phần "DynamoDB stream"
2. Nhấp vào nút **"Turn on"**
3. Chọn **"New and old images"**
4. Nhấp vào **"Turn on stream"**

### Bước 3: Ghi lại Stream ARN

Sao chép Stream ARN - bạn sẽ cần nó để thiết lập Lambda:
```
arn:aws:dynamodb:us-east-1:123456789012:table/demo-ecommerce-freetier/stream/2025-08-13T10:00:00.000
```

![4.1.1](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/4.1.1.png?featherlight=false&width=90pc)

### Xác minh

✅ Trạng thái stream hiển thị "Enabled"  
✅ Stream ARN được hiển thị  
✅ Loại view là "New and old images"

![4.1.2](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/4.1.2.png?featherlight=false&width=90pc)

## Bài tập 2: Hiểu cài đặt Stream

### So sánh các loại Stream View

**Chọn loại view phù hợp với trường hợp sử dụng của bạn**:

| Loại View | Trường hợp sử dụng | Dữ liệu được ghi lại |
|-----------|--------------------|-----------------------|
| **KEYS_ONLY** | Ghi nhật ký kiểm tra | Chỉ PK, SK |
| **NEW_IMAGE** | Cập nhật bộ nhớ đệm | Mục sau thay đổi |
| **OLD_IMAGE** | Theo dõi thay đổi | Mục trước thay đổi |
| **NEW_AND_OLD_IMAGES** | Ghi nhật ký đầy đủ | Cả hai phiên bản |

### Cân nhắc về hiệu suất

**Ảnh hưởng của cấu hình Stream**:

- **Lưu trữ**: NEW_AND_OLD_IMAGES sử dụng nhiều không gian nhất
- **Payload Lambda**: Payload lớn hơn với hình ảnh đầy đủ
- **Thời gian xử lý**: Nhiều dữ liệu hơn = thời gian xử lý lâu hơn
- **Chi phí**: Chi phí bổ sung tối thiểu cho streams

{{% notice warning %}}
**Lưu ý Free Tier**: DynamoDB Streams được bao gồm mà không tính thêm phí. Xử lý Lambda nằm trong giới hạn Free Tier.
{{% /notice %}}

## Bài tập 3: Kiểm tra chức năng Stream

### Bước 1: Tạo mục kiểm tra

**Tạo một sự kiện stream**:

1. **Tab Items**: Quay lại tab "Items"
2. **Tạo mục**: Nhấp vào "Create item"
3. **Thêm dữ liệu kiểm tra**:

```json
{
  "PK": "STREAM#test-item",
  "SK": "EVENT#001",
  "event_type": "stream_test",
  "description": "Testing DynamoDB Stream functionality",
  "created_at": "2025-08-11T16:00:00Z",
  "test_purpose": "verify_stream_capture"
}
```

4. **Tạo**: Nhấp vào "Create item"

![4.1.3](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/4.1.3.png?featherlight=false&width=90pc)

### Bước 2: Giám sát hoạt động Stream

**Kiểm tra các chỉ số stream**:

1. **CloudWatch**: Mở console CloudWatch trong tab mới
2. **Metrics**: Điều hướng đến Metrics
3. **DynamoDB**: Nhấp vào namespace "DynamoDB"
4. **Chỉ số stream**: Tìm các chỉ số liên quan đến stream
5. **IncomingRecords**: Nên hiển thị 1 bản ghi mới

![4.1.4](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/4.1.4.png?featherlight=false&width=90pc)

### Bước 3: Hiểu cấu trúc bản ghi Stream

**Cấu trúc bản ghi Stream** (tham khảo):

```json
{
  "Records": [
    {
      "eventID": "12345...",
      "eventName": "INSERT",
      "eventVersion": "1.1",
      "eventSource": "aws:dynamodb",
      "awsRegion": "us-east-1",
      "dynamodb": {
        "ApproximateCreationDateTime": 1642857600,
        "Keys": {
          "PK": {"S": "STREAM#test-item"},
          "SK": {"S": "EVENT#001"}
        },
        "NewImage": {
          "PK": {"S": "STREAM#test-item"},
          "SK": {"S": "EVENT#001"},
          "event_type": {"S": "stream_test"},
          "description": {"S": "Testing DynamoDB Stream functionality"},
          "created_at": {"S": "2025-08-11T16:00:00Z"},
          "test_purpose": {"S": "verify_stream_capture"}
        },
        "SequenceNumber": "123456789",
        "SizeBytes": 245,
        "StreamViewType": "NEW_AND_OLD_IMAGES"
      }
    }
  ]
}
```

**Các thành phần chính**:
- **eventName**: INSERT (vì đây là mục mới)
- **Keys**: Khóa chính của mục đã thay đổi
- **NewImage**: Dữ liệu mục đầy đủ sau khi tạo
- **OldImage**: Sẽ trống đối với các sự kiện INSERT

## Bài tập 4: Thực hành cấu hình Stream tốt nhất

### Cấu hình tối ưu cho Workshop

**Cài đặt khuyến nghị**:

- ✅ **Loại View**: NEW_AND_OLD_IMAGES (dấu vết kiểm tra toàn diện)
- ✅ **Thời gian lưu trữ**: 24 giờ (mặc định, đủ cho xử lý)
- ✅ **Shards**: Được AWS quản lý tự động
- ✅ **Xử lý**: Lambda với kích thước batch phù hợp

### Cân nhắc về bảo mật

**Kiểm soát truy cập**:

1. **Quyền IAM**: Lambda cần quyền đọc stream
2. **Mã hóa**: Streams kế thừa cài đặt mã hóa của bảng
3. **VPC**: Streams hoạt động trong cấu hình VPC của bạn
4. **Giám sát**: CloudTrail ghi lại truy cập stream

### Tối ưu hóa chi phí

**Các yếu tố chi phí Stream**:

- **Yêu cầu đọc**: Không tính thêm phí cho ghi stream
- **Lời gọi Lambda**: Tính vào Free Tier
- **Truyền dữ liệu**: Tối thiểu cho xử lý trong cùng khu vực
- **Lưu trữ**: Bản ghi stream được lưu giữ trong 24 giờ

## Bài tập 5: Cấu hình Stream nâng cao

### Mô hình nhiều người tiêu dùng

**Khi bạn cần nhiều bộ xử lý**:

1. **Stream đơn**: Một stream DynamoDB cho mỗi bảng
2. **Nhiều Lambda**: Mỗi Lambda có thể xử lý cùng một stream
3. **Kinesis Data Streams**: Cho định tuyến phức tạp hơn
4. **Lọc sự kiện**: Lọc ở mức Lambda

### Cân nhắc đa khu vực

**Global Tables + Streams**:

- **Mỗi khu vực**: Có stream riêng
- **Sự kiện sao chép**: Tạo bản ghi stream
- **Lọc**: Phân biệt ghi ứng dụng với sao chép
- **Xử lý**: Xử lý sự khác biệt khu vực

## Tóm tắt cấu hình

Bằng cách hoàn thành bài tập này, bạn đã:

- ✅ **Bật DynamoDB Streams** trên bảng của bạn
- ✅ **Cấu hình loại view** để ghi lại thay đổi toàn diện  
- ✅ **Kiểm tra chức năng stream** với dữ liệu mẫu
- ✅ **Hiểu cấu trúc bản ghi stream** và các thành phần
- ✅ **Áp dụng thực hành tốt nhất** để cấu hình tối ưu

{{% notice success %}}
**Stream Sẵn Sàng**: Bảng DynamoDB của bạn bây giờ ghi lại mọi thay đổi và sẵn sàng cho xử lý Lambda!
{{% /notice %}}

## Bước tiếp theo

Với streams đã được cấu hình, bạn đã sẵn sàng tạo các hàm Lambda sẽ xử lý các sự kiện này trong thời gian thực. Trong phần tiếp theo, chúng ta sẽ xây dựng và triển khai một hàm Lambda được tối ưu hóa cho xử lý stream DynamoDB.
