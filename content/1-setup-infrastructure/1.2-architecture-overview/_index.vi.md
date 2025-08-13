---
title : "1.2 Tổng quan Kiến trúc"
date : "2025-08-11"
weight : 12
chapter : false
pre : " <b> 1.2 </b> "
---

# Tổng quan Kiến trúc

🏗️ **Hiểu về hạ tầng hoàn chỉnh chúng ta sẽ triển khai cho workshop DynamoDB**

## Kiến trúc Cấp cao

Hạ tầng workshop của chúng ta trải rộng hai vùng AWS để minh họa chức năng Global Tables trong khi duy trì tuân thủ Free Tier.

![global-tables-architecture](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/1/global-tables-architecture-improved.png?featherlight=false&width=90pc)

## Các Thành phần Cốt lõi

### 1. DynamoDB Global Table

**Bảng Chính (US-East-1)**:

- **Tên Bảng**: `demo-ecommerce-freetier`
- **Partition Key**: `PK` (String)
- **Sort Key**: `SK` (String)
- **Capacity**: 5 RCU / 5 WCU (Provisioned)
- **Streams**: Enabled (NEW_AND_OLD_IMAGES)
- **Point-in-time Recovery**: Enabled

**Bảng Replica (EU-West-1)**:

- **Synchronized**: Sao chép tự động
- **Read Capacity**: 5 RCU
- **Eventual Consistency**: Chéo vùng
- **Local Reads**: Độ trễ thấp cho người dùng EU

### 2. Lambda Stream Processor

**Cấu hình Function**:

```python
Runtime: Python 3.9
Memory: 128 MB (Tối ưu Free Tier)
Timeout: 30 seconds
Environment: Demo
Trigger: DynamoDB Streams
```

**Logic Xử lý**:

- **Stream Records**: Xử lý các sự kiện INSERT, MODIFY, REMOVE
- **Data Validation**: Đảm bảo tính toàn vẹn dữ liệu
- **Audit Logging**: Theo dõi tất cả thay đổi
- **Error Handling**: Dead letter queue cho records thất bại

### 3. CloudWatch Monitoring

**Thành phần Dashboard**:

- **DynamoDB Metrics**: Sử dụng Read/Write capacity
- **Lambda Metrics**: Số lần gọi, thời lượng, lỗi
- **Cost Tracking**: Sử dụng Free Tier thời gian thực
- **Performance**: Metrics độ trễ và throughput

**Billing Alarms**:

- **Cảnh báo ở $1.00**: Hệ thống cảnh báo sớm
- **Cảnh báo ở 80% Free Tier**: Giám sát sử dụng
- **Email Notifications**: Nhận thức ngay lập tức

## Kiến trúc Data Model

### Mẫu Single Table Design

Nền tảng thương mại điện tử của chúng ta sử dụng một bảng DynamoDB duy nhất với nhiều loại entity:

| Loại Entity | Partition Key | Sort Key | Mục đích |
|-------------|---------------|----------|----------|
| User Profile | USER#id | PROFILE | User metadata |
| User Addresses | USER#id | ADDRESS#id | Thông tin ship |
| Product | PRODUCT#id | DETAILS | Danh mục sản phẩm |
| Product Reviews | PRODUCT#id | REVIEW#user_id | Đánh giá khách |
| Order Header | ORDER#id | DETAILS | Order metadata |
| Order Items | ORDER#id | ITEM#product_id | Nội dung order |
| Category | CATEGORY#id | DETAILS | Nhóm sản phẩm |

### Access Patterns

1. **Get User Profile**: `PK=USER#123, SK=PROFILE`
2. **Get User's Orders**: `PK=USER#123, SK begins_with ORDER#`
3. **Get Product Details**: `PK=PRODUCT#456, SK=DETAILS`
4. **Get Order with Items**: `PK=ORDER#789, SK begins_with ITEM#`
5. **Get Product Reviews**: `PK=PRODUCT#456, SK begins_with REVIEW#`

## Kiến trúc Bảo mật

### IAM Roles và Policies

