---
title : "3.1 Tổng quan Global Tables"
date : "2025-08-11"
weight : 31
chapter : false
pre : " <b> 3.1 </b> "
---

# Tổng quan Global Tables

🌐 **Hiểu về kiến trúc DynamoDB Global Tables và cơ chế sao chép (replication)**

## Global Tables là gì?

Global Tables cho phép bạn tạo một cơ sở dữ liệu đa vùng (multi-region), đa chủ (multi-master) cung cấp hiệu suất đọc và ghi cục bộ cho các ứng dụng phân tán toàn cầu. Triển khai CloudFormation của bạn đã cấu hình sẵn điều này.

## Các thành phần Kiến trúc

### Cấu hình Workshop hiện tại

Hạ tầng của bạn đã bao gồm:

| Thành phần | US-East-1 | EU-West-1 | Trạng thái |
|------------|-----------|-----------|------------|
| **Bảng DynamoDB** | Chính (Primary) | Bản sao (Replica) | ✅ Hoạt động |
| **Tên bảng** | `demo-ecommerce-freetier` | `demo-ecommerce-freetier` | ✅ Đồng bộ |
| **DynamoDB Streams** | Bật | Bật | ✅ Đang sao chép |
| **Sao chép** | Hai chiều | Hai chiều | ✅ Ổn định |

## Quá trình Sao chép

### Cách dữ liệu luân chuyển giữa các vùng

| Bước | Hành động | Chi tiết |
|------|-----------|---------|
| **Bước 1** | **Người dùng ghi vào US-EAST-1** | |
| | Mục | `USER#john, SK: PROFILE` |
| | Ghi cục bộ | Thành công ngay lập tức |
| | Bản ghi Stream | Được tạo |
| **Bước 2** | **DynamoDB Streams ghi nhận thay đổi** | |
| | Bản ghi Stream | `NEW_AND_OLD_IMAGES` |
| | Dấu thời gian | `2025-08-11T15:30:00.123Z` |
| | Sự kiện | `INSERT` |
| **Bước 3** | **Sao chép giữa các vùng** | |
| | Nguồn | Stream us-east-1 |
| | Đích | Bảng eu-west-1 |
| | Độ trễ | 500ms - 2 giây |
| **Bước 4** | **EU-WEST-1 nhận bản cập nhật** | |
| | Mục xuất hiện | `USER#john, SK: PROFILE` |
| | Có sẵn để đọc | Ngay lập tức |
| | Trạng thái | Đã sao chép ✅ |

## Mô hình Nhất quán

### Nhất quán Cuối cùng (Eventually Consistent)

Global Tables cung cấp **nhất quán cuối cùng** giữa các vùng:

- **Ngay lập tức**: Ghi thành công ngay tại vùng nguồn
- **Lan truyền**: Thay đổi được sao chép đến các vùng khác trong 0.5-2 giây
- **Hội tụ**: Tất cả các vùng cuối cùng sẽ có dữ liệu giống nhau
- **Bền vững**: Dữ liệu không bao giờ bị mất trong quá trình sao chép

### Giải quyết Xung đột

Khi cùng một mục được sửa đổi ở nhiều vùng đồng thời:

**Chiến lược Last Writer Wins**:
1. **So sánh dấu thời gian** của các bản cập nhật xung đột
2. **Giữ lại dấu thời gian muộn hơn** (thay đổi gần đây nhất)
3. **Ghi đè các thay đổi trước đó** ở tất cả các vùng
4. **Thông báo qua CloudWatch** metrics

Ví dụ về xung đột:
```text
Thời gian: 15:30:00 - Người dùng US cập nhật: name = "John Smith"
Thời gian: 15:30:01 - Người dùng EU cập nhật: name = "John Doe"

Kết quả: Tất cả các vùng sẽ có name = "John Doe"
(Bản cập nhật EU thắng do dấu thời gian muộn hơn)
```

## Lợi ích của Global Tables

### Lợi ích về Hiệu suất

- **Độ trễ cục bộ**: Thời gian phản hồi dưới 10ms trong mỗi vùng
- **Quy mô toàn cầu**: Phục vụ người dùng trên toàn thế giới mà không bị giảm hiệu suất
- **Phân phối tải**: Lưu lượng được phân phối giữa các vùng

