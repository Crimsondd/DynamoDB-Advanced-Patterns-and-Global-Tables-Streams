---
title : "3.3 Hoạt động Đa Vùng"
date : "2025-08-11"
weight : 33
chapter : false
pre : " <b> 3.3 </b> "
---

# Hoạt động Đa Vùng

🌍 **Thực hành trực tiếp với các thao tác đọc/ghi giữa các vùng và kiểm tra sao chép**

## Tổng quan

Bây giờ Global Tables của bạn đã được xác minh, hãy trải nghiệm các hoạt động đa vùng. Bạn sẽ tạo dữ liệu ở một vùng, xác minh nó được sao chép sang vùng khác và kiểm tra các kịch bản giải quyết xung đột.

## Bài tập 1: Ghi vào Vùng Chính, Đọc từ Bản Sao

### Bước 1: Tạo Người Dùng Toàn Cầu ở US-East-1

**Đảm bảo bạn đang ở US-East-1**:
1. **Kiểm tra vùng**: Góc trên bên phải sẽ hiển thị "N. Virginia"
2. **Điều hướng**: DynamoDB → Tables → `demo-ecommerce-freetier`
3. **Đi tới**: Tab Items
4. **Nhấp vào**: "Create item"

![3.3.1](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/3.3.1.png?featherlight=false&width=90pc)
![3.3.1(1)](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/3.3.1(1).png?featherlight=false&width=90pc)

### Mẫu Tạo Người Dùng

**Chuyển sang chế độ xem JSON** và tạo:

```json
{
  "PK": "USER#global-user-[tên-của-bạn]",
  "SK": "PROFILE",
  "GSI1PK": "USER#[tên-của-bạn]@global.com",
  "GSI1SK": "PROFILE",
  "user_id": "global-user-[tên-của-bạn]",
  "name": "[Tên Của Bạn] Global",
  "email": "[tên-của-bạn]@global.com",
  "region_created": "us-east-1",
  "created_timestamp": "2025-08-11T15:30:00Z",
  "test_type": "global_replication",
  "workshop_session": "module3"
}
```

**Quan trọng**: Thay thế `[tên-của-bạn]` bằng tên thực của bạn để làm cho các mục trở nên duy nhất.

![3.3.2](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/3.3.2.png?featherlight=false&width=90pc)

### Bước 2: Ghi lại Thời gian Tạo

**Ghi lại chi tiết**:
1. **Nhấp vào "Create item"**
2. **Ghi lại thời gian**: Ghi lại khi bạn nhấp vào tạo
3. **Chụp ảnh màn hình**: Của mục đã tạo

![3.3.3](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/3.3.3.png?featherlight=false&width=90pc)

### Bước 3: Chuyển sang EU-West-1

**Thay đổi vùng**:
1. **Bộ chọn vùng**: Nhấp vào menu thả xuống (góc trên bên phải)
2. **Chọn**: "Europe (Ireland)"
3. **Chờ**: Để hoàn tất chuyển vùng
4. **Điều hướng**: DynamoDB → Tables → `demo-ecommerce-freetier`

![Europe](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/Europe.png?featherlight=false&width=50pc)

### Bước 4: Truy vấn Dữ liệu Sao chép

**Tìm kiếm người dùng của bạn**:
1. **Tab Items**: Điều hướng đến chế độ xem các mục
2. **Nhấp vào**: Nút "Query"
3. **Cấu hình truy vấn**:
   - **Khóa phân vùng (PK)**: `USER#global-user-[tên-của-bạn]`
   - **Khóa sắp xếp (SK)**: `PROFILE`
4. **Nhấp vào**: "Run"

![3.3.4](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/3.3.4.png?featherlight=false&width=90pc)

### Bước 5: Xác minh Sao chép

**Kết quả mong đợi**:
- **Nếu ngay lập tức**: Mục xuất hiện ngay lập tức
- **Nếu bị trễ**: Chờ 30-60 giây và thử lại
- **Thời gian sao chép**: Ghi lại mất bao lâu

**Xác minh dữ liệu**:
- **Tất cả các thuộc tính**: Phải khớp chính xác
- **region_created**: Vẫn sẽ hiển thị "us-east-1"
- **Dấu thời gian**: Phải giống hệt nhau

![3.3.5](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/3.3.5.png?featherlight=false&width=90pc)

## Bài tập 2: Ghi từ Bản Sao, Đọc từ Vùng Chính

### Bước 1: Tạo Sản phẩm ở EU-West-1

**Ở lại EU-West-1** và tạo một sản phẩm:

```json
{
  "PK": "PRODUCT#eu-product-[id-duy-nhat]",
  "SK": "DETAILS",
  "GSI1PK": "CATEGORY#eu-electronics",
  "GSI1SK": "PRODUCT#eu-product-[id-duy-nhat]",
  "GSI2PK": "PRICE#200-500",
  "GSI2SK": "PRODUCT#eu-product-[id-duy-nhat]",
  "product_id": "eu-product-[id-duy-nhat]",
  "name": "European Smartphone",
  "description": "Created in EU region",
  "category": "eu-electronics",
  "price": 299,
  "stock": 50,
  "region_created": "eu-west-1",
  "created_timestamp": "2025-08-11T15:35:00Z",
  "test_type": "reverse_replication"
}
```

