---
title : "6.2 Cập Nhật Có Điều Kiện"
date : "2025-08-11"
weight : 62
chapter : false
pre : " <b> 6.2 </b> "
---

# Cập Nhật Có Điều Kiện

🛡️ **Ngăn chặn điều kiện tranh chấp với cập nhật có điều kiện**

## Tổng Quan

Cập nhật có điều kiện đảm bảo tính toàn vẹn dữ liệu bằng cách chỉ cập nhật các mục khi các điều kiện cụ thể được đáp ứng. Điều này ngăn chặn các điều kiện tranh chấp trong các ứng dụng nhiều người dùng.

### Vấn Đề: Điều Kiện Tranh Chấp

**Không có điều kiện (nguy hiểm)**:
```text
Người dùng A đọc: stock = 1
Người dùng B đọc: stock = 1  
Người dùng A cập nhật: stock = 0  ✅
Người dùng B cập nhật: stock = 0  ❌ (Nên thất bại!)
Kết quả: Bán quá mức!
```

**Có điều kiện (an toàn)**:
```text
Người dùng A: stock = 0 (điều kiện: stock >= 1) ✅
Người dùng B: thất bại (điều kiện: stock >= 1) ❌
Kết quả: Chỉ người đầu tiên thành công ✅
```

## Bài Tập 1: Cập Nhật Có Điều Kiện Cơ Bản

### Bước 1: Tạo Sản Phẩm Thử Nghiệm

**Tạo một sản phẩm để thử nghiệm**:

```bash
aws dynamodb put-item \
  --table-name demo-ecommerce-freetier \
  --item '{
    "PK": {"S": "PRODUCT#conditional-test"},
    "SK": {"S": "DETAILS"},
    "name": {"S": "Test Product"},
    "price": {"N": "100"},
    "stock": {"N": "5"}
  }'
```

![6.2.1](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.1.png?featherlight=false&width=90pc)
![6.2.1(1)](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.1(1).png?featherlight=false&width=90pc)
### Bước 2: Cập Nhật Giá An Toàn

**Cập nhật giá với điều kiện**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET price = :new_price" \
  --condition-expression "price = :current_price" \
  --expression-attribute-values '{
    ":new_price": {"N": "90"},
    ":current_price": {"N": "100"}
  }'
```

![6.2.2](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.2.png?featherlight=false&width=90pc)

### Bước 3: Cập Nhật Có Điều Kiện Thất Bại

**Thử cập nhật với điều kiện sai**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET price = :new_price" \
  --condition-expression "price = :wrong_price" \
  --expression-attribute-values '{
    ":new_price": {"N": "80"},
    ":wrong_price": {"N": "100"}
  }'
```

**Kết quả**: Lỗi ConditionalCheckFailedException

![6.2.3](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.3.png?featherlight=false&width=90pc)

## Bài Tập 2: Ngăn Chặn Bán Quá Mức

### Bước 1: Giảm Số Lượng Hàng An Toàn

**Mua 2 sản phẩm một cách an toàn**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET stock = stock - :quantity" \
  --condition-expression "stock >= :quantity" \
  --expression-attribute-values '{
    ":quantity": {"N": "2"}
  }'
```

![6.2.4](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.4.png?featherlight=false&width=90pc)

### Bước 2: Ngăn Chặn Bán Quá Mức

**Thử mua nhiều hơn số lượng có sẵn**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET stock = stock - :quantity" \
  --condition-expression "stock >= :quantity" \
  --expression-attribute-values '{
    ":quantity": {"N": "5"}
  }'
```

**Kết quả**: Thất bại vì chỉ còn 3 sản phẩm trong kho

![6.2.5](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.5.png?featherlight=false&width=90pc)

## Bài Tập 3: Kiểm Soát Phiên Bản

### Bước 1: Thêm Phiên Bản Vào Sản Phẩm

**Cập nhật sản phẩm với số phiên bản**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET version = :version" \
  --expression-attribute-values '{
    ":version": {"N": "1"}
  }'
