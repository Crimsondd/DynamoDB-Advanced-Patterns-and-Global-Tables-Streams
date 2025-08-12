---
title : "1.4 Xác minh Hạ tầng"
date : "2025-08-11"
weight : 14
chapter : false
pre : " <b> 1.4 </b> "
---

# Xác minh Hạ tầng

✅ **Kiểm tra toàn diện để đảm bảo tất cả tài nguyên AWS được triển khai và hoạt động đúng cách**

## Tổng quan

Sau khi triển khai CloudFormation, việc xác minh tất cả tài nguyên hoạt động đúng là rất quan trọng. Phần này cung cấp quy trình xác minh từng bước để đảm bảo hạ tầng của bạn sẵn sàng cho workshop.

## Checklist Xác minh

Sử dụng checklist này để xác minh từng thành phần một cách có hệ thống:

- [ ] Trạng thái CloudFormation stack: CREATE_COMPLETE
- [ ] DynamoDB table: Active và có thể truy cập
- [ ] DynamoDB Global Tables: Replication được cấu hình
- [ ] Lambda function: Đã triển khai và kết nối với stream
- [ ] CloudWatch dashboard: Metrics hiển thị
- [ ] IAM roles: Permissions được cấu hình đúng
- [ ] Trạng thái billing: $0.00 phí
- [ ] Sử dụng Free Tier: Trong giới hạn

## Bước 1: Xác minh CloudFormation Stack

### 1.1 Kiểm tra Stack Status

**Điều hướng đến CloudFormation**:
1. AWS Console → CloudFormation → Stacks
2. Tìm stack: `demo-dynamodb-freetier`
3. **Status nên là**: `CREATE_COMPLETE` ✅

**Nếu status hiển thị khác**:
- `CREATE_IN_PROGRESS`: Đợi hoàn thành
- `CREATE_FAILED`: Kiểm tra Events tab để tìm lỗi
- `ROLLBACK_COMPLETE`: Xóa và triển khai lại

### 1.2 Xem lại Stack Outputs

**Nhấp vào stack của bạn** → **Outputs tab**:

![OutPut-Stack](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/1/Output-Stack.png?featherlight=false&width=90pc)

**Ghi lại các giá trị này** - bạn sẽ sử dụng chúng để xác minh thêm.

## Bước 2: Xác minh DynamoDB Table

### 2.1 Truy cập DynamoDB Console

**Điều hướng đến DynamoDB**:
1. AWS Console → Services → DynamoDB
2. Nhấp **Tables** trong sidebar trái
3. Tìm table: `demo-ecommerce-freetier`

### 2.2 Kiểm tra Table Status

**Table Overview**:

```text
Chi tiết Table:
┌─────────────────────┬─────────────────────────────────────────┐
│ Property            │ Expected Value                          │
├─────────────────────┼─────────────────────────────────────────┤
│ Table Status        │ Active ✅                               │
│ Partition Key       │ PK (String)                             │
│ Sort Key            │ SK (String)                             │
│ Read Capacity       │ 5 (Provisioned)                         │
│ Write Capacity      │ 5 (Provisioned)                         │
│ Point-in-time Rec.  │ Enabled                                 │
│ Streams             │ Enabled (NEW_AND_OLD_IMAGES)            │
└─────────────────────┴─────────────────────────────────────────┘
```

### 2.3 Xác minh Cấu hình Table

**Nhấp vào tên table** để xem chi tiết:

**General Tab**:
- **Table name**: `demo-ecommerce-freetier`
- **Primary key**: `PK` (String), `SK` (String)
- **Table status**: Active
- **Creation date**: Ngày hôm nay

**Capacity Tab**:
- **Read capacity**: 5 units (Provisioned)
- **Write capacity**: 5 units (Provisioned)
- **Auto scaling**: Disabled (để an toàn Free Tier)

### 2.4 Kiểm tra DynamoDB Streams

**Exports and streams Tab**:
- **DynamoDB stream**: Enabled ✅
- **Stream view type**: New and old images
- **Stream ARN**: Nên khớp với CloudFormation output

## Bước 3: Xác minh Global Tables Setup

### 3.1 Kiểm tra Cấu hình Global Tables

