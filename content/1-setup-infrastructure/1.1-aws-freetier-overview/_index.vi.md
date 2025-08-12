---
title : "1.1 Tổng quan AWS Free Tier"
date : "2025-08-11"
weight : 11
chapter : false
pre : " <b> 1.1 </b> "
---

# Tổng quan AWS Free Tier

🆓 **Hiểu giới hạn AWS Free Tier và đảm bảo trải nghiệm workshop không mất phí**

## AWS Free Tier là gì?

AWS Free Tier cung cấp cho khách hàng mới quyền truy cập vào các dịch vụ AWS miễn phí trong thời gian giới hạn. Nó bao gồm ba loại gói:

### 1. Miễn phí 12 Tháng
Có sẵn trong 12 tháng sau ngày đăng ký AWS của bạn:
- **Amazon DynamoDB**: 25 GB storage, 25 WCU, 25 RCU
- **AWS Lambda**: 1 triệu requests mỗi tháng
- **Amazon CloudWatch**: 10 metrics, 10 alarms, 1 triệu API requests

### 2. Luôn Miễn phí
Có sẵn cho tất cả khách hàng AWS vô thời hạn:
- **DynamoDB**: 25 GB storage (luôn miễn phí)
- **Lambda**: 1 triệu requests, 400,000 GB-seconds compute time
- **CloudWatch**: 10 custom metrics và 10 alarms

### 3. Trials
Truy cập miễn phí ngắn hạn vào một số dịch vụ nhất định

## Giới hạn Free Tier cho Workshop này

```text
Phân tích Sử dụng Dịch vụ:
┌─────────────────┬──────────────┬──────────────┬─────────────┐
│ Dịch vụ         │ Free Tier    │ Sử dụng      │ An toàn %   │
├─────────────────┼──────────────┼──────────────┼─────────────┤
│ DynamoDB RCU    │ 25 units     │ 15 units     │ 60%         │
│ DynamoDB WCU    │ 25 units     │ 15 units     │ 60%         │
│ DynamoDB Storage│ 25 GB        │ <1 GB        │ 4%          │
│ Lambda Requests │ 1M/tháng     │ ~100/ngày    │ 0.3%        │
│ Lambda Duration │ 400K GB-sec  │ <1K GB-sec   │ 0.25%       │
│ CloudWatch      │ 10 metrics   │ 6 metrics    │ 60%         │
│ Data Transfer   │ 1 GB         │ <100 MB      │ 10%         │
└─────────────────┴──────────────┴──────────────┴─────────────┘

Tổng Sử dụng Free Tier: ~50% = 100% AN TOÀN! 🛡️
```

{{% notice tip %}}
**Bảo vệ Chi phí**: Chúng ta chỉ sử dụng 50% giới hạn Free Tier có sẵn, đảm bảo không có phí phát sinh trong suốt workshop.
{{% /notice %}}

## Cân nhắc về Vùng

### Vùng Chính: US East (N. Virginia)
- **Tại sao được chọn**: Giới hạn Free Tier cao nhất
- **DynamoDB**: Đầy đủ 25 RCU/WCU có sẵn
- **Lambda**: Đầy đủ 1M requests có sẵn
- **Tốt nhất cho**: Workloads chính và testing

### Vùng Phụ: EU West (Ireland)
- **Mục đích**: Sao chép Global Tables
- **Free Tier**: Giới hạn giống vùng chính
- **Sử dụng**: Tối thiểu (chỉ replica table)
- **Tác động chi phí**: Gần như bằng không

## Giám sát Sử dụng Free Tier

### AWS Billing Dashboard
1. **Điều hướng đến**: AWS Console → Billing & Cost Management
2. **Free Tier usage**: Theo dõi tiêu thụ hiện tại
3. **Alerts**: Thiết lập khi tiến gần 80% giới hạn
4. **Forecasting**: Dự đoán sử dụng hàng tháng

### CloudWatch Billing Alarms
Cảnh báo tự động khi:
- DynamoDB consumed units > 20 (80% giới hạn)
- Lambda invocations > 800K/tháng (80% giới hạn)
- Tổng ước tính phí > $1.00

