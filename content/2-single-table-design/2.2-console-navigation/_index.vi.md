---
title : "2.2 Điều hướng Console"
date : "2025-08-11"
weight : 22
chapter : false
pre : " <b> 2.2 </b> "
---

# Điều hướng DynamoDB Console

🖥️ **Hướng dẫn nhanh để điều hướng DynamoDB Console cho Single Table Design**

## Truy cập Table của bạn

### Bước 1: Điều hướng đến DynamoDB Service

1. **Mở AWS Console**: https://console.aws.amazon.com
2. **Xác minh Region**: Đảm bảo bạn đang ở **US East (N. Virginia)**
3. **Truy cập DynamoDB**: Services → Database → DynamoDB

![DynamoDB-search](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/2/DynamoDB-search.png?featherlight=false&width=90pc)

### Bước 2: Tìm Workshop Table của bạn

1. **Click "Tables"** trong sidebar bên trái
2. **Tìm table**: `demo-ecommerce-freetier`
3. **Xác minh Status**: Phải hiển thị "Active"
4. **Click tên table** để vào chi tiết table

![DynamoDB-Server](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/2/DynamoDB-Server.png?featherlight=false&width=90pc)

## Table Overview Dashboard

### Hiểu về Table Layout

Khi bạn click vào table của mình, bạn sẽ thấy một số tab:

| Tab | Mục đích | Bạn sẽ sử dụng để |
|-----|---------|-------------------|
| **Overview** | Cấu hình table | Kiểm tra status, keys, capacity |
| **Items** | Quản lý dữ liệu | Tạo, xem, chỉnh sửa items |
| **Metrics** | Dữ liệu hiệu suất | Giám sát usage và costs |
| **Indexes** | Quản lý GSI | Xem Global Secondary Indexes |
| **Global tables** | Thiết lập multi-region | Kiểm tra replication status |

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của trang table overview hiển thị tất cả các tab
{{% /notice %}}

### Thông tin chính cần lưu ý

**Cấu hình Table**:
- **Tên table**: `demo-ecommerce-freetier`
- **Partition key**: `PK` (String)
- **Sort key**: `SK` (String)
- **Table status**: Active
- **Item count**: Hiện tại là 0 (table trống)

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của chi tiết table hiển thị cấu hình PK và SK
{{% /notice %}}

## Items Tab - Workspace dữ liệu của bạn

### Truy cập Items View

1. **Click tab "Items"** 
2. **Xem cấu trúc table**: Hiện tại trống
3. **Lưu ý các cột**: PK, SK, và các attributes bổ sung

Đây là nơi bạn sẽ:
- ✅ Tạo items mới (users, products, orders)
- ✅ Xem dữ liệu hiện có
- ✅ Chỉnh sửa attributes của item
- ✅ Xóa items nếu cần

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của tab Items trống hiển thị cột PK và SK
{{% /notice %}}

### Giao diện tạo Items

**Để tạo item mới**:
1. **Click nút "Create item"**
2. **Chọn phương thức input**:
   - **Form view**: Giao diện point-and-click
   - **JSON view**: Chỉnh sửa JSON trực tiếp (được khuyến nghị)
3. **Chuyển sang JSON view** để nhập dữ liệu dễ dàng hơn

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của dialog "Create item" hiển thị tùy chọn Form vs JSON view
{{% /notice %}}

## JSON View để nhập dữ liệu

### Hiểu về định dạng JSON

Khi tạo items, bạn sẽ sử dụng cấu trúc JSON này:

```json
{
  "PK": "USER#user123",
  "SK": "PROFILE",
  "name": "John Doe",
  "email": "john@example.com",
  "created_at": "2025-08-11T10:00:00Z"
}
```

**Điểm chính**:
- **PK và SK**: Luôn luôn bắt buộc (composite primary key của bạn)
- **Attributes bổ sung**: Thêm theo nhu cầu cho từng loại entity
- **Kiểu dữ liệu**: Hỗ trợ strings, numbers, booleans, lists, maps

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của JSON view trong dialog create item với dữ liệu mẫu
{{% /notice %}}

## Giao diện Query

### Truy cập chức năng Query