![3.3.6](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/3.3.6.png?featherlight=false&width=90pc)

### Bước 2: Chuyển lại sang US-East-1

**Quay lại vùng chính**:
1. **Bộ chọn vùng**: "US East (N. Virginia)"
2. **Điều hướng**: DynamoDB → Tables → Items
3. **Truy vấn sản phẩm**:
   - **PK**: `PRODUCT#eu-product-[id-duy-nhat]`
   - **SK**: `DETAILS`

![3.3.7](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/3.3.7.png?featherlight=false&width=90pc)

### Bước 3: Xác minh Sao chép Ngược

**Kiểm tra kết quả**:
- **Sản phẩm xuất hiện**: Trong vùng US
- **region_created**: Vẫn hiển thị "eu-west-1"
- **Tất cả dữ liệu nguyên vẹn**: Bản sao chính xác từ EU

Điều này chứng minh **sao chép hai chiều** - bạn có thể ghi vào bất kỳ vùng nào!

![3.3.8](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/3.3.8.png?featherlight=false&width=90pc)

## Bài tập 3: Kiểm tra Giải quyết Xung đột

### Bước 1: Tạo Đơn hàng Cơ sở

**Trong US-East-1**, tạo một đơn hàng:

```json
{
  "PK": "USER#global-user-[tên-của-bạn]",
  "SK": "ORDER#conflict-test",
  "GSI1PK": "ORDER#conflict-test",
  "GSI1SK": "DETAILS",
  "GSI2PK": "STATUS#pending",
  "GSI2SK": "ORDER#conflict-test",
  "order_id": "conflict-test",
  "user_id": "global-user-[tên-của-bạn]",
  "status": "pending",
  "total_amount": 100,
  "last_updated_region": "us-east-1",
  "conflict_test": true
}
```

**Chờ 2 phút** cho việc sao chép hoàn tất.

![3.3.9](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/3.3.9.png?featherlight=false&width=90pc)

### Bước 2: Cập nhật Đồng thời (Nâng cao)

**Nếu làm việc với một đối tác**:
1. **Đối tác A**: Cập nhật đơn hàng trong US-East-1
2. **Đối tác B**: Cập nhật cùng một đơn hàng trong EU-West-1
3. **Cả hai cùng thực hiện**: Trong vòng 10 giây của nhau

**Cập nhật US** (Đối tác A):
```json
{
  "total_amount": 150,
  "last_updated_region": "us-east-1",
  "update_timestamp": "2025-08-11T15:40:00Z"
}
```

**Cập nhật EU** (Đối tác B):
```json
{
  "total_amount": 200,
  "last_updated_region": "eu-west-1", 
  "update_timestamp": "2025-08-11T15:40:05Z"
}
```

![3.3.10](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/3.3.10.png?featherlight=false&width=90pc)

### Bước 3: Quan sát Giải quyết Xung đột

**Sau 2-3 phút**:
1. **Kiểm tra cả hai vùng**: Truy vấn cùng một đơn hàng
2. **So sánh kết quả**: Cập nhật nào đã thắng?
3. **Hiểu lý do**: Cập nhật có dấu thời gian sau cùng sẽ thắng

**Kết quả mong đợi**: Cập nhật EU thắng vì dấu thời gian 15:40:05 > 15:40:00

![3.3.11](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/3.3.11.png?featherlight=false&width=90pc)
![3.3.11(1)](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/3.3.11(1).png?featherlight=false&width=90pc)

## Bài tập 4: Các Mẫu Truy vấn Giữa các Vùng

### Bước 1: Truy vấn Danh mục trong EU

**Kiểm tra các truy vấn GSI** hoạt động giữa các vùng:
1. **Ở lại EU-West-1**
2. **Truy vấn Chỉ mục**: GSI1
3. **GSI1PK**: `CATEGORY#eu-electronics`
4. **Chạy truy vấn**

**Kết quả mong đợi**: Hiển thị các sản phẩm được tạo trong vùng EU

![3.3.12](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/3.3.12.png?featherlight=false&width=90pc)

### Bước 2: Truy vấn Trạng thái trong US

**Kiểm tra các truy vấn trạng thái giữa các vùng**:
1. **Chuyển đến US-East-1**
2. **Truy vấn Chỉ mục**: GSI2
3. **GSI2PK**: `STATUS#pending`
4. **Chạy truy vấn**

**Kết quả mong đợi**: Hiển thị các đơn hàng từ cả hai vùng với trạng thái đang chờ xử lý

![3.3.13](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/3.3.13.png?featherlight=false&width=90pc)

## Bài tập 5: Phân tích Thời gian Sao chép

### Bước 1: Đo tốc độ sao chép