**Global Tables Tab**:
- **Primary region**: us-east-1 (US East N. Virginia)
- **Replica regions**: eu-west-1 (Europe Ireland)
- **Replication status**: Active

### 3.2 Xác minh Secondary Region

**Chuyển đến EU-West-1**:
1. **Đổi region** trong AWS Console thành "Europe (Ireland)"
2. **Điều hướng đến DynamoDB** → Tables
3. **Tìm replica table**: `demo-ecommerce-freetier`
4. **Status nên là**: Active

**Replica Table Properties**:
- **Table status**: Active
- **Read capacity**: 5 units
- **Global table**: Yes (replica)
- **Primary region**: us-east-1

## Bước 4: Xác minh Lambda Function

### 4.1 Truy cập Lambda Console

**Điều hướng đến Lambda**:
1. **Chuyển lại về US-East-1** region
2. AWS Console → Services → Lambda
3. Nhấp **Functions** trong sidebar trái
4. Tìm function: `demo-dynamodb-stream-processor`

### 4.2 Kiểm tra Cấu hình Function

**Function Overview**:

```text
Chi tiết Lambda Function:
┌─────────────────────┬─────────────────────────────────────────┐
│ Property            │ Expected Value                          │
├─────────────────────┼─────────────────────────────────────────┤
│ Function Name       │ demo-dynamodb-stream-processor          │
│ Runtime             │ Python 3.9                              │
│ Memory              │ 128 MB                                  │
│ Timeout             │ 30 seconds                              │
│ Handler             │ lambda_function.lambda_handler          │
│ Last Modified       │ Ngày hôm nay                            │
└─────────────────────┴─────────────────────────────────────────┘
```

### 4.3 Xác minh Stream Trigger

**Configuration Tab**:
- **Triggers**: DynamoDB stream nên được liệt kê
- **Source**: `demo-ecommerce-freetier` table
- **State**: Enabled
- **Batch size**: 100 (default)

### 4.4 Test Function Permissions

**Permissions Tab**:
- **Execution role**: Nên có quyền đọc DynamoDB stream
- **Resource-based policy**: Nên được cấu hình tự động

## Bước 5: Xác minh CloudWatch Dashboard

### 5.1 Truy cập CloudWatch Console

**Điều hướng đến CloudWatch**:
1. AWS Console → Services → CloudWatch
2. Nhấp **Dashboards** trong sidebar trái
3. Tìm dashboard: `demo-dynamodb-freetier-monitoring`

### 5.2 Kiểm tra Dashboard Widgets

**Widgets Dự kiến**:

```text
Layout Dashboard:
┌─────────────────────┬─────────────────────────────────────────┐
│ Widget              │ Description                             │
├─────────────────────┼─────────────────────────────────────────┤
│ DynamoDB RCU        │ Read Capacity Utilization              │
│ DynamoDB WCU        │ Write Capacity Utilization             │
│ DynamoDB Throttles  │ Throttled Read/Write Requests           │
│ Lambda Invocations  │ Function invocation count               │
│ Lambda Errors       │ Function error rate                     │
│ Lambda Duration     │ Function execution duration             │
└─────────────────────┴─────────────────────────────────────────┘
```

### 5.3 Xác minh Metrics Data

**Trạng thái Ban đầu** (chưa có hoạt động):
- **DynamoDB metrics**: Nên hiển thị 0 consumed capacity
- **Lambda metrics**: Nên hiển thị 0 invocations
- **Tất cả metrics**: Nên hiển thị nhưng chưa có data points

## Bước 6: Test Data Operations

### 6.1 Tạo Test Item

**Thêm sample data để xác minh chức năng table**:

1. **Đi đến DynamoDB Console** → Tables → `demo-ecommerce-freetier`
2. **Nhấp "Explore table items"**
3. **Nhấp "Create item"**

**Test Item**:
```json
{
  "PK": "USER#test123",
  "SK": "PROFILE",
  "name": "Test User",
  "email": "test@example.com",
  "created_at": "2025-08-11T10:00:00Z"
}
```

### 6.2 Xác minh Item Creation

