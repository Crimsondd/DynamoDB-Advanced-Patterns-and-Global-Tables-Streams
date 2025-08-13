---
title : "2.3 Tạo Data Items"
date : "2025-08-11"
weight : 23
chapter : false
pre : " <b> 2.3 </b> "
---

# Tạo Data Items

📝 **Hướng dẫn từng bước để tạo dữ liệu e-commerce sử dụng Single Table Design patterns**

## Tổng quan

Trong phần này, bạn sẽ tạo các entity cốt lõi cho nền tảng e-commerce của chúng ta: Users, Products, Orders, và Order Items. Mỗi loại entity tuân theo các key patterns cụ thể để cho phép querying hiệu quả.

## Các loại Entity chúng ta sẽ tạo

| Entity | PK Pattern | SK Pattern | Mục đích |
|--------|------------|------------|----------|
| **User Profile** | `USER#userID` | `PROFILE` | Lưu trữ thông tin khách hàng |
| **Product** | `PRODUCT#productID` | `DETAILS` | Lưu trữ catalog sản phẩm |
| **Order** | `USER#userID` | `ORDER#orderID` | Liên kết orders với customers |
| **Order Item** | `ORDER#orderID` | `ITEM#productID` | Liên kết products với orders |

## Bước 1: Tạo User Profile

### Truy cập Item Creation

1. **Navigate to**: DynamoDB → Tables → `demo-ecommerce-freetier`
2. **Click**: "Items" tab
3. **Click**: "Create item" button
4. **Switch to**: JSON view

![Create_item1](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/2/Create_item1.png?featherlight=false&width=90pc)

![Create_item](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/2/Create_item.png?featherlight=false&width=90pc)

### User Profile JSON Template

**Copy và paste template này**, sau đó chỉnh sửa với thông tin của bạn:

```json
{
  "PK": "USER#user001",
  "SK": "PROFILE",
  "GSI1PK": "USER#john.doe@email.com",
  "GSI1SK": "PROFILE",
  "user_id": "user001",
  "name": "John Doe",
  "email": "john.doe@email.com",
  "phone": "+1-555-0123",
  "address": {
    "street": "123 Main St",
    "city": "Seattle",
    "state": "WA",
    "zip": "98101"
  },
  "created_at": "2025-08-11T10:00:00Z",
  "status": "active"
}
```

**Giải thích Key Patterns**:
- **PK**: `USER#user001` - Nhóm tất cả dữ liệu user lại với nhau
- **SK**: `PROFILE` - Xác định đây là record profile của user
- **GSI1PK**: `USER#john.doe@email.com` - Cho phép lookups dựa trên email
- **GSI1SK**: `PROFILE` - Duy trì tính nhất quán trong GSI

![JSON_editor](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/2/JSON_editor.png?featherlight=false&width=90pc)

### Lưu User của bạn

1. **Review JSON** để kiểm tra lỗi syntax
2. **Click "Create item"**
3. **Verify creation**: Item sẽ xuất hiện trong table view

![successfully_created](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/2/successfully_created.png?featherlight=false&width=90pc)

## Bước 2: Tạo Products

### Ví dụ Electronics Product

**Tạo product đầu tiên của bạn** - tùy chỉnh các giá trị:

```json
{
  "PK": "PRODUCT#laptop001",
  "SK": "DETAILS",
  "GSI1PK": "CATEGORY#electronics",
  "GSI1SK": "PRODUCT#laptop001",
  "GSI2PK": "PRICE#500-1000",
  "GSI2SK": "PRODUCT#laptop001",
  "product_id": "laptop001",
  "name": "Professional Laptop",
  "description": "High-performance laptop for professionals",
  "category": "electronics",
  "price": 799,
  "stock": 25,
  "brand": "TechCorp",
  "specifications": {
    "processor": "Intel i7",
    "memory": "16GB RAM",
    "storage": "512GB SSD"
  },
  "created_at": "2025-08-11T10:00:00Z",
  "status": "active"
}
```

**Giải thích Key Patterns**:
- **GSI1PK**: `CATEGORY#electronics` - Cho phép category-based queries
- **GSI2PK**: `PRICE#500-1000` - Cho phép price range queries
- **Nested attributes**: Lưu trữ chi tiết sản phẩm phức tạp

![product_creation](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/2/product_creation.png?featherlight=false&width=90pc)

### Ví dụ Books Product

**Tạo product thứ hai** trong category khác:

```json
{
  "PK": "PRODUCT#book001",
  "SK": "DETAILS",
  "GSI1PK": "CATEGORY#books",
  "GSI1SK": "PRODUCT#book001",
  "GSI2PK": "PRICE#10-50",
  "GSI2SK": "PRODUCT#book001",
  "product_id": "book001",
  "name": "DynamoDB Patterns Guide",
  "description": "Complete guide to DynamoDB design patterns",
  "category": "books",
  "price": 29,
  "stock": 100,
  "author": "Database Expert",
  "isbn": "978-1234567890",
  "pages": 350,
  "created_at": "2025-08-11T10:00:00Z",
  "status": "active"
}
```

**Lưu ý các điểm khác biệt**:
- **Category khác**: `CATEGORY#books`
- **Price range khác**: `PRICE#10-50`
- **Attributes đặc biệt cho sách**: author, isbn, pages

![products_created](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/2/products_created.png?featherlight=false&width=90pc)

## Bước 3: Tạo Order

### Order Header

**Tạo một order liên kết với user của bạn**:

