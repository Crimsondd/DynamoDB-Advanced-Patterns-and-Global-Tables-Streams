---
title : "2.1 Nguyên tắc Thiết kế"
date : "2025-08-11"
weight : 21
chapter : false
pre : " <b> 2.1 </b> "
---

# Nguyên tắc Single Table Design

🎯 **Hiểu các khái niệm cốt lõi làm cho Single Table Design mạnh mẽ**

## Sự Chuyển đổi Mô hình

### Từ Tư duy Quan hệ sang NoSQL

Cơ sở dữ liệu quan hệ truyền thống tổ chức dữ liệu theo **entities** (các bảng riêng biệt cho Users, Products, Orders). DynamoDB tổ chức dữ liệu theo **access patterns** (cách bạn sẽ truy vấn dữ liệu).

**Phương pháp Quan hệ**:

| Bảng | Trường |
|---|---|
| **Users** | user_id, name, email |
| **Products** | product_id, name, category |
| **Orders** | order_id, user_id, status |

**Phương pháp Single Table**:

| PK | SK | Entity | Additional Data |
|---|---|---|---|
| **USER#user1** | **PROFILE** | User | name, email, phone |
| **USER#user1** | **ORDER#ord1** | Order | status, total, date |
| **PRODUCT#p1** | **DETAILS** | Product | name, price, stock |
| **ORDER#ord1** | **ITEM#p1** | OrderItem | quantity, price |

## Nguyên tắc Cốt lõi

### 1. Chiến lược Composite Primary Key

**Partition Key (PK) + Sort Key (SK)** tạo identification duy nhất cho item và cho phép relationships:

- **PK**: Nhóm các items liên quan với nhau (như một namespace)
- **SK**: Sắp xếp items trong một partition và tạo hierarchies
- **Cùng nhau**: Cho phép relationships 1-to-1, 1-to-many, và many-to-many

### 2. Entity Namespacing

Sử dụng prefixes để tạo separation logic:

| Loại Entity | PK Pattern | SK Pattern | Mục đích |
|-------------|------------|------------|---------|
| **User Profile** | `USER#user123` | `PROFILE` | Lưu trữ thông tin user |
| **User Orders** | `USER#user123` | `ORDER#order456` | Liên kết orders với users |
| **Product Details** | `PRODUCT#prod789` | `DETAILS` | Lưu trữ thông tin product |
| **Order Items** | `ORDER#order456` | `ITEM#prod789` | Liên kết products với orders |

### 3. Thiết kế Access Pattern First

**Bắt đầu với các câu hỏi**, sau đó thiết kế cấu trúc key:

1. "Làm sao tôi lấy user profile?" → `PK=USER#id, SK=PROFILE`
2. "Làm sao tôi lấy orders của user?" → `PK=USER#id, SK begins_with ORDER#`
3. "Làm sao tôi lấy chi tiết order?" → `PK=ORDER#id, SK begins_with ITEM#`
4. "Làm sao tôi lấy products theo category?" → Sử dụng GSI với `CATEGORY#` keys

## Chiến lược Global Secondary Index (GSI)

### Khi nào Sử dụng GSIs

Sử dụng GSIs khi bạn cần truy vấn dữ liệu theo attributes khác với primary key:

- **Nhóm khác**: Products theo category thay vì theo product ID
- **Sắp xếp khác**: Orders theo status thay vì theo user
- **Cross-entity queries**: Tất cả pending orders qua tất cả users

### Thiết kế GSI Key

**GSI1** - Category-based queries:

| GSI1PK | GSI1SK |
|---|---|
| **CATEGORY#electronics** | **PRODUCT#prod1** |
| **CATEGORY#electronics** | **PRODUCT#prod2** |
| **CATEGORY#books** | **PRODUCT#prod3** |

**GSI2** - Status/Price-based queries:

| GSI2PK | GSI2SK |
|---|---|
| **STATUS#pending** | **ORDER#order1** |
| **STATUS#shipped** | **ORDER#order2** |
| **PRICE#100-500** | **PRODUCT#prod1** |

## Lợi ích trong Thực tế

### Lợi ích Performance

- **Single Query**: Lấy user profile + tất cả orders trong một query
- **Độ trễ Dự đoán được**: Thời gian phản hồi single-digit millisecond
- **Không JOINs**: Tất cả dữ liệu liên quan được retrieved cùng nhau
- **Scaling Hiệu quả**: Performance nhất quán ở bất kỳ scale nào

### Lợi ích Chi phí

- **Ít Bảng hơn**: Chi phí DynamoDB table thấp hơn
- **Ít Operations hơn**: Batch queries thay vì multiple calls
- **Capacity Tối ưu**: Utilization tốt hơn của provisioned capacity
- **Giảm Data Transfer**: Ít network overhead hơn

### Lợi ích Operational

- **Atomic Transactions**: Cập nhật các items liên quan cùng nhau
- **Backup Đơn giản**: Một bảng để backup/restore
- **Monitoring Dễ hơn**: Single table metrics để track
- **Bảo mật Nhất quán**: Một set IAM policies

## Mẫu Thiết kế Chính

### 1. Adjacency List Pattern
Lưu trữ các items liên quan cạnh nhau:
```text
PK=USER#user1, SK=PROFILE        (Chi tiết User)
PK=USER#user1, SK=ORDER#order1   (Order 1)
PK=USER#user1, SK=ORDER#order2   (Order 2)
```

### 2. Hierarchical Data Pattern
Sử dụng sort key để biểu diễn hierarchy:
```text
PK=ORDER#order1, SK=DETAILS      (Order header)
PK=ORDER#order1, SK=ITEM#prod1   (Order item 1)
PK=ORDER#order1, SK=ITEM#prod2   (Order item 2)
```

### 3. GSI Overloading Pattern
Sử dụng cùng GSI cho nhiều query patterns:
```text
GSI1PK=CATEGORY#electronics, GSI1SK=PRODUCT#prod1
GSI1PK=USER#user1@email.com, GSI1SK=PROFILE
```

{{% notice tip %}}
**Quy tắc Thiết kế**: Luôn bắt đầu với access patterns của bạn, sau đó thiết kế cấu trúc key. Đừng bắt đầu với entities!
{{% /notice %}}

## Anti-Patterns Thường gặp cần Tránh

### ❌ Đừng Sử dụng Scan Operations
- **Sai**: Scan toàn bộ table để tìm items
- **Đúng**: Sử dụng Query với cấu trúc key phù hợp

### ❌ Đừng Tạo Quá nhiều GSIs
- **Sai**: Một GSI cho mỗi query pattern
- **Đúng**: Overload GSIs cho nhiều patterns

### ❌ Đừng Bỏ qua Hot Partitions
- **Sai**: Tất cả items có cùng partition key
- **Đúng**: Phân phối items qua nhiều partitions

### ❌ Đừng Sử dụng Relational Patterns
- **Sai**: Normalize dữ liệu qua nhiều items
- **Đúng**: Denormalize dữ liệu liên quan cùng nhau

{{% notice warning %}}
**Nhớ rằng**: Single Table Design yêu cầu mindset shift. Hãy suy nghĩ theo access patterns, không phải entity relationships!
{{% /notice %}}

## Sẵn sàng cho Implementation

Bây giờ bạn đã hiểu các nguyên tắc, hãy chuyển đến AWS Console để xem những khái niệm này trong thực tế. Trong phần tiếp theo, chúng ta sẽ điều hướng DynamoDB Console và bắt đầu tạo e-commerce data model của chúng ta.