**Để query table của bạn**:
1. **Đi đến tab Items**
2. **Click nút "Query"** (bên cạnh Create item)
3. **Chọn loại query**:
   - **Table query**: Query table chính
   - **Index query**: Query GSI

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot hiển thị vị trí nút Query và lựa chọn loại query
{{% /notice %}}

### Tham số Query

**Đối với table queries, bạn sẽ chỉ định**:
- **Partition key (PK)**: Giá trị chính xác (ví dụ: `USER#user123`)
- **Sort key (SK)**: Điều kiện tùy chọn:
  - Khớp chính xác: `PROFILE`
  - Bắt đầu với: `ORDER#`
  - Giữa: Range queries

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của dialog query hiển thị các trường input PK và SK
{{% /notice %}}

## Điều hướng Global Secondary Index (GSI)

### Xem cấu hình GSI

1. **Click tab "Indexes"**
2. **Xem GSI1**: Được sử dụng cho category-based queries
3. **Xem GSI2**: Được sử dụng cho status/price-based queries

**Cấu trúc GSI**:
- **GSI1**: `GSI1PK` (Partition) + `GSI1SK` (Sort)
- **GSI2**: `GSI2PK` (Partition) + `GSI2SK` (Sort)

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của tab Indexes hiển thị cấu hình GSI1 và GSI2
{{% /notice %}}

### Query GSIs

**Để query một GSI**:
1. **Click "Query" trong tab Items**
2. **Chọn "Query (index)"**
3. **Chọn GSI**: GSI1 hoặc GSI2
4. **Nhập giá trị GSI key**

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của giao diện GSI query với dropdown lựa chọn index
{{% /notice %}}

## Metrics và Monitoring

### Kiểm tra Usage và Performance

1. **Click tab "Metrics"**
2. **Giám sát các metrics chính**:
   - **Consumed read/write capacity**
   - **Throttled requests** (phải là 0)
   - **Item count** (tăng khi bạn thêm dữ liệu)

**Tại sao điều này quan trọng**:
- ✅ Duy trì trong giới hạn Free Tier
- ✅ Giám sát hiệu suất
- ✅ Phát hiện sớm các vấn đề

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của tab Metrics hiển thị biểu đồ capacity utilization
{{% /notice %}}

## Tham chiếu Quick Actions

### Các hành động Console thường dùng

| Hành động | Vị trí | Mục đích |
|-----------|--------|----------|
| **Create Item** | Items tab → Create item | Thêm dữ liệu mới |
| **Query Table** | Items tab → Query | Tìm kiếm theo PK/SK |
| **Query GSI** | Items tab → Query (index) | Tìm kiếm theo GSI keys |
| **View Metrics** | Metrics tab | Giám sát hiệu suất |
| **Check Capacity** | Overview tab | Xác minh provisioned capacity |

## Console Tips

### Tips hiệu quả

1. **Sử dụng JSON view** để tạo item nhanh hơn
2. **Copy/paste** cấu trúc item để đảm bảo tính nhất quán
3. **Sử dụng Query, không phải Scan** để có hiệu suất tốt hơn
4. **Kiểm tra metrics thường xuyên** để giám sát usage

### Phím tắt điều hướng

- **Danh sách Tables**: DynamoDB home → Tables
- **Truy cập table nhanh**: Bookmark URL table của bạn
- **Chuyển region**: Sử dụng region selector ở góc trên-phải
- **Tìm kiếm service**: Sử dụng Ctrl+K để truy cập service nhanh

{{% notice tip %}}
**Pro Tip**: Giữ DynamoDB console mở trong tab trình duyệt riêng biệt trong suốt workshop để truy cập nhanh giữa các bài tập.
{{% /notice %}}

## Sẵn sàng cho việc tạo dữ liệu

Bây giờ bạn đã quen thuộc với giao diện console, bạn đã sẵn sàng bắt đầu tạo mô hình dữ liệu e-commerce của mình. Trong phần tiếp theo, chúng ta sẽ tạo users, products, và orders sử dụng các patterns bạn đã học.

{{% notice warning %}}
**Trước khi bắt đầu**: Đảm bảo bạn đang ở table đúng (`demo-ecommerce-freetier`) và hiểu sự khác biệt giữa các thao tác Query và Scan.
{{% /notice %}}
