---
title : "7. Dọn dẹp Tài nguyên"
date : "2025-08-11"
weight : 7
chapter : true
pre : " <b> 7. </b> "
---

# Dọn dẹp Tài nguyên trên AWS Cloud

Trong phần này, bạn sẽ học cách dọn dẹp tài nguyên trên AWS Cloud để tránh phát sinh chi phí không cần thiết.

## Tại sao Dọn dẹp quan trọng

- **Kiểm soát Chi phí**: Ngăn chặn các khoản phí AWS không mong muốn
- **Quản lý Tài nguyên**: Loại bỏ hạ tầng không sử dụng
- **Best Practices**: Học quản lý lifecycle tài nguyên đúng cách

**Chi phí cuối cùng dự kiến**: **$0.00** ✅

## 1. Xóa DynamoDB Table được tạo trong Lab

- Truy cập **DynamoDB Management Console**
- Trên thanh điều hướng bên trái, chọn **Tables**
- Chọn DynamoDB table `demo-ecommerce-freetier` liên quan đến lab
- Nhấp vào **Actions**
- Chọn **Delete table**
- Nhập tên table để xác nhận
- Xác nhận bằng cách nhấp **Delete**

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của quá trình xóa DynamoDB table
{{% /notice %}}

## 2. Xóa CloudWatch Resources được tạo trong Lab

- Truy cập **CloudWatch Management Console**
- Trên thanh điều hướng bên trái, đi đến **Dashboards**
- Chọn tất cả dashboards liên quan đến lab
- Nhấp vào **Actions**
- Chọn **Delete dashboards**
- Xác nhận xóa bằng cách nhấp **Delete**
- Trên thanh điều hướng bên trái, đi đến **Alarms**
- Chọn tất cả alarms liên quan đến lab
- Nhấp vào **Actions**
- Chọn **Delete**
- Xác nhận xóa bằng cách nhấp **Delete**

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của việc xóa CloudWatch dashboard và alarm
{{% /notice %}}

## 3. Xóa Lambda Functions (nếu đã tạo)

- Truy cập **Lambda Management Console**
- Trên thanh điều hướng bên trái, điều hướng đến **Functions**
- Chọn các Lambda functions liên quan đến lab
- Nhấp vào **Actions**
- Chọn **Delete function**
- Xác nhận bằng cách nhấp **Delete**

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của việc xóa Lambda function
{{% /notice %}}

## 4. Xóa SNS Topics (nếu đã tạo)

- Truy cập **SNS Management Console**
- Trên thanh điều hướng bên trái, chọn **Topics**
- Chọn tất cả SNS topics liên quan đến lab
- Nhấp vào **Actions**
- Chọn **Delete**
- Nhập "delete me" để xác nhận
- Xác nhận xóa bằng cách nhấp **Delete**

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của việc xóa SNS topic
{{% /notice %}}

## 5. Xác minh Billing và Chi phí

- Truy cập **Billing and Cost Management Console**
- Kiểm tra các khoản phí tháng hiện tại
- Xác minh rằng tất cả các khoản phí hiển thị **$0.00**
- Xác nhận không có dịch vụ nào đang chạy không mong muốn

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của billing dashboard hiển thị $0.00 charges
{{% /notice %}}

## Xác minh Cuối cùng

✅ **Checklist Dọn dẹp:**

- DynamoDB table đã xóa
- CloudWatch dashboards và alarms đã loại bỏ
- Lambda functions đã xóa (nếu có)
- SNS topics đã xóa (nếu có)
- Billing hiển thị $0.00 charges

{{% notice success %}}
**Dọn dẹp Hoàn tất**: Tất cả tài nguyên đã được loại bỏ thành công. Workshop của bạn hoàn tất với zero AWS charges!
{{% /notice %}}

---

**Chúc mừng!** Bạn đã hoàn thành thành công workshop DynamoDB Advanced Patterns và dọn dẹp tất cả tài nguyên.
