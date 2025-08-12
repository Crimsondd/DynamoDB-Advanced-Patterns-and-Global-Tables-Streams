---
title : "4.3 Event Processing Practice"
date : "2025-08-11"
weight : 43
chapter : false
pre : " <b> 4.3 </b> "
---

## Hands-On Stream Processing

🎯 **Practice real-time event processing với various data changes và business scenarios**

### Tổng quan

Put stream processing setup của bạn to work với realistic scenarios. Bạn sẽ create, modify, và delete items trong khi monitoring làm thế nào Lambda processes mỗi event trong real-time.

### Exercise Setup

**Preparation checklist**:
- ✅ DynamoDB Streams enabled
- ✅ Lambda function deployed
- ✅ Event source mapping active
- ✅ CloudWatch logs accessible

## Exercise 1: INSERT Event Processing

### Bước 1: Setup Monitoring Dashboard

**Open multiple browser tabs**:

1. **Tab 1**: DynamoDB Console (Items view)
2. **Tab 2**: Lambda Console (Monitor tab)
3. **Tab 3**: CloudWatch Logs (log group của function bạn)
4. **Tab 4**: CloudWatch Metrics (cho real-time metrics)

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot showing browser tabs setup cho monitoring
{{% /notice %}}

### Bước 2: Create User Profile

**Test user creation event**:

1. **DynamoDB tab**: Navigate to Items
2. **Create item**: Click "Create item"
3. **User data**:

```json
{
  "PK": "USER#stream-user-001",
  "SK": "PROFILE",
  "GSI1PK": "USER#john.stream@example.com",
  "GSI1SK": "PROFILE",
  "user_id": "stream-user-001",
  "name": "John Stream",
  "email": "john.stream@example.com", 
  "registration_date": "2025-08-11T16:45:00Z",
  "account_type": "standard",
  "stream_test": true
}
```

4. **Create**: Save the item
5. **Note time**: Record khi bạn clicked create

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của user creation trong DynamoDB với stream test data
{{% /notice %}}

### Bước 3: Monitor INSERT Processing

**Check Lambda execution**:

1. **Lambda tab**: Refresh Monitor tab
2. **Invocations**: Should increase by 1
3. **Duration**: Note processing time
4. **Success rate**: Should be 100%

**Check processing logs**:

1. **CloudWatch Logs tab**: Refresh log stream
2. **Look for**: "Processing INSERT for item: USER#stream-user-001#PROFILE"
3. **Business logic**: "New user created - could send welcome email"
4. **Success message**: "Successfully processed 1 records"

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của CloudWatch logs showing INSERT event processing
{{% /notice %}}

### Bước 4: Create Product Item

**Test product creation**:

```json
{
  "PK": "PRODUCT#stream-prod-001",
  "SK": "DETAILS",
  "GSI1PK": "CATEGORY#electronics",
  "GSI1SK": "PRODUCT#stream-prod-001",
  "GSI2PK": "PRICE#100-500",
  "GSI2SK": "PRODUCT#stream-prod-001",
  "product_id": "stream-prod-001",
  "name": "Stream Test Smartphone",
  "description": "Test product for stream processing",
  "category": "electronics",
  "price": 299,
  "stock": 50,
  "created_date": "2025-08-11T16:50:00Z",
  "stream_test": true
}
```

**Expected log output**: "New product created - could update search index"

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của product creation và corresponding log entry
{{% /notice %}}

### Bước 5: Create Order Item

**Test order creation**:

```json
{
  "PK": "USER#stream-user-001",
  "SK": "ORDER#stream-order-001",
  "GSI1PK": "ORDER#stream-order-001",
  "GSI1SK": "DETAILS",
  "GSI2PK": "STATUS#pending",
  "GSI2SK": "ORDER#stream-order-001",
  "order_id": "stream-order-001",
  "user_id": "stream-user-001",
  "product_id": "stream-prod-001",
  "quantity": 1,
  "total_amount": 299,
  "status": "pending",
  "order_date": "2025-08-11T16:55:00Z",
  "stream_test": true
}
```

