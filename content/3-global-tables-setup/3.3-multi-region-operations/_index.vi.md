---
title : "3.3 Multi-Region Operations"
date : "2025-08-11"
weight : 33
chapter : false
pre : " <b> 3.3 </b> "
---

# Multi-Region Operations

🌍 **Thực hành hands-on với cross-region read/write operations và replication testing**

## Tổng quan

Bây giờ Global Tables của bạn đã được verified, hãy experience multi-region operations firsthand. Bạn sẽ create data trong một region, verify nó replicates đến region khác, và test conflict resolution scenarios.

## Exercise 1: Write to Primary, Read from Replica

### Bước 1: Create Global User trong US-East-1

**Ensure bạn đang ở US-East-1**:
1. **Check region**: Top-right sẽ hiển thị "N. Virginia"
2. **Navigate**: DynamoDB → Tables → `demo-ecommerce-freetier`
3. **Go to**: Items tab
4. **Click**: "Create item"

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của US-East-1 region với Create item dialog mở
{{% /notice %}}

### User Creation Template

**Switch to JSON view** và create:

```json
{
  "PK": "USER#global-user-[your-name]",
  "SK": "PROFILE",
  "GSI1PK": "USER#[your-name]@global.com",
  "GSI1SK": "PROFILE",
  "user_id": "global-user-[your-name]",
  "name": "[Your Name] Global",
  "email": "[your-name]@global.com",
  "region_created": "us-east-1",
  "created_timestamp": "2025-08-11T15:30:00Z",
  "test_type": "global_replication",
  "workshop_session": "module3"
}
```

**Important**: Replace `[your-name]` với tên thực của bạn để make items unique.

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của JSON editor với global user data được nhập
{{% /notice %}}

### Bước 2: Note Creation Time

**Record the details**:
1. **Click "Create item"**
2. **Note the time**: Record khi bạn clicked create
3. **Take screenshot**: Của created item

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot hiển thị successfully created item trong US region
{{% /notice %}}

### Bước 3: Switch đến EU-West-1

**Change regions**:
1. **Region selector**: Click dropdown (top-right)
2. **Select**: "Europe (Ireland)"
3. **Wait**: Cho region switch to complete
4. **Navigate**: DynamoDB → Tables → `demo-ecommerce-freetier`

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của region selector với Europe (Ireland) selected
{{% /notice %}}

### Bước 4: Query for Replicated Data

**Search cho user của bạn**:
1. **Items tab**: Navigate đến items view
2. **Click**: "Query" button
3. **Configure query**:
   - **Partition key (PK)**: `USER#global-user-[your-name]`
   - **Sort key (SK)**: `PROFILE`
4. **Click**: "Run"

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của query setup trong EU region looking for the US-created user
{{% /notice %}}

### Bước 5: Verify Replication

**Expected results**:
- **If immediate**: Item appears ngay lập tức
- **If delayed**: Wait 30-60 seconds và try again
- **Replication time**: Note how long it took

**Verify the data**:
- **All attributes**: Sẽ match exactly
- **region_created**: Vẫn sẽ show "us-east-1"
- **Timestamps**: Sẽ identical

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot hiển thị replicated item trong EU region với identical data
{{% /notice %}}

## Exercise 2: Write from Replica, Read from Primary

### Bước 1: Create Product trong EU-West-1

**Stay trong EU-West-1** và create một product:

```json
{
  "PK": "PRODUCT#eu-product-[unique-id]",
  "SK": "DETAILS",
  "GSI1PK": "CATEGORY#eu-electronics",
  "GSI1SK": "PRODUCT#eu-product-[unique-id]",
  "GSI2PK": "PRICE#200-500",
  "GSI2SK": "PRODUCT#eu-product-[unique-id]",
  "product_id": "eu-product-[unique-id]",
  "name": "European Smartphone",
  "description": "Created in EU region",
  "category": "eu-electronics",
  "price": 299,
  "stock": 50,
  "region_created": "eu-west-1",
  "created_timestamp": "2025-08-11T15:35:00Z",
  "test_type": "reverse_replication"
}
```

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của product creation trong EU region
{{% /notice %}}

### Bước 2: Switch Back đến US-East-1

**Return to primary region**:
1. **Region selector**: "US East (N. Virginia)"
2. **Navigate**: DynamoDB → Tables → Items
3. **Query for product**:
   - **PK**: `PRODUCT#eu-product-[unique-id]`
   - **SK**: `DETAILS`

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của US region query looking for EU-created product
{{% /notice %}}

### Bước 3: Verify Reverse Replication

**Check the results**:
- **Product appears**: Trong US region
- **region_created**: Vẫn shows "eu-west-1"
- **All data intact**: Exact copy từ EU

Điều này demonstrates **bi-directional replication** - bạn có thể write to any region!

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot hiển thị EU-created product bây giờ visible trong US region
{{% /notice %}}

## Exercise 3: Conflict Resolution Testing

### Bước 1: Create Base Order

**Trong US-East-1**, create một order:

```json
{
  "PK": "USER#global-user-[your-name]",
  "SK": "ORDER#conflict-test",
  "GSI1PK": "ORDER#conflict-test",
  "GSI1SK": "DETAILS",
  "GSI2PK": "STATUS#pending",
  "GSI2SK": "ORDER#conflict-test",
  "order_id": "conflict-test",
  "user_id": "global-user-[your-name]",
  "status": "pending",
  "total_amount": 100,
  "last_updated_region": "us-east-1",
  "conflict_test": true
}
```

