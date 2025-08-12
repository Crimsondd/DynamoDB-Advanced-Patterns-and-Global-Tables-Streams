---
title : "2.4 Query Patterns"
date : "2025-08-11"
weight : 24
chapter : false
pre : " <b> 2.4 </b> "
---

# Query Patterns

🔍 **Học các kỹ thuật query hiệu quả cho Single Table Design sử dụng DynamoDB Console**

## Tổng quan

Bây giờ bạn đã tạo dữ liệu e-commerce của mình, hãy khám phá các query patterns mạnh mẽ làm cho Single Table Design trở nên hiệu quả. Bạn sẽ học cách retrieve dữ liệu hiệu quả sử dụng cả table queries và Global Secondary Index (GSI) queries.

## Query vs Scan - Sự khác biệt quan trọng

### Luôn sử dụng Query (Không phải Scan)

- **Query**: Nhanh, hiệu quả, cost-effective (sử dụng primary keys)
- **Scan**: Chậm, đắt đỏ, đọc toàn bộ table (tránh trong production)

{{% notice warning %}}
**Quan trọng**: Luôn sử dụng Query operations trong workshop này. Scan operations không hiệu quả và có thể nhanh chóng vượt quá giới hạn Free Tier.
{{% /notice %}}

## Pattern 1: Get User Profile

### Single Item Lookup

**Mục tiêu**: Retrieve thông tin profile của một user cụ thể

**Truy cập Query Interface**:
1. **Go to**: Items tab trong DynamoDB table của bạn
2. **Click**: nút "Query" (không phải Scan)
3. **Ensure**: Table query được chọn (không phải index)

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của vị trí nút Query và lựa chọn table/index
{{% /notice %}}

### Configure User Profile Query

**Query Parameters**:
- **Partition key (PK)**: `USER#user001`
- **Sort key (SK)**: `PROFILE`
- **Query condition**: Sử dụng "equals" (default)

**Expected Result**: Single item chứa user profile data

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của query parameters được điền cho user profile lookup
{{% /notice %}}

### Execute và Verify

1. **Click "Run"**
2. **Check results**: Sẽ return 1 item
3. **Verify data**: Profile information sẽ được hiển thị

**Performance**: ~1-2ms latency, 1 RCU consumed

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của query results hiển thị user profile data
{{% /notice %}}

## Pattern 2: Get User's Orders

### One-to-Many Relationship Query

**Mục tiêu**: Retrieve tất cả orders cho một user cụ thể

**Query Configuration**:
- **Partition key (PK)**: `USER#user001`
- **Sort key condition**: "begins_with"
- **Sort key value**: `ORDER#`

Pattern này retrieves cả user profile VÀ tất cả orders của họ trong một query duy nhất.

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot hiển thị "begins_with" sort key condition setup
{{% /notice %}}

### Advanced Sort Key Options

**Available sort key conditions**:
- **=** (equals): Exact match
- **begins_with**: Prefix matching
- **between**: Range queries
- **>**, **>=**, **<**, **<=**: Comparison operators

**Cho pattern này**: Sử dụng "begins_with" để get tất cả items có SK bắt đầu với "ORDER#"

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của sort key condition dropdown menu
{{% /notice %}}

### Execute User Orders Query

**Expected Results**:
- User profile (`SK = PROFILE`)
- Tất cả user orders (`SK = ORDER#order001`, etc.)

**Tại sao điều này hoạt động**: Tất cả items với `PK = USER#user001` được lưu trữ cùng nhau và có thể được retrieved trong một query hiệu quả.

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của query results hiển thị profile + orders
{{% /notice %}}

## Pattern 3: Get Order Details with Items

### Hierarchical Data Query

**Mục tiêu**: Get thông tin order hoàn chỉnh bao gồm tất cả items

**Query Configuration**:
- **Partition key (PK)**: `ORDER#order001`
- **Sort key**: Để trống (gets tất cả items trong partition)

**Expected Results**:
- Order details (`SK = DETAILS`)  
- Tất cả order items (`SK = ITEM#laptop001`, `SK = ITEM#book001`)

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của order details query hiển thị order + items
{{% /notice %}}

## Pattern 4: Products by Category (GSI Query)

### Sử dụng Global Secondary Index

**Mục tiêu**: Tìm tất cả products trong một category cụ thể

**Switch to GSI Query**:
1. **Click Query dropdown**: Chọn "Query (index)"
2. **Choose Index**: GSI1
3. **Query the GSI**: Sử dụng GSI key structure

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot hiển thị index selection dropdown với GSI1 được highlighted
{{% /notice %}}

### Configure Category Query

**GSI1 Query Parameters**:
- **GSI1 Partition key**: `CATEGORY#electronics`
- **GSI1 Sort key**: Để trống (gets tất cả products trong category)