**Xác nhận item xuất hiện**:
- Item nên hiển thị trong table
- Item count nên tăng lên 1
- Không nên có lỗi xuất hiện

### 6.3 Kiểm tra Lambda Trigger

**Xác minh stream processing**:
1. **Đi đến Lambda Console**
2. **Nhấp vào stream processor function**
3. **Kiểm tra "Monitor" tab**
4. **Nên thấy 1 invocation** (từ việc tạo item)

## Bước 7: Xác minh Chi phí và Free Tier

### 7.1 Kiểm tra Current Billing

**Điều hướng đến Billing**:
1. AWS Console → Services → Billing & Cost Management
2. **Current charges**: Nên hiển thị **$0.00** ✅
3. **Month-to-date**: Nên hiển thị **$0.00** ✅

### 7.2 Xác minh Free Tier Usage

**Free Tier Dashboard**:
- **DynamoDB**: Nên hiển thị sử dụng tối thiểu
- **Lambda**: Nên hiển thị <10 invocations
- **CloudWatch**: Nên hiển thị active metrics

**Usage Breakdown**:
```text
Sử dụng Free Tier Hiện tại:
┌─────────────────┬──────────────┬──────────────┬─────────────┐
│ Service         │ Used         │ Available    │ % Used      │
├─────────────────┼──────────────┼──────────────┼─────────────┤
│ DynamoDB RCU    │ <1 unit      │ 25 units     │ <4%         │
│ DynamoDB WCU    │ <1 unit      │ 25 units     │ <4%         │
│ DynamoDB Storage│ <0.01 GB     │ 25 GB        │ <0.1%       │
│ Lambda Requests │ 1 request    │ 1M requests  │ <0.001%     │
│ Lambda Duration │ <1 GB-sec    │ 400K GB-sec  │ <0.001%     │
└─────────────────┴──────────────┴──────────────┴─────────────┘

Tổng Sử dụng: <1% giới hạn Free Tier ✅
```

## Bước 8: Tóm tắt Xác minh Cuối cùng

### 8.1 Hoàn thành Verification Checklist

**Trạng thái Hạ tầng**:
- [x] CloudFormation: CREATE_COMPLETE
- [x] DynamoDB: Active table với streams
- [x] Global Tables: Replication hoạt động
- [x] Lambda: Function đã triển khai và triggered
- [x] CloudWatch: Dashboard có thể truy cập
- [x] IAM: Permissions được cấu hình đúng
- [x] Billing: $0.00 phí
- [x] Test data: Được tạo và xử lý thành công

### 8.2 Sẵn sàng cho Module Tiếp theo

**Infrastructure Health Check**:
```text
🟢 Tất cả Hệ thống Hoạt động
┌─────────────────────┬─────────────────────┬─────────────────────┐
│ Component           │ Status              │ Health              │
├─────────────────────┼─────────────────────┼─────────────────────┤
│ DynamoDB Table      │ Active              │ 🟢 Healthy         │
│ Global Tables       │ Replicating         │ 🟢 Healthy         │
│ Lambda Function     │ Active              │ 🟢 Healthy         │
│ CloudWatch          │ Monitoring          │ 🟢 Healthy         │
│ Cost Management     │ $0.00               │ 🟢 On Track        │
└─────────────────────┴─────────────────────┴─────────────────────┘
```

{{% notice success %}}
**Xác minh Hạ tầng Hoàn tất**: Tất cả tài nguyên đã được triển khai đúng cách và hoạt động. Bạn sẵn sàng tiến hành Module 2: Single Table Design.
{{% /notice %}}

## Các Bước Tiếp theo

Với hạ tầng đã được xác minh thành công, bây giờ bạn có:

- **DynamoDB table production-ready** với Global Tables
- **Lambda stream processor hoạt động đầy đủ**
- **Thiết lập monitoring và alerting hoàn chỉnh**
- **Triển khai Free Tier không mất phí**

**Sẵn sàng cho**: [Module 2: Single Table Design](/2-single-table-design/)

{{% notice tip %}}
**Giữ Môi trường Này**: Đừng xóa các tài nguyên này - chúng ta sẽ sử dụng chúng trong suốt workshop cho các bài tập thực hành.
{{% /notice %}}
