---
title : "3.2 Xác minh Global Setup"
date : "2025-08-11"
weight : 32
chapter : false
pre : " <b> 3.2 </b> "
---

# Xác minh Global Setup

✅ **Xác minh từng bước cấu hình Global Tables của bạn thông qua AWS Console**

## Tổng quan

CloudFormation template của bạn đã tự động cấu hình Global Tables giữa US-East-1 và EU-West-1. Hãy xác minh mọi thứ hoạt động correctly trước khi chúng ta bắt đầu testing replication.

## Bước 1: Truy cập Primary Region

### Navigate đến US-East-1

1. **Open AWS Console**: Đảm bảo bạn đã logged in
2. **Check Region**: Góc trên-phải sẽ hiển thị "N. Virginia"
3. **Switch if needed**: Click region dropdown → "US East (N. Virginia)"

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của AWS Console với region selector hiển thị US East (N. Virginia)
{{% /notice %}}

### Tìm DynamoDB Table của bạn

1. **Services**: Navigate đến DynamoDB service
2. **Tables**: Click "Tables" trong left sidebar
3. **Locate**: Tìm `demo-ecommerce-freetier`
4. **Status**: Verify table hiển thị "Active"

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của DynamoDB Tables list hiển thị demo-ecommerce-freetier với Active status
{{% /notice %}}

## Bước 2: Check Global Tables Configuration

### Access Global Tables Tab

1. **Click**: Table name `demo-ecommerce-freetier`
2. **Navigate**: Click "Global tables" tab
3. **Review**: Global Tables configuration

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của table overview với Global tables tab được highlighted
{{% /notice %}}

### Verify Global Tables Status

**Expected Configuration**:

| Region | Status | Role | Health |
|--------|--------|------|--------|
| **us-east-1** | Active | Primary | ✅ Healthy |
| **eu-west-1** | Active | Replica | ✅ Healthy |

**Key indicators cần verify**:
- **Replication Status**: "Healthy" hoặc "Active"
- **Last Replication**: Recent timestamp
- **Pending Updates**: Sẽ là 0

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của Global tables configuration hiển thị cả hai regions với healthy status
{{% /notice %}}

## Bước 3: Verify Secondary Region

### Switch đến EU-West-1

1. **Region Selector**: Click region dropdown (top-right)
2. **Select**: "Europe (Ireland)" - eu-west-1
3. **Wait**: Cho phép console switch regions

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của region selector dropdown với Europe (Ireland) được highlighted
{{% /notice %}}

### Check Replica Table

1. **Navigate**: DynamoDB → Tables
2. **Find**: Cùng table name `demo-ecommerce-freetier`
3. **Verify**: Table exists và hiển thị "Active"
4. **Check**: Global tables tab hiển thị replica status

**Expected trong EU-West-1**:
- **Table Name**: `demo-ecommerce-freetier` (identical)
- **Status**: Active
- **Role**: Replica table
- **Primary Region**: us-east-1

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của EU region hiển thị replica table với same name
{{% /notice %}}

## Bước 4: Compare Table Schemas

### Verify Schema Consistency

Cả hai regions sẽ có identical table schema:

**Primary Keys**:
- **Partition Key**: PK (String)
- **Sort Key**: SK (String)

**Global Secondary Indexes**:
- **GSI1**: GSI1PK (String), GSI1SK (String)
- **GSI2**: GSI2PK (String), GSI2SK (String)

**Settings**:
- **Read Capacity**: 5 units (provisioned)
- **Write Capacity**: 5 units (provisioned)
- **Point-in-time Recovery**: Enabled
- **DynamoDB Streams**: Enabled

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot so sánh table schema giữa US và EU regions
{{% /notice %}}

## Bước 5: Check Current Data

### Verify Existing Data Replication

Nếu bạn đã hoàn thành Module 2, check rằng existing data của bạn xuất hiện trong cả hai regions:

