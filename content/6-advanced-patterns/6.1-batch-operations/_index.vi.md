---
title : "6.1 Thao Tác Hàng Loạt"
date : "2025-08-11"
weight : 61
chapter : false
pre : " <b> 6.1 </b> "
---

# Thao Tác Hàng Loạt

⚡ **Xử lý nhiều mục một cách hiệu quả với thao tác hàng loạt**

## Tổng Quan

Thao tác hàng loạt cho phép bạn xử lý nhiều mục trong một lần gọi API, giảm độ trễ và cải thiện hiệu suất.

### Lợi Ích Chính

- **BatchWriteItem**: Thêm hoặc xóa tối đa 25 mục cùng lúc
- **BatchGetItem**: Lấy tối đa 100 mục cùng lúc
- **Giảm số lần gọi API**: Hiệu suất và hiệu quả tốt hơn
- **Giảm độ trễ**: Xử lý hàng loạt giảm số lần truyền tải

## Bài Tập 1: Ghi Hàng Loạt Đơn Giản

### Bước 1: Tạo Dữ Liệu Sản Phẩm Hàng Loạt

**Sử dụng AWS CloudShell cho thao tác hàng loạt**:

1. **Mở CloudShell**: Từ AWS Console
2. **Tạo tệp hàng loạt**: Tạo JSON cho nhiều sản phẩm

![6.1.1](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.1.1.png?featherlight=false&width=90pc)

```bash
cat > batch-products.json << 'EOF'
{
  "demo-ecommerce-freetier": [
    {
      "PutRequest": {
        "Item": {
          "PK": {"S": "PRODUCT#gaming-laptop"},
          "SK": {"S": "DETAILS"},
          "GSI1PK": {"S": "CATEGORY#electronics"},
          "GSI1SK": {"S": "PRODUCT#gaming-laptop"},
          "name": {"S": "Gaming Laptop Pro"},
          "price": {"N": "1299"},
          "stock": {"N": "15"},
          "category": {"S": "electronics"}
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "PK": {"S": "PRODUCT#wireless-mouse"},
          "SK": {"S": "DETAILS"},
          "GSI1PK": {"S": "CATEGORY#electronics"},
          "GSI1SK": {"S": "PRODUCT#wireless-mouse"},
          "name": {"S": "Wireless Gaming Mouse"},
          "price": {"N": "79"},
          "stock": {"N": "50"},
          "category": {"S": "electronics"}
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "PK": {"S": "PRODUCT#rgb-keyboard"},
          "SK": {"S": "DETAILS"},
          "GSI1PK": {"S": "CATEGORY#electronics"},
          "GSI1SK": {"S": "PRODUCT#rgb-keyboard"},
          "name": {"S": "RGB Mechanical Keyboard"},
          "price": {"N": "159"},
          "stock": {"N": "25"},
          "category": {"S": "electronics"}
        }
      }
    }
  ]
}
EOF
```

### Bước 2: Thực Hiện Ghi Hàng Loạt

**Thực hiện thao tác ghi hàng loạt**:

```bash
aws dynamodb batch-write-item --request-items file://batch-products.json
```

**Kết quả mong đợi**: Tất cả 3 mục được tạo trong một lần gọi API thay vì 3 lần riêng lẻ.

![6.1.2](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.1.2.png?featherlight=false&width=90pc)

### Bước 3: Xác Minh Kết Quả

**Kiểm tra DynamoDB Console**:

1. Điều hướng đến các mục trong bảng của bạn
2. Tìm 3 sản phẩm mới:
   - `PRODUCT#gaming-laptop`
   - `PRODUCT#wireless-mouse`
   - `PRODUCT#rgb-keyboard`

![6.1.3](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.1.3.png?featherlight=false&width=90pc)

## Bài Tập 2: Đọc Hàng Loạt

### Bước 1: Lấy Nhiều Mục

**Lấy nhiều mục cùng lúc**:

```bash
cat > batch-get.json << 'EOF'
{
  "demo-ecommerce-freetier": {
    "Keys": [
      {
        "PK": {"S": "PRODUCT#gaming-laptop"},
        "SK": {"S": "DETAILS"}
      },
      {
        "PK": {"S": "PRODUCT#wireless-mouse"},
        "SK": {"S": "DETAILS"}
      },
      {
        "PK": {"S": "PRODUCT#rgb-keyboard"},
        "SK": {"S": "DETAILS"}
      }
    ]
  }
}
EOF
```

### Bước 2: Thực Hiện Đọc Hàng Loạt

```bash
aws dynamodb batch-get-item --request-items file://batch-get.json
```

![6.1.4](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.1.4.png?featherlight=false&width=90pc)

**So sánh hiệu suất**:

- **Thao tác hàng loạt**: 1 lần gọi API cho 3 mục
- **Thao tác riêng lẻ**: 3 lần gọi API cho 3 mục
- **Kết quả**: Giảm 3 lần gọi API = hiệu suất tốt hơn

## Lợi Ích Của Thao Tác Hàng Loạt

### Tăng Hiệu Quả

| Loại Thao Tác | Gọi API Riêng Lẻ | Gọi API Hàng Loạt | Hiệu Quả |
|---------------|------------------|-------------------|----------|
| **Ghi 25 mục** | 25 lần gọi API  | 1 lần gọi API     | Nhanh hơn 25 lần |
| **Đọc 100 mục** | 100 lần gọi API | 1 lần gọi API     | Nhanh hơn 100 lần |
| **Thao tác hỗn hợp** | Nhiều lần gọi | 1 lần gọi API     | Nhanh hơn nhiều |

### Lợi Ích Free Tier

- **Cùng mức sử dụng RCU/WCU**: Dung lượng tiêu thụ không đổi
- **Giảm số lần gọi API**: Tăng thông lượng trong giới hạn
- **Giảm độ trễ**: Phản hồi ứng dụng nhanh hơn
- **Xử lý hiệu quả**: Hoàn thành nhiều công việc hơn mỗi lần yêu cầu

## Thực Hành Tốt Nhất

### Giới Hạn Hàng Loạt

- **BatchWriteItem**: Tối đa 25 mục mỗi yêu cầu
- **BatchGetItem**: Tối đa 100 mục mỗi yêu cầu
- **Kích thước mục**: Mỗi mục vẫn tính vào giới hạn kích thước
- **Xử lý lỗi**: Một số mục có thể thất bại trong khi các mục khác thành công

### Khi Nào Nên Sử Dụng Hàng Loạt

**Thích hợp cho hàng loạt**:
- Nhiều mục liên quan cần xử lý
- Nhập/xuất dữ liệu hàng loạt
- Các thao tác khởi tạo hoặc dọn dẹp

**Không lý tưởng cho hàng loạt**:
- Thao tác một mục
- Các mục cần xử lý lỗi riêng lẻ
- Tương tác người dùng theo thời gian thực

{{% notice success %}}
**Thành Thạo Thao Tác Hàng Loạt**: Bây giờ bạn có thể xử lý nhiều mục một cách hiệu quả, giảm số lần gọi API và cải thiện hiệu suất ứng dụng!
{{% /notice %}}

## Bước Tiếp Theo

Tiếp tục học các mẫu DynamoDB nâng cao hoặc chuyển sang module workshop tiếp theo.