**Lambda Execution Role**:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:DescribeStream",
        "dynamodb:GetRecords",
        "dynamodb:GetShardIterator",
        "dynamodb:ListStreams"
      ],
      "Resource": "arn:aws:dynamodb:*:*:table/demo-ecommerce-freetier/stream/*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:*:*:*"
    }
  ]
}
```

**Nguyên tắc Least Privilege**:

- Lambda: Chỉ quyền đọc stream
- CloudWatch: Chỉ quyền ghi metrics và logs
- DynamoDB: Truy cập chỉ định bảng

## Kiến trúc Network

### Chiến lược Triển khai Vùng

**Vùng Chính (US-East-1)**:

- **Availability**: 99.99% SLA
- **Latency**: <10ms cho người dùng US
- **Capacity**: Hoạt động đọc/ghi đầy đủ
- **Backup**: Point-in-time recovery

**Vùng Phụ (EU-West-1)**:

- **Availability**: 99.99% SLA (độc lập)
- **Latency**: <10ms cho người dùng EU
- **Capacity**: Tối ưu đọc
- **Sync**: Sao chép eventually consistent

### Data Flow

1. **Write Operations**: Luôn đến vùng chính
2. **Read Operations**: Có thể từ bất kỳ vùng nào
3. **Replication**: Đồng bộ cross-region tự động
4. **Conflict Resolution**: Last-writer-wins
5. **Failover**: Promotion thủ công nếu cần

## Kiến trúc Chi phí

### Tối ưu Free Tier

**Chi phí DynamoDB**:

```text
Bảng Chính (US-East-1):
- Provisioned RCU: 5 units (Free: 25) = $0.00
- Provisioned WCU: 5 units (Free: 25) = $0.00
- Storage: <1 GB (Free: 25 GB) = $0.00

Bảng Replica (EU-West-1):
- Provisioned RCU: 5 units (Free: 25) = $0.00
- Cross-region replication: <1 GB/month = $0.00
```

**Chi phí Lambda**:

```text
Stream Processor:
- Invocations: ~100/ngày (Free: 1M/tháng) = $0.00
- Duration: 128MB × 1s × 100 = <1 GB-second = $0.00
```

**Tổng Chi phí Workshop**: **$0.00** ✅

## Cân nhắc Scalability

### Horizontal Scaling

**DynamoDB**:

- **Auto Scaling**: Có thể bật nếu cần
- **On-Demand**: Chuyển từ provisioned
- **Global Secondary Indexes**: Thêm cho access patterns mới

**Lambda**:

- **Concurrent Executions**: Lên đến 1000 mặc định
- **Dead Letter Queue**: Xử lý failures
- **Reserved Concurrency**: Kiểm soát scaling

### Tối ưu Performance

**Read Performance**:

- **Consistent Reads**: Khi cần tính nhất quán dữ liệu
- **Eventually Consistent**: Cho performance tốt hơn
- **DAX**: DynamoDB Accelerator cho caching

**Write Performance**:

- **Batch Operations**: Giảm API calls
- **Parallel Writes**: Nhiều partition keys
- **Write Sharding**: Phân phối hot partitions

## Monitoring và Observability

### Key Metrics để Monitor

**DynamoDB**:

- **Consumed Read Capacity**: Target <80% of provisioned
- **Consumed Write Capacity**: Target <80% of provisioned
- **Throttled Requests**: Nên là 0
- **Error Rate**: Target <1%

**Lambda**:

- **Invocation Count**: Theo dõi volume xử lý
- **Duration**: Monitor xu hướng performance
- **Error Rate**: Target <1%
- **Dead Letter Queue**: Monitor failed records

### Chiến lược Alerting

**Critical Alerts**:

- DynamoDB throttling events
- Lambda function errors
- Billing threshold exceeded
- Free Tier limit approaching

**Warning Alerts**:

- High capacity utilization (>70%)
- Increased error rates
- Performance degradation

{{% notice info %}}
**Kiến trúc Sẵn sàng**: Kiến trúc này cung cấp nền tảng production-ready để học các mẫu DynamoDB nâng cao trong khi vẫn ở trong giới hạn AWS Free Tier.
{{% /notice %}}

## Các Bước Tiếp theo

Với việc hiểu kiến trúc hoàn tất, chúng ta sẽ tiến hành triển khai hạ tầng này bằng CloudFormation templates trong phần tiếp theo.