**Switch đến US-East-1**:
1. **Go to**: Items tab
2. **Count items**: Note số lượng items

**Switch đến EU-West-1**:
1. **Go to**: Items tab  
2. **Compare count**: Sẽ match US region exactly

**Nếu item counts không match**:
- Wait 1-2 phút cho replication
- Refresh browser page
- Check Global Tables health status

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot hiển thị identical item counts trong cả hai regions
{{% /notice %}}

## Bước 6: Verify Stream Configuration

### Check DynamoDB Streams

**Trong US-East-1**:
1. **Table Overview**: Go to table details
2. **Exports and streams**: Click tab
3. **Stream details**: Verify settings

**Expected Stream Configuration**:
- **Stream Status**: Enabled
- **Stream view type**: New and old images
- **Stream ARN**: Sẽ present
- **Shard count**: 1 hoặc nhiều active shards

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của DynamoDB Streams configuration hiển thị enabled status
{{% /notice %}}

## Bước 7: Health Check Dashboard

### Monitor Replication Health

**Access Monitoring**:
1. **Metrics Tab**: Click trong table view
2. **Global Tables metrics**: Look for replication metrics
3. **Key metrics**: 
   - Replication latency
   - Pending replication count
   - Failed replication events

**Healthy Indicators**:
- **Replication Latency**: < 2 seconds average
- **Pending Count**: 0 hoặc very low
- **Error Rate**: 0%

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của metrics dashboard hiển thị healthy replication metrics
{{% /notice %}}

## Bước 8: Network Connectivity Test

### Test Cross-Region Communication

**Simple connectivity verification**:
1. **Create test item** trong US-East-1
2. **Wait 30 seconds**
3. **Check EU-West-1** cho item đó
4. **Delete test item** từ either region

**Test Item Example**:
```json
{
  "PK": "TEST#connectivity",
  "SK": "VERIFICATION",
  "timestamp": "2025-08-11T15:00:00Z",
  "test_purpose": "global_tables_verification"
}
```

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của test item xuất hiện trong cả hai regions
{{% /notice %}}

## Troubleshooting Common Issues

### Issue: Table Not Found trong EU-West-1

**Possible Causes**:
- Wrong region selected
- CloudFormation deployment incomplete
- Global Tables setup failed

**Solutions**:
1. **Double-check region** trong top-right corner
2. **Verify CloudFormation** stack completed successfully
3. **Check IAM permissions** cho cross-region access

### Issue: Replication Status Unhealthy

**Check These Items**:
- **Network connectivity** giữa regions
- **DynamoDB Streams** enabled trên source table
- **IAM permissions** cho Global Tables service
- **Table capacity** không throttling

### Issue: Item Counts Don't Match

**Troubleshooting Steps**:
1. **Wait longer** (up to 2 minutes)
2. **Refresh browser** page
3. **Check for errors** trong CloudWatch logs
4. **Verify no throttling** trong metrics

## Verification Checklist

Trước khi proceeding đến multi-region operations:

- [ ] **Both regions accessible** thông qua AWS Console
- [ ] **Table exists** trong cả us-east-1 và eu-west-1
- [ ] **Global Tables status** hiển thị "Healthy" hoặc "Active"
- [ ] **Schema identical** giữa regions
- [ ] **DynamoDB Streams** enabled
- [ ] **Existing data replicated** (nếu có)
- [ ] **Test connectivity** working
- [ ] **Monitoring metrics** available

{{% notice success %}}
**Verification Complete**: Global Tables setup của bạn healthy và ready cho multi-region operations!
{{% /notice %}}

## Next Steps

Với Global Tables đã verified và healthy, bạn ready để experience multi-region operations. Trong section tiếp theo, chúng ta sẽ create data trong một region và watch nó automatically appear trong region khác!

{{% notice tip %}}
**Pro Tip**: Giữ cả hai region tabs mở trong browser của bạn (US-East-1 và EU-West-1) để easily switch giữa chúng trong các exercises.
{{% /notice %}}