**Tạo các mục có dấu thời gian**:
1. **Ghi lại thời gian bắt đầu**: Ghi chú thời gian chính xác trước khi tạo
2. **Tạo mục**: Trong một vùng
3. **Chuyển vùng**: Ngay lập tức
4. **Truy vấn liên tục**: Cho đến khi mục xuất hiện
5. **Tính toán độ trễ**: Thời gian kết thúc - thời gian bắt đầu

**Mẫu Mục Kiểm tra**:
```json
{
  "PK": "TEST#timing-[timestamp]",
  "SK": "REPLICATION",
  "created_at": "[exact-timestamp]",
  "source_region": "us-east-1",
  "test_purpose": "timing_analysis"
}
```

### Bước 2: Ghi lại Kết quả

**Ghi lại phát hiện của bạn**:
- **Sao chép nhanh nhất**: ___ giây
- **Sao chép chậm nhất**: ___ giây
- **Thời gian trung bình**: ___ giây
- **Tính nhất quán**: Thường < 2 giây

## Các Kịch bản Thực tế

### Kịch bản 1: Đăng nhập Người dùng Toàn cầu

**Giả lập ứng dụng toàn cầu**:
1. **Người dùng đăng nhập**: Vùng US
2. **Hồ sơ được cập nhật**: Dấu thời gian đăng nhập cuối
3. **Người dùng di chuyển**: Đến vùng EU  
4. **Ứng dụng kiểm tra**: Hồ sơ từ EU
5. **Xác minh**: Thời gian đăng nhập gần đây hiển thị

### Kịch bản 2: Quản lý Tồn kho

**Cập nhật tồn kho sản phẩm**:
1. **Sản phẩm được bán**: Vùng US (-1 tồn kho)
2. **Cùng sản phẩm**: Truy vấn vùng EU
3. **Mức tồn kho**: Nhất quán theo thời gian
4. **Logic kinh doanh**: Xử lý sự không nhất quán tạm thời

### Kịch bản 3: Xử lý Đơn hàng

**Luồng đơn hàng đa vùng**:
1. **Đơn hàng được tạo**: Vùng EU
2. **Thanh toán được xử lý**: Vùng US
3. **Trạng thái được cập nhật**: Vùng EU
4. **Thực hiện**: Đọc từ vùng gần nhất

## Giám sát Hiệu suất

### Kiểm tra Các chỉ số Sao chép

**Trong các bài tập**:
1. **Giám sát**: Các chỉ số CloudWatch
2. **Theo dõi**: Độ trễ sao chép
3. **Quan sát**: Số lượng sao chép đang chờ xử lý
4. **Theo dõi**: Tỷ lệ lỗi (sẽ là 0)

![3.3.14](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/3.3.14.png?featherlight=false&width=90pc)

## Hướng dẫn Khắc phục sự cố

### Sao chép Không hoạt động

**Các vấn đề phổ biến**:
- **Chọn sai vùng**: Kiểm tra kỹ vùng đã chọn
- **Lỗi chính tả trong khóa**: Cần khớp chính xác cho các truy vấn
- **Bộ nhớ đệm trình duyệt**: Làm mới trang
- **Chờ lâu hơn**: Có thể mất đến 2 phút

### Truy vấn Trả về Rỗng

**Danh sách kiểm tra**:
- **PK/SK chính xác**: Khớp chuỗi chính xác
- **Vùng chính xác**: Mục tồn tại trong vùng được truy vấn
- **GSI đã được điền**: Các khóa GSI được bao gồm trong mục
- **Loại truy vấn**: Sử dụng Query, không phải Scan

### Giải quyết Xung đột Không như Mong đợi

**Hiểu biết**:
- **Độ chính xác của dấu thời gian**: Ở mức độ mili giây
- **Đồng bộ hóa đồng hồ**: AWS xử lý thời gian
- **Thiết kế ứng dụng**: Lập kế hoạch cho việc ghi đè

{{% notice warning %}}
**Quan trọng**: Nếu bạn gặp sự cố, hãy kiểm tra trạng thái sức khỏe của Global Tables trong bảng điều khiển và xác minh kết nối mạng.
{{% /notice %}}

## Tóm tắt Bài tập

Bằng cách hoàn thành các bài tập này, bạn đã trải nghiệm:

- ✅ **Sao chép giữa các vùng** trong cả hai hướng
- ✅ **Thời gian nhất quán theo sự kiện**
- ✅ **Giải quyết xung đột** với Người viết Cuối cùng thắng
- ✅ **Các mẫu truy vấn** hoạt động giữa các vùng
- ✅ **Các kịch bản thực tế** và phân tích thời gian

{{% notice success %}}
**Thành thạo Đa Vùng**: Bây giờ bạn hiểu cách Global Tables cho phép các ứng dụng thực sự toàn cầu với hiệu suất địa phương!
{{% /notice %}}

## Các Bước Tiếp theo

Với trải nghiệm thực hành Global Tables đã hoàn thành, hãy giám sát hiệu suất sao chép và hiểu các chỉ số giúp bạn vận hành các ứng dụng toàn cầu trong môi trường sản xuất.