**Expected log output**: "New order created - could trigger fulfillment"

## Exercise 2: MODIFY Event Processing

### Bước 1: Update Order Status

**Test status change processing**:

1. **Find order**: Query cho `ORDER#stream-order-001`
2. **Edit item**: Click "Edit" button
3. **Update status**: Change `status` từ "pending" to "processing"
4. **Add timestamp**: Update hoặc add `last_modified` field
5. **Save changes**: Click "Save"

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của order status update trong DynamoDB
{{% /notice %}}

### Bước 2: Monitor MODIFY Processing

**Check logs cho MODIFY event**:

1. **CloudWatch Logs**: Look for new entries
2. **Event type**: "Processing MODIFY event"
3. **Status change**: "Status changed from pending to processing"
4. **Business logic**: Could trigger notifications

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của CloudWatch logs showing MODIFY event với status change detection
{{% /notice %}}

### Bước 3: Update Product Price

**Test price change detection**:

1. **Find product**: `PRODUCT#stream-prod-001`
2. **Edit price**: Change từ 299 to 279
3. **Save changes**: Update the item
4. **Check logs**: "Price changed from 299 to 279"

### Bước 4: Multiple Field Updates

**Test complex modifications**:

1. **Edit user profile**: Update multiple fields:
   - `name`: "John Stream Updated"
   - `last_login`: "2025-08-11T17:00:00Z"
   - `login_count`: 5
2. **Save changes**: Apply updates
3. **Monitor logs**: Check processing details

## Exercise 3: REMOVE Event Processing

### Bước 1: Delete Test Product

**Test deletion processing**:

1. **Find product**: `PRODUCT#stream-prod-001`
2. **Delete item**: Click "Delete" button
3. **Confirm deletion**: Click "Delete" trong confirmation dialog
4. **Note time**: Record deletion time

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của item deletion confirmation dialog
{{% /notice %}}

### Bước 2: Monitor REMOVE Processing

**Check deletion logs**:

1. **CloudWatch Logs**: Look for REMOVE event
2. **Event type**: "Processing REMOVE event" 
3. **Business logic**: "Product deleted - could remove from search"
4. **Old image**: Shows deleted item data

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của CloudWatch logs showing REMOVE event processing
{{% /notice %}}

### Bước 3: Cleanup Remaining Items

**Delete test items**:

1. **Delete order**: `ORDER#stream-order-001`
2. **Delete user**: `USER#stream-user-001`
3. **Monitor each**: Check logs cho each deletion
4. **Verify processing**: All events processed successfully

## Exercise 4: Batch Processing Analysis

### Bước 1: Create Multiple Items Rapidly

**Test batch processing**:

1. **Create 5 items quickly** (within 30 seconds):

```json
// Item 1
{
  "PK": "BATCH#test-001",
  "SK": "DATA",
  "test_type": "batch_processing",
  "created_at": "2025-08-11T17:10:00Z"
}

// Item 2
{
  "PK": "BATCH#test-002", 
  "SK": "DATA",
  "test_type": "batch_processing",
  "created_at": "2025-08-11T17:10:15Z"
}

// Continue với test-003, test-004, test-005...
```

2. **Create rapidly**: Submit each within seconds của nhau

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot showing rapid item creation cho batch testing
{{% /notice %}}

### Bước 2: Analyze Batch Processing

**Check Lambda behavior**:

1. **Monitor tab**: Check invocation count
2. **Fewer invocations**: Should be less than 5 (batching occurred)
3. **CloudWatch Logs**: Look for entries như "Successfully processed 3 records"
4. **Batch efficiency**: Multiple records per invocation

### Bước 3: Measure Processing Time

**Performance analysis**:

1. **Duration metrics**: Average processing time per batch
2. **Throughput**: Records processed per second
3. **Cost efficiency**: Fewer invocations = lower cost
4. **Latency**: Time từ change to processing

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của Lambda metrics showing batch processing efficiency
{{% /notice %}}

