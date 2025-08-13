---
title : "3. Global Tables Multi-Region Setup"
date : "2025-08-11"
weight : 3 
chapter : true
pre : " <b> 3. </b> "
---

# Global Tables Multi-Region Setup

🌍 **Thiết lập DynamoDB đa vùng cho truy cập toàn cầu**

## Tổng quan

Global Tables biến đổi DynamoDB table đơn vùng của bạn thành cơ sở dữ liệu phân tán toàn cầu phục vụ người dùng trên toàn thế giới với độ trễ thấp.

## Tại sao Global Tables?

![BEFOREGB](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/BEFOREGB.png?featherlight=false&width=50pc)

### Vấn đề
- **Độ trễ cao**: Người dùng xa database phải chờ lâu
- **Không có Disaster Recovery**: Điểm lỗi đơn
- **Khả năng mở rộng hạn chế**: Tất cả traffic qua một vùng

### Giải pháp
```text
Trước: Chỉ US-EAST-1 → Độ trễ cao cho người dùng EU
Sau:   US-EAST-1 + EU-WEST-1 → Độ trễ thấp toàn cầu
```

## Bạn sẽ học gì

- **Xác minh Global Setup**: Kiểm tra cấu hình đa vùng
- **Test Replication**: Ghi ở vùng này, đọc ở vùng khác
- **Multi-Region Operations**: Xử lý dữ liệu toàn cầu

## Lợi ích chính

- **Độ trễ dưới 10ms** cho người dùng toàn cầu
- **Sao chép tự động** giữa các vùng (0.5-2 giây)
- **Free Tier friendly**: Áp dụng cho mỗi vùng
- **Disaster recovery tích hợp**

## Cơ bản về Global Tables

### Luồng sao chép
```text
1. Ghi vào US-EAST-1 → ORDER#12345 được tạo
2. DynamoDB Streams ghi nhận thay đổi
3. Tự động sao chép sang EU-WEST-1
4. ORDER#12345 có sẵn ở Europe (1-2 giây)
```

### Tính năng chính
- **Hai chiều**: Đọc/ghi từ bất kỳ vùng nào
- **Eventually Consistent**: Thay đổi đồng bộ trong vài giây
- **Conflict Resolution**: Last Writer Wins
- **Zero downtime**: Chuyển đổi vùng tự động

## Nội dung Module

1. **[Global Tables Overview](3.1-global-tables-overview/)** - Hiểu kiến trúc
2. **[Verify Global Setup](3.2-verify-global-setup/)** - Kiểm tra cấu hình
3. **[Multi-Region Operations](3.3-multi-region-operations/)** - Test chức năng đa vùng

{{% notice info %}}
**Thiết lập**: CloudFormation deployment của bạn đã cấu hình Global Tables giữa US-East-1 và EU-West-1.
{{% /notice %}}

{{% children %}}

Hãy làm cho DynamoDB table của bạn có thể truy cập toàn cầu!