## Thực hành Tốt nhất Free Tier

### 1. Giám sát Thường xuyên
- Kiểm tra Free Tier dashboard hàng ngày trong workshop
- Thiết lập cảnh báo billing trước khi triển khai
- Giám sát sử dụng tài nguyên trong CloudWatch

### 2. Sử dụng Capacity Thích hợp
```text
DynamoDB Provisioned Capacity:
- Read Capacity Units (RCU): 5 (dưới giới hạn 25)
- Write Capacity Units (WCU): 5 (dưới giới hạn 25)
- On-Demand: KHÔNG khuyến nghị (có thể vượt Free Tier)
```

### 3. Dọn dẹp Tài nguyên
- Xóa test data thường xuyên
- Loại bỏ Lambda functions không sử dụng
- Dọn dẹp CloudWatch logs cũ hơn 7 ngày

## Những Bẫy Free Tier Thường gặp cần Tránh

{{% notice warning %}}
**Tránh Những Lỗi Này**:
1. **On-Demand DynamoDB**: Có thể nhanh chóng vượt Free Tier
2. **Nhiều Vùng**: Triển khai cùng tài nguyên trong >2 vùng
3. **Tập Dữ liệu Lớn**: Upload >25GB vào DynamoDB
4. **Quên Dọn dẹp**: Để tài nguyên chạy quá workshop
{{% /notice %}}

### Lỗi #1: Provisioned vs On-Demand
```text
❌ SAI: DynamoDB On-Demand
- Trả tiền theo request (có thể đắt)
- Chi phí không dự đoán được
- Không có bảo vệ Free Tier

✅ ĐÚNG: DynamoDB Provisioned
- Capacity units cố định
- Chi phí dự đoán được
- Được bảo vệ bởi giới hạn Free Tier
```

### Lỗi #2: Nhân Tài nguyên
```text
❌ SAI: Triển khai đến 5 vùng
- 5x tiêu thụ tài nguyên
- 5x sử dụng Free Tier
- Có khả năng vượt giới hạn

✅ ĐÚNG: Triển khai tối đa 2 vùng
- Sử dụng tài nguyên tối thiểu
- Trong giới hạn Free Tier
- Duy trì tính khả dụng toàn cầu
```

## Quy trình Khẩn cấp

### Nếu Bạn Thấy Phí Xuất hiện

1. **DỪNG NGAY LẬP TỨC**: Tạm dừng tất cả hoạt động workshop
2. **Kiểm tra Billing Dashboard**: Xác định nguồn phí
3. **Xem lại Tài nguyên**: Liệt kê tất cả tài nguyên đang hoạt động
4. **Liên hệ Support**: Sử dụng hỗ trợ AWS Free Tier nếu cần
5. **Xóa Tài nguyên**: Loại bỏ bất cứ thứ gì ngoài phạm vi workshop

### Lệnh Audit Tài nguyên Nhanh

```bash
# Liệt kê tất cả DynamoDB tables
aws dynamodb list-tables --region us-east-1

# Liệt kê tất cả Lambda functions  
aws lambda list-functions --region us-east-1

# Kiểm tra CloudFormation stacks
aws cloudformation list-stacks --region us-east-1
```

## Checklist Trước Workshop

Trước khi bắt đầu triển khai hạ tầng:

- [ ] Tài khoản AWS được tạo và xác minh
- [ ] Tính đủ điều kiện Free Tier được xác nhận (tài khoản <12 tháng tuổi)
- [ ] Cảnh báo billing được cấu hình
- [ ] Free Tier dashboard được bookmark
- [ ] Thông tin liên hệ khẩn cấp sẵn sàng
- [ ] Hiểu biết về giới hạn tài nguyên được xác nhận

{{% notice info %}}
**Sẵn sàng Tiếp tục**: Sau khi bạn đã xem xét giới hạn Free Tier và hiểu các biện pháp an toàn, hãy tiến hành Tổng quan Kiến trúc để hiểu những gì chúng ta sẽ xây dựng.
{{% /notice %}}