## Exercise 5: Real-World Scenarios

### Scenario 1: E-commerce Order Flow

**Simulate complete order lifecycle**:

1. **User signup**: Create new user profile
2. **Product browsing**: Create product views (optional)
3. **Add to cart**: Create cart items
4. **Place order**: Create order với "pending" status
5. **Payment**: Update order to "paid"
6. **Fulfillment**: Update to "processing" → "shipped"
7. **Delivery**: Update to "delivered"

**Monitor each step**: Check logs cho appropriate business logic triggers

### Scenario 2: Inventory Management

**Test inventory updates**:

1. **Create product**: Với initial stock level
2. **Sale occurs**: Decrease stock by purchase quantity
3. **Restock**: Increase stock khi inventory arrives
4. **Low stock**: Monitor khi stock falls below threshold
5. **Out of stock**: Handle zero inventory scenarios

### Scenario 3: User Activity Tracking

**Profile management scenario**:

1. **Registration**: New user creation
2. **Profile updates**: Name, email, preferences changes
3. **Activity logging**: Login events, page views
4. **Account upgrades**: Change account type
5. **Account deletion**: Remove user data

## Exercise 6: Error Handling Testing

### Bước 1: Test Large Item Processing

**Create oversized item** (careful với limits):

```json
{
  "PK": "LARGE#test-item",
  "SK": "DATA",
  "large_data": "[Create a string approaching 400KB limit]",
  "test_type": "size_testing"
}
```

**Monitor**: Check nếu processing handles large payloads appropriately

### Bước 2: Rapid Fire Testing

**Stress test scenario**:

1. **Create 20 items**: As fast as possible
2. **Monitor throttling**: Check cho any rate limiting
3. **Processing lag**: Measure iterator age
4. **Error rates**: Ensure no processing failures

### Bước 3: Recovery Testing

**Test retry behavior**:

1. **Simulate errors**: (Advanced) Modify Lambda để fail temporarily
2. **Check retries**: Verify automatic retry logic
3. **Dead letter queue**: Monitor failed messages (nếu configured)
4. **Recovery**: Ensure processing resumes normally

## Performance Metrics Analysis

### Key Metrics to Track

**Lambda Function Metrics**:
- **Invocations**: Total function calls
- **Duration**: Average execution time (target < 1 second)
- **Errors**: Should remain at 0
- **Throttles**: Should be 0 cho Free Tier usage

**DynamoDB Stream Metrics**:
- **IncomingRecords**: Rate của changes captured
- **IteratorAge**: Processing delay (target < 1 second)
- **ReadThrottledEvents**: Should be 0

### Optimization Insights

**Processing efficiency**:
- **Batch size impact**: Larger batches = fewer invocations
- **Memory usage**: Monitor để optimize allocation
- **Cold starts**: First invocation có thể slower
- **Consistent performance**: Subsequent calls should be fast

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của comprehensive metrics dashboard showing all key performance indicators
{{% /notice %}}

## Exercise Summary

Through các exercises này, bạn đã experienced:

- ✅ **INSERT event processing** với different item types
- ✅ **MODIFY event handling** với change detection
- ✅ **REMOVE event processing** với cleanup logic
- ✅ **Batch processing** cho efficiency optimization
- ✅ **Real-world scenarios** cho practical application
- ✅ **Performance monitoring** và optimization insights

### Business Logic Patterns Demonstrated

**Event-driven actions bạn đã implemented**:
- User registration → Welcome email trigger
- Product creation → Search index update
- Order placement → Fulfillment notification
- Status changes → Customer notifications
- Price changes → Cache invalidation
- Item deletion → Cleanup operations

{{% notice success %}}
**Stream Processing Mastery**: Bây giờ bạn understand làm thế nào để build responsive, event-driven applications với DynamoDB Streams và Lambda!
{{% /notice %}}

## Next Steps

Với hands-on stream processing experience complete, final section covers monitoring, debugging, và troubleshooting stream processing pipeline của bạn trong production scenarios.