```

### Bước 2: Cập Nhật Dựa Trên Phiên Bản

**Cập nhật với kiểm tra phiên bản**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET price = :new_price, version = version + :inc" \
  --condition-expression "version = :expected_version" \
  --expression-attribute-values '{
    ":new_price": {"N": "85"},
    ":inc": {"N": "1"},
    ":expected_version": {"N": "1"}
  }'
```

![6.2.6](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.6.png?featherlight=false&width=90pc)

### Bước 3: Xung Đột Phiên Bản

**Thử cập nhật với phiên bản cũ**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET price = :new_price, version = version + :inc" \
  --condition-expression "version = :old_version" \
  --expression-attribute-values '{
    ":new_price": {"N": "75"},
    ":inc": {"N": "1"},
    ":old_version": {"N": "1"}
  }'
```

**Kết quả**: Thất bại vì phiên bản hiện tại là 2, không phải 1

![6.2.7](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.7.png?featherlight=false&width=90pc)

## Bài Tập 4: Ngăn Chặn Trùng Lặp

### Bước 1: Tạo Người Dùng Duy Nhất

**Tạo người dùng chỉ khi chưa tồn tại**:

```bash
aws dynamodb put-item \
  --table-name demo-ecommerce-freetier \
  --item '{
    "PK": {"S": "USER#unique-user"},
    "SK": {"S": "PROFILE"},
    "email": {"S": "user@example.com"}
  }' \
  --condition-expression "attribute_not_exists(PK)"
```

### Bước 2: Thử Trùng Lặp

**Thử tạo cùng người dùng lần nữa**:

```bash
aws dynamodb put-item \
  --table-name demo-ecommerce-freetier \
  --item '{
    "PK": {"S": "USER#unique-user"},
    "SK": {"S": "PROFILE"}, 
    "email": {"S": "different@example.com"}
  }' \
  --condition-expression "attribute_not_exists(PK)"
```

**Kết quả**: Thất bại - ngăn chặn người dùng trùng lặp

![6.2.8](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.8.png?featherlight=false&width=90pc)

### Bước 3: Điều Kiện Phức Tạp

**Cập nhật với nhiều điều kiện**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET featured = :featured" \
  --condition-expression "contains(#n, :keyword)" \
  --expression-attribute-names '{"#n": "name"}' \
  --expression-attribute-values '{
    ":featured": {"BOOL": true},
    ":keyword": {"S": "Test"}
  }'
```

![6.2.9](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.9.png?featherlight=false&width=90pc)

## Giám Sát Cập Nhật Có Điều Kiện

**Kiểm tra các chỉ số CloudWatch**:

1. **ConditionalCheckFailedException**: Tỷ lệ điều kiện thất bại
2. **SuccessfulRequestLatency**: Hiệu suất cập nhật  
3. **ThrottledRequests**: Vấn đề dung lượng

**Vị trí ảnh chụp màn hình**: Thêm ảnh chụp màn hình CloudWatch hiển thị các chỉ số cập nhật có điều kiện

## Lợi Ích Chính

- **Ngăn chặn điều kiện tranh chấp**: Nhiều người dùng không thể bán quá mức hàng tồn kho
- **Tính toàn vẹn dữ liệu**: Đảm bảo các quy tắc kinh doanh được thực thi
- **Kiểm soát phiên bản**: Ngăn chặn ghi đè các thay đổi đồng thời
- **Ngăn chặn trùng lặp**: Ràng buộc duy nhất mà không cần khóa cơ sở dữ liệu

{{% notice success %}}
**Thành Thạo Cập Nhật Có Điều Kiện**: Bây giờ bạn có thể xử lý an toàn truy cập đồng thời và ngăn chặn hỏng dữ liệu!
{{% /notice %}}

## Bước Tiếp Theo

Bạn đã học các mẫu cập nhật có điều kiện cần thiết cho các ứng dụng sản xuất.
