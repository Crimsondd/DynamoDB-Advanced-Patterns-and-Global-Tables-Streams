---
title : "3.2 Xác minh Global Setup"
date : "2025-08-11"
weight : 32
chapter : false
pre : " <b> 3.2 </b> "
---

# Xác minh Global Setup

✅ **Xác minh từng bước cấu hình Global Tables của bạn thông qua AWS Console**

## Tổng quan

CloudFormation template của bạn đã tự động cấu hình Global Tables giữa US-East-1 và EU-West-1. Hãy xác minh mọi thứ hoạt động đúng trước khi chúng ta bắt đầu kiểm tra việc sao chép.

## Bước 1: Truy cập Primary Region

### Điều hướng đến US-East-1

1. **Mở AWS Console**: Đảm bảo bạn đã đăng nhập
2. **Kiểm tra Region**: Góc trên-phải sẽ hiển thị "N. Virginia"
3. **Chuyển đổi nếu cần**: Nhấp vào dropdown region → "US East (N. Virginia)"

![US_East](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/US_East.png?featherlight=false&width=50pc)

### Tìm DynamoDB Table của bạn

1. **Dịch vụ**: Điều hướng đến dịch vụ DynamoDB
2. **Bảng**: Nhấp vào "Tables" trong thanh bên trái
3. **Định vị**: Tìm `demo-ecommerce-freetier`
4. **Trạng thái**: Xác minh bảng hiển thị "Active"

![DynamoDB_Tables](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/DynamoDB_Tables.png?featherlight=false&width=90pc)

## Bước 2: Kiểm tra Cấu hình Global Tables

### Truy cập Tab Global Tables

1. **Nhấp**: Tên bảng `demo-ecommerce-freetier`
2. **Điều hướng**: Nhấp vào tab "Global tables"
3. **Xem xét**: Cấu hình Global Tables

![Global_tables_tab](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/Global_tables_tab.png?featherlight=false&width=90pc)

### Xác minh Trạng thái Global Tables

**Cấu hình Mong đợi**:

| Region | Status | Role | Health |
|--------|--------|------|--------|
| **us-east-1** | Active | Primary | ✅ Healthy |
| **eu-west-1** | Active | Replica | ✅ Healthy |

**Các chỉ số chính cần xác minh**:
- **Trạng thái Sao chép**: "Healthy" hoặc "Active"
- **Lần Sao chép Cuối**: Dấu thời gian gần đây
- **Cập nhật Đang Chờ**: Sẽ là 0

![Global_tables](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/Global_tables.png?featherlight=false&width=90pc)

## Bước 3: Xác minh Khu vực Thứ cấp

### Chuyển đến EU-West-1

1. **Bộ chọn Khu vực**: Nhấp vào dropdown khu vực (góc trên bên phải)
2. **Chọn**: "Europe (Ireland)" - eu-west-1
3. **Chờ**: Cho phép console chuyển đổi khu vực

![Europe](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/Europe.png?featherlight=false&width=50pc)

### Kiểm tra Bảng Sao chép

1. **Điều hướng**: DynamoDB → Tables
2. **Tìm**: Cùng tên bảng `demo-ecommerce-freetier`
3. **Xác minh**: Bảng tồn tại và hiển thị "Active"
4. **Kiểm tra**: Tab global tables hiển thị trạng thái sao chép

**Mong đợi trong EU-West-1**:
- **Tên Bảng**: `demo-ecommerce-freetier` (giống hệt)
- **Trạng thái**: Active
- **Vai trò**: Bảng sao chép
- **Khu vực Chính**: us-east-1

![EU_region_showing](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/EU_region_showing.png?featherlight=false&width=90pc)

## Bước 4: So sánh Lược đồ Bảng

### Xác minh Tính nhất quán của Lược đồ

Cả hai khu vực sẽ có lược đồ bảng giống hệt nhau:

**Khóa Chính**:
- **Khóa Phân vùng**: PK (Chuỗi)
- **Khóa Sắp xếp**: SK (Chuỗi)

**Chỉ mục Bổ sung Toàn cầu**:
- **GSI1**: GSI1PK (Chuỗi), GSI1SK (Chuỗi)
- **GSI2**: GSI2PK (Chuỗi), GSI2SK (Chuỗi)

**Cài đặt**:
- **Công suất Đọc**: 5 đơn vị (được cấp phát)
- **Công suất Ghi**: 5 đơn vị (được cấp phát)
- **Khôi phục theo thời gian**: Bật
- **DynamoDB Streams**: Bật

![table_schema](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/table_schema.png?featherlight=false&width=90pc)

## Bước 5: Kiểm tra Dữ liệu Hiện tại

### Xác minh Việc Sao chép Dữ liệu Tồn tại

Nếu bạn đã hoàn thành Module 2, hãy kiểm tra rằng dữ liệu hiện có của bạn xuất hiện trong cả hai khu vực:

**Chuyển đến US-East-1**:
1. **Đi tới**: Tab Items
2. **Đếm mục**: Ghi lại số lượng mục

**Chuyển đến EU-West-1**:
1. **Đi tới**: Tab Items  
2. **So sánh số lượng**: Sẽ khớp chính xác với khu vực US

**Nếu số lượng mục không khớp**:
- Chờ 1-2 phút cho việc sao chép
- Làm mới trang trình duyệt
- Kiểm tra trạng thái sức khỏe của Global Tables