### Lợi ích về Khả dụng

- **Chuyển đổi vùng tự động**: Tự động chuyển đổi nếu một vùng không khả dụng
- **Khôi phục thảm họa**: DR tích hợp giữa các vùng địa lý
- **99.999% Khả dụng**: Khả dụng cao hơn so với triển khai một vùng

### Lợi ích về Vận hành

- **Không cần thay đổi mã**: Ứng dụng hoạt động với bất kỳ vùng nào
- **Tự động mở rộng**: Mỗi vùng mở rộng độc lập
- **Quản lý hợp nhất**: Một bảng duy nhất trên tất cả các vùng

## Các Khái niệm Chính cần nhớ

### Sao chép Đa chủ (Multi-Master Replication)
- **Bất kỳ vùng nào cũng có thể chấp nhận ghi**
- **Tất cả các vùng đều có thể phục vụ đọc**
- **Không có điểm lỗi duy nhất**

### Sao chép Dựa trên Stream
- **DynamoDB Streams** cung cấp năng lượng cho sao chép
- **Giao hàng theo thứ tự** đảm bảo tính nhất quán
- **Logic thử lại** xử lý các lỗi tạm thời

### Độc lập Vùng
- **Mỗi vùng** hoạt động độc lập
- **Phân vùng mạng** không ảnh hưởng đến hoạt động cục bộ
- **Kết nối giữa các vùng** chỉ cần thiết cho sao chép

{{% notice tip %}}
**Lợi thế Workshop**: Mẫu CloudFormation của bạn đã cấu hình tất cả các thành phần Global Tables. Bạn có thể tập trung vào việc hiểu và kiểm tra chức năng!
{{% /notice %}}

## Hạn chế cần hiểu

### Thách thức về Nhất quán Cuối cùng
- **Không nhất quán tạm thời** có thể xảy ra trong 0.5-2 giây
- **Thiết kế ứng dụng** phải xử lý nhất quán cuối cùng
- **Nhất quán mạnh** chỉ khả dụng trong một vùng

### Hạn chế về Giải quyết Xung đột
- **Last Writer Wins** có thể ghi đè các thay đổi
- **Không có logic giải quyết xung đột tùy chỉnh**
- **Xử lý xung đột ở cấp ứng dụng** có thể cần thiết

### Phụ thuộc giữa các vùng
- **Kết nối mạng** cần thiết cho sao chép
- **Sự cố vùng** có thể làm chậm sao chép
- **Độ trễ giữa các vùng** ảnh hưởng đến tốc độ sao chép

## Các Trường hợp Sử dụng Thực tế

### Lý tưởng cho Global Tables

| Trường hợp Sử dụng | Tại sao nó hoạt động | Cân nhắc |
|---------------------|----------------------|----------|
| **Hồ sơ người dùng** | Cập nhật không thường xuyên, đọc nhiều | Xử lý xung đột hồ sơ |
| **Danh mục sản phẩm** | Phân phối nội dung, truy cập toàn cầu | Thách thức đồng bộ hóa tồn kho |
| **Bảng xếp hạng trò chơi** | Cạnh tranh toàn cầu, nhất quán cuối cùng OK | Có thể xảy ra xung đột điểm số |
| **Dữ liệu cảm biến IoT** | Dữ liệu chuỗi thời gian, chỉ thêm | Khối lượng ghi cao |

### Các kịch bản thách thức

- **Giao dịch tài chính** (yêu cầu nhất quán mạnh)
- **Quản lý tồn kho** (mức tồn kho cần độ chính xác)
- **Hợp tác thời gian thực** (cần nhất quán ngay lập tức)

## Bước Tiếp theo

Bây giờ bạn đã hiểu kiến trúc Global Tables, hãy xác minh cấu hình đa vùng của bạn và xem sao chép hoạt động thông qua AWS Console.

{{% notice info %}}
**Sẵn sàng Khám phá**: Global Tables của bạn đã được cấu hình và đang chạy. Đã đến lúc xem chúng hoạt động!
{{% /notice %}}
