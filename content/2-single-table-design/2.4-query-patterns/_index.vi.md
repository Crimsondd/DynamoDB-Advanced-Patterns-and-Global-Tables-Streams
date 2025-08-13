---
title : "2.4 Các Mẫu Truy Vấn"
date : "2025-08-11"
weight : 24
chapter : false
pre : " <b> 2.4 </b> "
---

# Các Mẫu Truy Vấn

🔍 **Học các kỹ thuật truy vấn hiệu quả sử dụng DynamoDB Console**

## Tổng Quan

Học cách truy xuất dữ liệu hiệu quả sử dụng cả truy vấn bảng và truy vấn Global Secondary Index (GSI).

## Truy Vấn vs Quét

- **Truy Vấn (Query)**: Nhanh, hiệu quả, sử dụng khóa chính ✅
- **Quét (Scan)**: Chậm, tốn kém, đọc toàn bộ bảng ❌

{{% notice warning %}}
**Quan trọng**: Luôn sử dụng các thao tác Query. Các thao tác Scan có thể vượt quá giới hạn Free Tier.
{{% /notice %}}

## Truy Cập Giao Diện Query

1. **Đi đến**: Tab Items trong bảng DynamoDB của bạn
2. **Nhấp vào**: Nút "Query" (không phải Scan)
3. **Chọn**: Truy vấn bảng hoặc truy vấn Index

## Mẫu 1: Lấy Hồ Sơ Người Dùng

**Mục tiêu**: Lấy thông tin cụ thể của người dùng

**Cấu hình Query**:
- **Khóa phân vùng (PK)**: `USER#user001`
- **Khóa sắp xếp (SK)**: `PROFILE`

**Kết quả**: Một mục chứa dữ liệu hồ sơ người dùng

## Mẫu 2: Lấy Đơn Hàng Của Người Dùng

**Mục tiêu**: Lấy tất cả đơn hàng của một người dùng

**Cấu hình Query**:
- **Khóa phân vùng (PK)**: `USER#user001`
- **Điều kiện khóa sắp xếp**: "begins_with"
- **Giá trị khóa sắp xếp**: `ORDER#`

**Kết quả**: Hồ sơ người dùng + tất cả đơn hàng của họ

## Mẫu 3: Lấy Chi Tiết Đơn Hàng

**Mục tiêu**: Lấy thông tin đầy đủ của đơn hàng

**Cấu hình Query**:
- **Khóa phân vùng (PK)**: `ORDER#order001`
- **Khóa sắp xếp**: Để trống (lấy tất cả các mục)

**Kết quả**: Chi tiết đơn hàng + tất cả các mục trong đơn hàng

## Mẫu 4: Sản Phẩm Theo Danh Mục (GSI)

**Mục tiêu**: Tìm sản phẩm trong danh mục cụ thể

**Chuyển sang Truy vấn GSI**:
1. Nhấp vào "Query (index)"
2. Chọn Index: GSI1

**Cấu hình Query**:
- **Khóa phân vùng GSI1**: `CATEGORY#electronics`
- **Khóa sắp xếp GSI1**: Để trống

**Kết quả**: Tất cả sản phẩm điện tử

## Mẫu 5: Đơn Hàng Theo Trạng Thái (GSI)

**Mục tiêu**: Tìm đơn hàng với trạng thái cụ thể

**Cấu hình Query**:
- **Chọn Index**: GSI2
- **Khóa phân vùng GSI2**: `STATUS#pending`

**Kết quả**: Tất cả đơn hàng đang chờ xử lý

## Hiệu Suất Truy Vấn

**Hiệu suất mong đợi**:
- **Một mục**: ~1-2ms, 1 RCU
- **Nhiều mục**: ~3-5ms, 2-5 RCU
- **Truy vấn GSI**: ~2-4ms, 1-3 RCU

## Thực Hành Tốt Nhất

1. **Luôn sử dụng Query** (không bao giờ Scan)
2. **Thiết kế khóa cho truy vấn** trước
3. **Sử dụng GSIs chiến lược** cho các mẫu truy vấn khác nhau
4. **Giám sát hiệu suất** và dung lượng tiêu thụ

{{% notice success %}}
**Thành Thạo Truy Vấn**: Bây giờ bạn có thể truy xuất dữ liệu hiệu quả sử dụng các mẫu thiết kế bảng đơn!
{{% /notice %}}

## Bước Tiếp Theo

Sẵn sàng cho quy mô toàn cầu! Module tiếp theo sẽ đề cập đến Global Tables cho triển khai đa vùng.