**Wait 2 minutes** cho replication to complete.

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của conflict test order creation trong US region
{{% /notice %}}

### Bước 2: Simultaneous Updates (Advanced)

**Nếu working với một partner**:
1. **Partner A**: Update order trong US-East-1
2. **Partner B**: Update same order trong EU-West-1
3. **Both execute**: Within 10 seconds của nhau

**US Update** (Partner A):
```json
{
  "total_amount": 150,
  "last_updated_region": "us-east-1",
  "update_timestamp": "2025-08-11T15:40:00Z"
}
```

**EU Update** (Partner B):
```json
{
  "total_amount": 200,
  "last_updated_region": "eu-west-1", 
  "update_timestamp": "2025-08-11T15:40:05Z"
}
```

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot hiển thị edit dialog cho conflict testing
{{% /notice %}}

### Bước 3: Observe Conflict Resolution

**After 2-3 minutes**:
1. **Check both regions**: Query the same order
2. **Compare results**: Which update won?
3. **Understand why**: Later timestamp wins

**Expected outcome**: EU update wins vì timestamp 15:40:05 > 15:40:00

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot hiển thị final conflict resolution result trong both regions
{{% /notice %}}

## Exercise 4: Query Patterns Across Regions

### Bước 1: Category Query trong EU

**Test GSI queries** work across regions:
1. **Stay trong EU-West-1**
2. **Query Index**: GSI1
3. **GSI1PK**: `CATEGORY#eu-electronics`
4. **Run query**

**Expected**: Shows products created trong EU region

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của GSI category query trong EU region
{{% /notice %}}

### Bước 2: Status Query trong US

**Test cross-region status queries**:
1. **Switch đến US-East-1**
2. **Query Index**: GSI2
3. **GSI2PK**: `STATUS#pending`
4. **Run query**

**Expected**: Shows orders từ both regions với pending status

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của GSI status query hiển thị orders từ multiple regions
{{% /notice %}}

## Exercise 5: Replication Timing Analysis

### Bước 1: Measure Replication Speed

**Create timestamped items**:
1. **Record start time**: Note exact time trước creation
2. **Create item**: Trong một region
3. **Switch regions**: Immediately
4. **Query repeatedly**: Until item appears
5. **Calculate delay**: End time - start time

**Test Item Template**:
```json
{
  "PK": "TEST#timing-[timestamp]",
  "SK": "REPLICATION",
  "created_at": "[exact-timestamp]",
  "source_region": "us-east-1",
  "test_purpose": "timing_analysis"
}
```

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot hiển thị timing test item creation với precise timestamps
{{% /notice %}}

### Bước 2: Document Results

**Record your findings**:
- **Fastest replication**: ___ seconds
- **Slowest replication**: ___ seconds
- **Average time**: ___ seconds
- **Consistency**: Usually < 2 seconds

## Real-World Scenarios

### Scenario 1: Global User Login

**Simulate global application**:
1. **User logs in**: US region
2. **Profile updated**: Last login timestamp
3. **User travels**: EU region  
4. **App checks**: Profile từ EU
5. **Verify**: Recent login time visible

### Scenario 2: Inventory Management

**Product stock updates**:
1. **Product sold**: US region (-1 stock)
2. **Same product**: EU region query
3. **Stock level**: Eventually consistent
4. **Business logic**: Handle temporary inconsistency

### Scenario 3: Order Processing

**Multi-region order flow**:
1. **Order created**: EU region
2. **Payment processed**: US region
3. **Status updated**: EU region
4. **Fulfillment**: Reads từ nearest region

## Performance Monitoring

### Check Replication Metrics

**During exercises**:
1. **Monitor**: CloudWatch metrics
2. **Watch**: Replication latency
3. **Observe**: Pending replication count
4. **Track**: Error rates (sẽ là 0)

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của CloudWatch metrics hiển thị replication performance trong exercises
{{% /notice %}}

## Troubleshooting Guide

### Replication Not Working

**Common issues**:
- **Wrong region**: Double-check region selection
- **Typos in keys**: Exact match required cho queries
- **Browser cache**: Refresh page
- **Wait longer**: Up to 2 minutes possible

### Queries Returning Empty

**Checklist**:
- **Correct PK/SK**: Exact string match
- **Region correct**: Item exists trong queried region
- **GSI populated**: GSI keys included trong item
- **Query type**: Using Query, không phải Scan

### Conflict Resolution Unexpected

**Understanding**:
- **Timestamp precision**: Millisecond level
- **Clock synchronization**: AWS handles timing
- **Application design**: Plan cho overwrites

{{% notice warning %}}
**Important**: Nếu bạn experience issues, check the Global Tables health status trong console và verify network connectivity.
{{% /notice %}}

## Exercise Summary

Bằng cách completing các exercises này, bạn đã experienced:

- ✅ **Cross-region replication** trong both directions
- ✅ **Eventual consistency** timing
- ✅ **Conflict resolution** với Last Writer Wins
- ✅ **Query patterns** working across regions
- ✅ **Real-world scenarios** và timing analysis

{{% notice success %}}
**Multi-Region Mastery**: Bây giờ bạn understand how Global Tables enables truly global applications với local performance!
{{% /notice %}}

## Next Steps

Với hands-on Global Tables experience complete, hãy monitor the replication performance và understand the metrics giúp bạn operate global applications trong production.