![item_counts1](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/item_counts1.png?featherlight=false&width=90pc)
![item_counts2](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/item_counts2.png?featherlight=false&width=90pc)

## Bước 6: Xác minh Cấu hình Luồng

### Kiểm tra DynamoDB Streams

**Trong US-East-1**:
1. **Tổng quan về Bảng**: Đi tới chi tiết bảng
2. **Xuất và luồng**: Nhấp vào tab
3. **Chi tiết luồng**: Xác minh cài đặt

**Cấu hình Luồng Mong đợi**:
- **Trạng thái Luồng**: Bật
- **Loại chế độ xem luồng**: Ảnh mới và cũ
- **Stream ARN**: Sẽ có mặt
- **Số lượng Shard**: 1 hoặc nhiều shard đang hoạt động

![DynamoDB_Streams_config](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/DynamoDB_Streams_config.png?featherlight=false&width=90pc)

## Bước 7: Bảng điều khiển Kiểm tra Sức khỏe

### Giám sát Sức khỏe Sao chép

**Truy cập Giám sát**:
1. **Tab Chỉ số**: Nhấp trong chế độ xem bảng
2. **Chỉ số Global Tables**: Tìm các chỉ số sao chép
3. **Các chỉ số chính**: 
   - Độ trễ sao chép
   - Số lượng sao chép đang chờ
   - Sự kiện sao chép thất bại

**Các chỉ số Khỏe mạnh**:
- **Độ trễ Sao chép**: < 2 giây trung bình
- **Số lượng Đang chờ**: 0 hoặc rất thấp
- **Tỷ lệ Lỗi**: 0%

![replication_metrics](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/replication_metrics.png?featherlight=false&width=90pc)

## Bước 8: Kiểm tra Kết nối Mạng

### Kiểm tra Giao tiếp Giữa các Khu vực

**Xác minh kết nối đơn giản**:
1. **Tạo mục kiểm tra** trong US-East-1
2. **Chờ 30 giây**
3. **Kiểm tra EU-West-1** cho mục đó
4. **Xóa mục kiểm tra** từ một trong hai khu vực

**Ví dụ về Mục Kiểm tra**:
```json
{
  "PK": "TEST#connectivity",
  "SK": "VERIFICATION",
  "timestamp": "2025-08-11T15:00:00Z",
  "test_purpose": "global_tables_verification"
}
```

![test_item_appearing](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/test_item_appearing.png?featherlight=false&width=90pc)

## Khắc phục sự cố Các Vấn đề Thường gặp

### Vấn đề: Không tìm thấy Bảng trong EU-West-1

**Nguyên nhân Có thể**:
- Chọn sai khu vực
- Triển khai CloudFormation chưa hoàn tất
- Thiết lập Global Tables thất bại

**Giải pháp**:
1. **Kiểm tra lại khu vực** trong góc trên bên phải
2. **Xác minh CloudFormation** stack đã hoàn tất thành công
3. **Kiểm tra quyền IAM** cho truy cập giữa các khu vực

### Vấn đề: Trạng thái Sao chép Không Khỏe mạnh

**Kiểm tra Các Mục này**:
- **Kết nối mạng** giữa các khu vực
- **DynamoDB Streams** đã bật trên bảng nguồn
- **Quyền IAM** cho dịch vụ Global Tables
- **Công suất Bảng** không bị giới hạn

### Vấn đề: Số lượng Mục Không Khớp

**Các Bước Khắc phục sự cố**:
1. **Chờ lâu hơn** (lên đến 2 phút)
2. **Làm mới trang** trình duyệt
3. **Kiểm tra lỗi** trong nhật ký CloudWatch
4. **Xác minh không bị giới hạn** trong các chỉ số

## Danh sách Kiểm tra Xác minh

Trước khi tiếp tục đến các hoạt động đa khu vực:

- [ ] **Cả hai khu vực đều có thể truy cập** thông qua AWS Console
- [ ] **Bảng tồn tại** trong cả us-east-1 và eu-west-1
- [ ] **Trạng thái Global Tables** hiển thị "Healthy" hoặc "Active"
- [ ] **Lược đồ giống hệt** giữa các khu vực
- [ ] **DynamoDB Streams** đã bật
- [ ] **Dữ liệu hiện có đã được sao chép** (nếu có)
- [ ] **Kiểm tra kết nối** hoạt động
- [ ] **Chỉ số giám sát** có sẵn

{{% notice success %}}
**Xác minh Hoàn tất**: Thiết lập Global Tables của bạn khỏe mạnh và sẵn sàng cho các hoạt động đa khu vực!
{{% /notice %}}

## Các Bước Tiếp Theo

Với Global Tables đã được xác minh và khỏe mạnh, bạn đã sẵn sàng để trải nghiệm các hoạt động đa khu vực. Trong phần tiếp theo, chúng ta sẽ tạo dữ liệu trong một khu vực và theo dõi nó tự động xuất hiện trong khu vực khác!

{{% notice tip %}}
**Mẹo Chuyên nghiệp**: Giữ cả hai tab khu vực mở trong trình duyệt của bạn (US-East-1 và EU-West-1) để dễ dàng chuyển đổi giữa chúng trong các bài tập.
{{% /notice %}}
