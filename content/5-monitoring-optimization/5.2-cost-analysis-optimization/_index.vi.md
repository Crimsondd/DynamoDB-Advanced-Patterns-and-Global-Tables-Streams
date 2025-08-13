---
title : "5.2 Phân Tích Chi Phí & Tối Ưu Hóa"
date : "2025-08-11"
weight : 53
chapter : false
pre : " <b> 5.2 </b> "
---

# Phân Tích Chi Phí & Giám Sát Free Tier

💰 **Giám sát việc sử dụng Free Tier của AWS để tránh các chi phí không mong muốn**

## Tổng Quan

Học cách theo dõi chi phí DynamoDB và duy trì trong giới hạn Free Tier trong suốt workshop.

## Giới Hạn Free Tier

| Dịch Vụ            | Giới Hạn Free Tier | Sử Dụng Trong Workshop | Trạng Thái |
|--------------------|--------------------|-------------------------|------------|
| **DynamoDB Đọc**   | 25 RCU/tháng      | ~5-10 RCU              | ✅ An Toàn |
| **DynamoDB Ghi**   | 25 WCU/tháng      | ~5-10 WCU              | ✅ An Toàn |
| **DynamoDB Lưu Trữ** | 25 GB             | <1 GB                  | ✅ An Toàn |
| **Lambda Requests**| 1M/tháng          | <1,000                 | ✅ An Toàn |
| **CloudWatch Metrics** | 10 custom        | 5-8 sử dụng            | ✅ An Toàn |

## Bài Tập 1: Kiểm Tra Chi Phí Hiện Tại

### Bước 1: Truy Cập Bảng Điều Khiển Billing

1. **AWS Console**: Nhấp vào tên tài khoản (góc trên bên phải)
2. **Billing and Cost Management**: Chọn từ menu thả xuống
3. **Cost Explorer**: Nhấp vào "Cost Explorer" trong thanh bên trái
4. **Kết quả mong đợi**: $0.00 cho tất cả các dịch vụ

### Bước 2: Xem Sử Dụng Free Tier

1. **Billing console**: Nhấp vào tab "Free Tier"
2. **Kiểm tra sử dụng**:
   - **DynamoDB**: Nên hiển thị phần trăm sử dụng thấp
   - **Lambda**: Ít lần gọi
   - **CloudWatch**: Sử dụng các metric cơ bản

## Bài Tập 2: Thiết Lập Cảnh Báo Billing

### Bước 1: Bật Cảnh Báo Billing

1. **Billing preferences**: Điều hướng đến phần cài đặt
2. **Billing alerts**: Bật "Nhận Cảnh Báo Sử Dụng Free Tier"
3. **Email**: Xác nhận email nhận thông báo
4. **Ngưỡng**: Đặt cảnh báo ở mức 80% giới hạn Free Tier

### Bước 2: Tạo Ngân Sách Chi Phí

1. **AWS Budgets**: Điều hướng đến dịch vụ Budgets
2. **Tạo ngân sách**:
   - **Loại ngân sách**: Ngân sách chi phí
   - **Số tiền**: $1.00 (dự phòng an toàn)
   - **Chu kỳ**: Hàng tháng
   - **Cảnh báo tại**: $0.80 (ngưỡng 80%)

{{% notice warning %}}
**Quan Trọng**: Workshop này được thiết kế để duy trì trong Free Tier. Bất kỳ chi phí nào phát sinh đều chỉ ra việc sử dụng không mong muốn.
{{% /notice %}}

## Mẹo Tối Ưu Hóa Chi Phí

### Duy Trì Chi Phí Bằng Không

- **Giám sát sử dụng**: Kiểm tra bảng điều khiển Free Tier hàng tuần
- **Dọn dẹp tài nguyên**: Xóa tài nguyên khi hoàn thành workshop
- **Tránh cấp phát quá mức**: Tuân theo cấu hình workshop
- **Đặt cảnh báo**: Nhận thông báo trước khi phát sinh chi phí

### Dọn Dẹp Tài Nguyên Workshop

**Khi hoàn thành workshop**:

1. **Xóa bảng DynamoDB**: Loại bỏ chi phí lưu trữ
2. **Xóa hàm Lambda**: Loại bỏ chi phí tính toán  
3. **Xóa bảng điều khiển CloudWatch**: Loại bỏ chi phí giám sát
4. **Xóa vai trò IAM**: Dọn dẹp quyền

{{% notice success %}}
**Kiểm Soát Chi Phí**: Tuân theo hướng dẫn workshop giúp bạn duy trì trong giới hạn Free Tier.
{{% /notice %}}

## Xử Lý Sự Cố Chi Phí

### Nếu Bạn Thấy Chi Phí Phát Sinh

1. **Kiểm tra vùng**: Đảm bảo sử dụng đúng vùng AWS
2. **Xem lại tài nguyên**: Tìm kiếm tài nguyên không mong muốn
3. **Liên hệ hỗ trợ**: Hỗ trợ Free Tier của AWS sẵn sàng
4. **Dừng tài nguyên**: Ngay lập tức dừng bất kỳ tài nguyên nào có thể tính phí

### Các Vấn Đề Chi Phí Thường Gặp

- **Sai vùng**: Tài nguyên ở các vùng không thuộc Free Tier
- **Cấp phát quá mức**: Công suất vượt giới hạn Free Tier
- **Tài nguyên bị quên**: Tài nguyên còn chạy sau workshop
- **Chuyển dữ liệu**: Chi phí di chuyển dữ liệu giữa các vùng

## Bước Tiếp Theo

Với việc giám sát chi phí đã được thiết lập, bạn có thể tiếp tục workshop với sự tự tin rằng bạn sẽ duy trì trong giới hạn Free Tier.
