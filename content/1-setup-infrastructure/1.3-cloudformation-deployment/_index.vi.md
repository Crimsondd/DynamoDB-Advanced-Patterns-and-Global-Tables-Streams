---
title : "1.3 Triển khai CloudFormation"
date : "2025-08-11"
weight : 13
chapter : false
pre : " <b> 1.3 </b> "
---

# Triển khai CloudFormation

🚀 **Hướng dẫn từng bước triển khai hạ tầng AWS sử dụng Infrastructure as Code**

## Tổng quan

Trong phần này, bạn sẽ triển khai hạ tầng workshop DynamoDB hoàn chỉnh sử dụng AWS CloudFormation. Phương pháp này đảm bảo triển khai nhất quán, có thể tái tạo và tuân theo AWS best practices.

## Prerequisites

Trước khi bắt đầu triển khai, đảm bảo:

- Quyền truy cập AWS Console với administrative permissions
- Region đã xác minh: **US East (N. Virginia) us-east-1**
- Trạng thái billing hiện tại: **$0.00**
- CloudFormation template đã tải về

## Tổng quan CloudFormation Template

Template của chúng ta tạo các tài nguyên sau:

### Hạ tầng Cốt lõi
- **DynamoDB Table**: Single table với Global Tables enabled
- **Lambda Function**: Stream processor cho real-time events
- **IAM Roles**: Chính sách truy cập least privilege
- **DynamoDB Streams**: Cấu hình change data capture

### Monitoring & Alerting
- **CloudWatch Dashboard**: Hiển thị metrics thời gian thực
- **CloudWatch Alarms**: Cảnh báo dựa trên threshold
- **Billing Alerts**: Cơ chế bảo vệ chi phí

### Cấu hình Bảo mật
- **IAM Policies**: Quyền fine-grained
- **Resource Encryption**: Bảo vệ dữ liệu at rest
- **VPC Integration**: Cách ly network (tùy chọn)

## Triển khai Từng bước

### Bước 1: Truy cập CloudFormation Service

1. **Điều hướng đến CloudFormation**
   - Mở AWS Management Console
   - Tìm kiếm "CloudFormation" hoặc tìm trong services menu
   - Đảm bảo bạn ở region **US East (N. Virginia)**

2. **Tạo New Stack**
   - Nhấp nút "Create stack"
   - Chọn "With new resources (standard)"

![create-stack](/images/1/create-stack.png?featherlight=false&width=90pc)

### Bước 2: Upload Template

1. **Chọn Template Source**
   - Chọn "Upload a template file"
   - Nhấp nút "Choose file"
   - Chọn file `infrastructure.yaml`

2. **Validate Template**
   - CloudFormation sẽ tự động validate syntax
   - Xem lại template details nếu cần
   - Nhấp "Next" để tiếp tục

![Choose_Template](/images/1/Choose-Template.png?featherlight=false&width=90pc)

### Bước 3: Cấu hình Stack Parameters

**Stack Details:**
```
Stack name: demo-dynamodb-freetier
Description: DynamoDB Advanced Patterns Workshop Infrastructure
```

**Parameters:**
```
Environment: demo
PrimaryRegion: us-east-1
SecondaryRegion: eu-west-1
TableName: demo-ecommerce-freetier
```

**Resource Configuration:**
```
ReadCapacityUnits: 5
WriteCapacityUnits: 5
StreamViewType: NEW_AND_OLD_IMAGES
```

### Bước 4: Cấu hình Stack Options

**Tags (Tùy chọn):**
```
Workshop: DynamoDB-Advanced-Patterns
Environment: Demo
CostCenter: Learning
```

**Permissions:**
- Sử dụng existing service role (nếu có)
- Hoặc cho phép CloudFormation tạo new role

**Advanced Options:**
- Giữ tất cả defaults
- Rollback on failure: Enabled
- Stack creation timeout: 10 phút

### Bước 5: Review và Deploy

1. **Review Configuration**
   - Xác minh tất cả parameters đúng
   - Kiểm tra resource list khớp với expectations
   - Xác nhận estimated costs ($0.00 cho Free Tier)

2. **Acknowledge Capabilities**
   - ✅ Check: "I acknowledge that AWS CloudFormation might create IAM resources"
   - ✅ Check: "I acknowledge that AWS CloudFormation might create IAM resources with custom names"

3. **Create Stack**
   - Nhấp nút "Create stack"
   - Deployment bắt đầu ngay lập tức

![review-deploy](/images/1/review-deploy.png?featherlight=false&width=90pc)

## Giám sát Deployment

### Stack Events Tab
Giám sát tiến trình deployment thời gian thực:

![Stack-Events](/images/1/Stack-Events.png?featherlight=false&width=90pc)

### Timeline Dự kiến
- **Tổng Thời lượng**: 5-7 phút
- **IAM Resources**: 1-2 phút
- **DynamoDB Table**: 2-3 phút
- **Lambda Function**: 1-2 phút
- **CloudWatch Components**: 1-2 phút

## Troubleshooting Các Vấn đề Thường gặp

### Vấn đề: Insufficient Permissions
**Triệu chứng**: CREATE_FAILED cho IAM resources
**Giải pháp**: 
- Xác minh account có administrator access
- Kiểm tra IAM permissions cho CloudFormation
- Sử dụng root account nếu cần (chỉ cho workshop)

### Vấn đề: Resource Limits Exceeded
**Triệu chứng**: CREATE_FAILED cho DynamoDB hoặc Lambda
**Giải pháp**:
- Kiểm tra Free Tier usage trong billing console
- Xác minh không có existing resources tiêu thụ limits
- Liên hệ AWS support nếu cần

### Vấn đề: Region Mismatch
**Triệu chứng**: Template validation errors
**Giải pháp**:
- Xác minh region là us-east-1
- Kiểm tra tất cả parameters phù hợp với region
- Khởi động lại deployment ở region đúng

## Các Bước Xác minh

Sau khi deployment thành công:

1. **Stack Status**: CREATE_COMPLETE ✅
2. **Điều hướng đến Outputs tab**
3. **Ghi lại các giá trị quan trọng**:
   - Table Name
   - Lambda Function ARN
   - Dashboard URL
   - Stream ARN

{{% notice success %}}
**Deployment Hoàn tất!** 
Hạ tầng DynamoDB Advanced Patterns của bạn hiện đang chạy. Trong phần tiếp theo, chúng ta sẽ xác minh tất cả components hoạt động đúng.
{{% /notice %}}

## Các Bước Tiếp theo

1. Xác minh DynamoDB table đang active
2. Test Lambda function deployment
3. Kiểm tra CloudWatch dashboard
4. Xác nhận zero billing charges
5. Bắt đầu data modeling exercises