```json
{
  "PK": "USER#user001",
  "SK": "ORDER#order001",
  "GSI1PK": "ORDER#order001",
  "GSI1SK": "DETAILS",
  "GSI2PK": "STATUS#pending",
  "GSI2SK": "ORDER#order001",
  "order_id": "order001",
  "user_id": "user001",
  "status": "pending",
  "total_amount": 828,
  "tax_amount": 66.24,
  "shipping_cost": 0,
  "shipping_address": {
    "street": "123 Main St",
    "city": "Seattle",
    "state": "WA",
    "zip": "98101"
  },
  "payment_method": "credit_card",
  "created_at": "2025-08-11T11:00:00Z",
  "updated_at": "2025-08-11T11:00:00Z"
}
```

**Giải thích Key Patterns**:
- **PK**: `USER#user001` - Liên kết order với user (cho phép "get user's orders")
- **SK**: `ORDER#order001` - Xác định đây là order record
- **GSI1PK**: `ORDER#order001` - Cho phép direct order lookup
- **GSI2PK**: `STATUS#pending` - Cho phép status-based queries

![relationship](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/2/relationship.png?featherlight=false&width=90pc)


## Bước 4: Tạo Order Items

### Order Item đầu tiên

**Thêm laptop vào order**:

```json
{
  "PK": "ORDER#order001",
  "SK": "ITEM#laptop001",
  "GSI1PK": "PRODUCT#laptop001",
  "GSI1SK": "ORDER#order001",
  "order_id": "order001",
  "product_id": "laptop001",
  "product_name": "Professional Laptop",
  "quantity": 1,
  "unit_price": 799,
  "total_price": 799,
  "added_at": "2025-08-11T11:00:00Z"
}
```

### Order Item thứ hai

**Thêm sách vào cùng order**:

```json
{
  "PK": "ORDER#order001",
  "SK": "ITEM#book001",
  "GSI1PK": "PRODUCT#book001",
  "GSI1SK": "ORDER#order001",
  "order_id": "order001",
  "product_id": "book001",
  "product_name": "DynamoDB Patterns Guide",
  "quantity": 1,
  "unit_price": 29,
  "total_price": 29,
  "added_at": "2025-08-11T11:00:00Z"
}
```

**Giải thích Key Patterns**:
- **PK**: `ORDER#order001` - Nhóm items với order của chúng
- **SK**: `ITEM#productID` - Xác định specific items
- **GSI1**: Tạo product-to-order relationship

![order_items](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/2/order_items.png?featherlight=false&width=90pc)

## Xác minh cấu trúc dữ liệu của bạn

### Kiểm tra nội dung Table

Sau khi tạo tất cả items, table của bạn sẽ chứa:

| PK | SK | Entity Type |
|----|----|-------------|
| USER#user001 | PROFILE | User Profile |
| USER#user001 | ORDER#order001 | Order (linked to user) |
| PRODUCT#laptop001 | DETAILS | Product (Electronics) |
| PRODUCT#book001 | DETAILS | Product (Books) |
| ORDER#order001 | ITEM#laptop001 | Order Item (Laptop) |
| ORDER#order001 | ITEM#book001 | Order Item (Book) |

![all_created](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/2/all_created.png?featherlight=false&width=90pc)


### Validate Relationships

**Xác minh các relationships này hoạt động**:

1. **User → Orders**: `PK=USER#user001` trả về profile + orders
2. **Order → Items**: `PK=ORDER#order001` trả về order details + items
3. **Category grouping**: GSI1 với `CATEGORY#electronics` trả về products
4. **Status grouping**: GSI2 với `STATUS#pending` trả về orders

## Tips tạo dữ liệu

### JSON Best Practices

1. **Sử dụng naming nhất quán**: Tuân theo các patterns đã thiết lập
2. **Validate JSON syntax**: Kiểm tra missing commas, brackets
3. **Include required attributes**: PK, SK, và GSI keys
4. **Sử dụng meaningful IDs**: Làm cho chúng readable và unique

### Các lỗi thường gặp cần tránh

- ❌ **Missing GSI keys**: Luôn populate GSI1PK/GSI1SK và GSI2PK/GSI2SK
- ❌ **Inconsistent patterns**: Tuân thủ định dạng `ENTITY#ID`
- ❌ **Wrong data types**: Sử dụng strings cho keys, appropriate types cho values
- ❌ **JSON syntax errors**: Missing quotes, commas, hoặc brackets

{{% notice warning %}}
**Quan trọng**: Nếu bạn gặp JSON syntax errors, hãy kiểm tra quotation marks, commas, và brackets của bạn. Console sẽ highlight các syntax issues.
{{% /notice %}}

## Tùy chỉnh dữ liệu của bạn

### Làm cho nó cá nhân hóa

**Tùy chỉnh các giá trị này để workshop trở thành của bạn**:

- **Tên user và emails**: Sử dụng thông tin của riêng bạn
- **Tên products**: Tạo products bạn thấy thú vị
- **Addresses**: Sử dụng city/state của bạn
- **Prices**: Giá trị thực tế cho products của bạn

### Thêm nhiều Items

**Xem xét tạo thêm**:
- Nhiều users (tên bạn bè, gia đình)
- Nhiều products (các categories khác)
- Additional orders
- Multiple items per order

## Sẵn sàng cho Querying

Bây giờ bạn đã có một dataset e-commerce hoàn chỉnh với Single Table Design patterns phù hợp, bạn đã sẵn sàng khám phá các query patterns khác nhau. Trong phần tiếp theo, chúng ta sẽ học cách retrieve dữ liệu này một cách hiệu quả sử dụng các query techniques khác nhau.

{{% notice success %}}
**Data Creation Complete**: Bạn đã thành công implement Single Table Design với proper entity relationships, composite keys, và GSI patterns!
{{% /notice %}}
