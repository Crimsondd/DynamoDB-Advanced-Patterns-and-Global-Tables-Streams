---
title : "5. Giám Sát & Tối Ưu Hóa"
date : "2025-08-11"
weight : 5 
chapter : true
pre : " <b> 5. </b> "
---

# Giám Sát & Tối Ưu Hóa

📈 **Giám sát và tối ưu hóa chi phí cho workshop DynamoDB của bạn**

## Tổng Quan Module

Học cách giám sát bảng DynamoDB của bạn và duy trì trong giới hạn Free Tier với các bảng điều khiển CloudWatch thực tế và theo dõi chi phí.

#### Những Gì Bạn Sẽ Học

- **Bảng Điều Khiển CloudWatch**: Tạo một giao diện giám sát cho bảng của bạn
- **Theo Dõi Chi Phí**: Giám sát việc sử dụng Free Tier để tránh chi phí
- **Cảnh Báo Cơ Bản**: Thiết lập các cảnh báo đơn giản cho giới hạn dung lượng

#### Các Chỉ Số Quan Trọng Cần Giám Sát

| Chỉ Số | Giới Hạn Free Tier | Cần Theo Dõi |
|--------|---------------------|---------------|
| **Dung Lượng Đọc** | 25 RCU | Giữ mức sử dụng dưới 20 RCU |
| **Dung Lượng Ghi** | 25 WCU | Giữ mức sử dụng dưới 20 WCU |
| **Lưu Trữ** | 25 GB | Theo dõi sự tăng trưởng dữ liệu |

#### Giám Sát Free Tier

**Duy trì trong giới hạn với các kiểm tra đơn giản này**:

```
✅ Bảng điều khiển hiển thị các chỉ số màu xanh
✅ Lưu trữ dưới 20 GB
✅ Sử dụng RCU/WCU dưới 80%
✅ Bảng điều khiển billing hiển thị $0.00
```

## Nội Dung Module

1. **[Bảng Điều Khiển CloudWatch](5.1-cloudwatch-dashboards/)** - Tạo giao diện giám sát
2. **[Phân Tích Chi Phí](5.3-cost-analysis-optimization/)** - Theo dõi việc sử dụng Free Tier

{{% notice info %}}
**Tập Trung**: Module này chỉ bao gồm các giám sát cần thiết cho workshop. Các mẫu nâng cao được đề cập riêng.
{{% /notice %}}

{{% children %}}

Hãy thiết lập giám sát cơ bản để giữ cho workshop của bạn hoạt động trơn tru và không tốn phí.