**Tại sao điều này hoạt động**: Tất cả electronics products có `GSI1PK = CATEGORY#electronics`

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của GSI1 query setup cho category search
{{% /notice %}}

### Execute Category Query

**Expected Results**: Tất cả products có category = "electronics"

**Thử additional categories**:
- `CATEGORY#books`
- `CATEGORY#clothing` (nếu bạn đã tạo)

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của category query results hiển thị tất cả electronics products
{{% /notice %}}

## Pattern 5: Orders by Status (GSI Query)

### Status-based Filtering

**Mục tiêu**: Tìm tất cả orders với status cụ thể

**GSI2 Query Configuration**:
- **Choose Index**: GSI2
- **GSI2 Partition key**: `STATUS#pending`
- **GSI2 Sort key**: Để trống

**Expected Results**: Tất cả orders với status = "pending"

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của GSI2 query cho order status
{{% /notice %}}

### Thử Different Status Values

**Query other statuses**:
- `STATUS#shipped`
- `STATUS#delivered`
- `STATUS#cancelled`

## Pattern 6: Price Range Queries (GSI Query)

### Range-based Product Search

**Mục tiêu**: Tìm products trong một price range

**GSI2 Query Configuration**:
- **Choose Index**: GSI2  
- **GSI2 Partition key**: `PRICE#500-1000`
- **GSI2 Sort key**: Để trống

**Price Ranges được sử dụng trong Data của chúng ta**:
- `PRICE#10-50` (books, accessories)
- `PRICE#50-200` (mid-range items)
- `PRICE#200-500` (premium items)
- `PRICE#500-1000` (high-end items)

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của price range query hiển thị products trong range
{{% /notice %}}

## Query Performance Analysis

### Monitor Query Efficiency

**Check Performance Metrics**:
1. **Go to**: Metrics tab
2. **Monitor**: Consumed read capacity
3. **Verify**: Không có throttled requests

**Expected Performance**:
- **Single item queries**: ~1-2ms, 1 RCU
- **Multi-item queries**: ~3-5ms, 2-5 RCU
- **GSI queries**: ~2-4ms, 1-3 RCU

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của Metrics tab hiển thị query performance data
{{% /notice %}}

## Advanced Query Techniques

### Combining Conditions

**Sort Key with Multiple Conditions**:
- **Between dates**: `SK between 2025-01-01 and 2025-12-31`
- **Greater than**: `SK > ORDER#order001`
- **Prefix + range**: Complex filtering patterns

### Query Result Options

**Additional Query Settings**:
- **Limit**: Maximum number of items to return
- **Scan index forward**: Sort order (ascending/descending)
- **Projection expression**: Specific attributes to return
- **Filter expression**: Additional filtering after query

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot hiển thị advanced query options và settings
{{% /notice %}}

## Query Pattern Summary

### Patterns bạn đã Mastered

| Pattern | Query Type | Key Structure | Use Case |
|---------|------------|---------------|----------|
| **User Profile** | Table | PK + SK exact | Get specific user |
| **User Orders** | Table | PK + SK prefix | Get user's orders |
| **Order Items** | Table | PK all items | Get order details |
| **Category Products** | GSI1 | Category grouping | Product catalog |
| **Status Orders** | GSI2 | Status grouping | Order management |
| **Price Range** | GSI2 | Price grouping | Product search |

### Query Best Practices

1. **Luôn sử dụng Query**: Không bao giờ sử dụng Scan cho production workloads
2. **Design keys cho queries**: Nghĩ về access patterns trước
3. **Sử dụng GSIs strategically**: Enable multiple query patterns
4. **Monitor performance**: Track consumed capacity và latency
5. **Test query patterns**: Verify chúng return expected results

{{% notice tip %}}
**Key Insight**: Single Table Design enables tất cả các query patterns này với consistent performance và minimal cost. Traditional relational approaches sẽ require multiple queries và JOINs.
{{% /notice %}}

## Query Troubleshooting

### Common Issues

**No Results Returned**:
- ✅ Check partition key spelling
- ✅ Verify sort key conditions
- ✅ Ensure data exists với những keys đó

**Unexpected Results**:
- ✅ Review sort key conditions (exact vs begins_with)
- ✅ Check index selection (table vs GSI)
- ✅ Verify data được tạo correctly

**Performance Issues**:
- ✅ Avoid Scan operations
- ✅ Sử dụng specific partition keys
- ✅ Monitor consumed capacity

## Sẵn sàng cho Global Scale

Bây giờ bạn đã mastered Single Table Design query patterns! Trong module tiếp theo, chúng ta sẽ đưa dữ liệu này lên global bằng cách configuring Global Tables cho multi-region deployment, enabling users worldwide to access your e-commerce platform với low latency.

{{% notice success %}}
**Query Mastery Achieved**: Bây giờ bạn có thể efficiently retrieve data sử dụng tất cả major Single Table Design patterns với consistent performance và cost optimization!
{{% /notice %}}
