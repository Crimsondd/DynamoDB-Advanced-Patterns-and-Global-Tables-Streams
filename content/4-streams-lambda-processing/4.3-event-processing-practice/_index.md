---
title : "4.3 Event Processing Practice"
date : "2025-08-11"
weight : 43
chapter : false
pre : " <b> 4.3 </b> "
---

## Hands-On Stream Processing

🎯 **Practice real-time event processing with various data changes and business scenarios**

### Overview

Put your stream processing setup to work with realistic scenarios. You'll create, modify, and delete items while monitoring how Lambda processes each event in real-time.

### Exercise Setup

**Preparation checklist**:
- ✅ DynamoDB Streams enabled
- ✅ Lambda function deployed
- ✅ Event source mapping active
- ✅ CloudWatch logs accessible

## Exercise 1: INSERT Event Processing

### Step 1: Setup Monitoring Dashboard

**Open multiple browser tabs**:

1. **Tab 1**: DynamoDB Console (Items view)
2. **Tab 2**: Lambda Console (Monitor tab)
3. **Tab 3**: CloudWatch Logs (your function's log group)
4. **Tab 4**: CloudWatch Metrics (for real-time metrics)

{{% notice info %}}
**Screenshot Location**: Add screenshot showing browser tabs setup for monitoring
{{% /notice %}}

### Step 2: Create User Profile

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
5. **Note time**: Record when you clicked create

{{% notice info %}}
**Screenshot Location**: Add screenshot of user creation in DynamoDB with stream test data
{{% /notice %}}

### Step 3: Monitor INSERT Processing

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
**Screenshot Location**: Add screenshot of CloudWatch logs showing INSERT event processing
{{% /notice %}}

### Step 4: Create Product Item

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
**Screenshot Location**: Add screenshot of product creation and corresponding log entry
{{% /notice %}}

### Step 5: Create Order Item

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

### Step 1: Update Order Status

**Test status change processing**:

1. **Find order**: Query for `ORDER#stream-order-001`
2. **Edit item**: Click "Edit" button
3. **Update status**: Change `status` from "pending" to "processing"
4. **Add timestamp**: Update or add `last_modified` field
5. **Save changes**: Click "Save"

{{% notice info %}}
**Screenshot Location**: Add screenshot of order status update in DynamoDB
{{% /notice %}}

### Step 2: Monitor MODIFY Processing

**Check logs for MODIFY event**:

1. **CloudWatch Logs**: Look for new entries
2. **Event type**: "Processing MODIFY event"
3. **Status change**: "Status changed from pending to processing"
4. **Business logic**: Could trigger notifications

{{% notice info %}}
**Screenshot Location**: Add screenshot of CloudWatch logs showing MODIFY event with status change detection
{{% /notice %}}

### Step 3: Update Product Price

**Test price change detection**:

1. **Find product**: `PRODUCT#stream-prod-001`
2. **Edit price**: Change from 299 to 279
3. **Save changes**: Update the item
4. **Check logs**: "Price changed from 299 to 279"

### Step 4: Multiple Field Updates

**Test complex modifications**:

1. **Edit user profile**: Update multiple fields:
   - `name`: "John Stream Updated"
   - `last_login`: "2025-08-11T17:00:00Z"
   - `login_count`: 5
2. **Save changes**: Apply updates
3. **Monitor logs**: Check processing details

## Exercise 3: REMOVE Event Processing

### Step 1: Delete Test Product

**Test deletion processing**:

1. **Find product**: `PRODUCT#stream-prod-001`
2. **Delete item**: Click "Delete" button
3. **Confirm deletion**: Click "Delete" in confirmation dialog
4. **Note time**: Record deletion time

{{% notice info %}}
**Screenshot Location**: Add screenshot of item deletion confirmation dialog
{{% /notice %}}

### Step 2: Monitor REMOVE Processing

**Check deletion logs**:

1. **CloudWatch Logs**: Look for REMOVE event
2. **Event type**: "Processing REMOVE event" 
3. **Business logic**: "Product deleted - could remove from search"
4. **Old image**: Shows deleted item data

{{% notice info %}}
**Screenshot Location**: Add screenshot of CloudWatch logs showing REMOVE event processing
{{% /notice %}}

### Step 3: Cleanup Remaining Items

**Delete test items**:

1. **Delete order**: `ORDER#stream-order-001`
2. **Delete user**: `USER#stream-user-001`
3. **Monitor each**: Check logs for each deletion
4. **Verify processing**: All events processed successfully

## Exercise 4: Batch Processing Analysis

### Step 1: Create Multiple Items Rapidly

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

// Continue with test-003, test-004, test-005...
```

2. **Create rapidly**: Submit each within seconds of each other

{{% notice info %}}
**Screenshot Location**: Add screenshot showing rapid item creation for batch testing
{{% /notice %}}

### Step 2: Analyze Batch Processing

**Check Lambda behavior**:

1. **Monitor tab**: Check invocation count
2. **Fewer invocations**: Should be less than 5 (batching occurred)
3. **CloudWatch Logs**: Look for entries like "Successfully processed 3 records"
4. **Batch efficiency**: Multiple records per invocation

### Step 3: Measure Processing Time

**Performance analysis**:

1. **Duration metrics**: Average processing time per batch
2. **Throughput**: Records processed per second
3. **Cost efficiency**: Fewer invocations = lower cost
4. **Latency**: Time from change to processing

{{% notice info %}}
**Screenshot Location**: Add screenshot of Lambda metrics showing batch processing efficiency
{{% /notice %}}

## Exercise 5: Real-World Scenarios

### Scenario 1: E-commerce Order Flow

**Simulate complete order lifecycle**:

1. **User signup**: Create new user profile
2. **Product browsing**: Create product views (optional)
3. **Add to cart**: Create cart items
4. **Place order**: Create order with "pending" status
5. **Payment**: Update order to "paid"
6. **Fulfillment**: Update to "processing" → "shipped"
7. **Delivery**: Update to "delivered"

**Monitor each step**: Check logs for appropriate business logic triggers

### Scenario 2: Inventory Management

**Test inventory updates**:

1. **Create product**: With initial stock level
2. **Sale occurs**: Decrease stock by purchase quantity
3. **Restock**: Increase stock when inventory arrives
4. **Low stock**: Monitor when stock falls below threshold
5. **Out of stock**: Handle zero inventory scenarios

### Scenario 3: User Activity Tracking

**Profile management scenario**:

1. **Registration**: New user creation
2. **Profile updates**: Name, email, preferences changes
3. **Activity logging**: Login events, page views
4. **Account upgrades**: Change account type
5. **Account deletion**: Remove user data

## Exercise 6: Error Handling Testing

### Step 1: Test Large Item Processing

**Create oversized item** (careful with limits):

```json
{
  "PK": "LARGE#test-item",
  "SK": "DATA",
  "large_data": "[Create a string approaching 400KB limit]",
  "test_type": "size_testing"
}
```

**Monitor**: Check if processing handles large payloads appropriately

### Step 2: Rapid Fire Testing

**Stress test scenario**:

1. **Create 20 items**: As fast as possible
2. **Monitor throttling**: Check for any rate limiting
3. **Processing lag**: Measure iterator age
4. **Error rates**: Ensure no processing failures

### Step 3: Recovery Testing

**Test retry behavior**:

1. **Simulate errors**: (Advanced) Modify Lambda to fail temporarily
2. **Check retries**: Verify automatic retry logic
3. **Dead letter queue**: Monitor failed messages (if configured)
4. **Recovery**: Ensure processing resumes normally

## Performance Metrics Analysis

### Key Metrics to Track

**Lambda Function Metrics**:
- **Invocations**: Total function calls
- **Duration**: Average execution time (target < 1 second)
- **Errors**: Should remain at 0
- **Throttles**: Should be 0 for Free Tier usage

**DynamoDB Stream Metrics**:
- **IncomingRecords**: Rate of changes captured
- **IteratorAge**: Processing delay (target < 1 second)
- **ReadThrottledEvents**: Should be 0

### Optimization Insights

**Processing efficiency**:
- **Batch size impact**: Larger batches = fewer invocations
- **Memory usage**: Monitor to optimize allocation
- **Cold starts**: First invocation may be slower
- **Consistent performance**: Subsequent calls should be fast

{{% notice info %}}
**Screenshot Location**: Add screenshot of comprehensive metrics dashboard showing all key performance indicators
{{% /notice %}}

## Exercise Summary

Through these exercises, you've experienced:

- ✅ **INSERT event processing** with different item types
- ✅ **MODIFY event handling** with change detection
- ✅ **REMOVE event processing** with cleanup logic
- ✅ **Batch processing** for efficiency optimization
- ✅ **Real-world scenarios** for practical application
- ✅ **Performance monitoring** and optimization insights

### Business Logic Patterns Demonstrated

**Event-driven actions you've implemented**:
- User registration → Welcome email trigger
- Product creation → Search index update
- Order placement → Fulfillment notification
- Status changes → Customer notifications
- Price changes → Cache invalidation
- Item deletion → Cleanup operations

{{% notice success %}}
**Stream Processing Mastery**: You now understand how to build responsive, event-driven applications with DynamoDB Streams and Lambda!
{{% /notice %}}

## Next Steps

With hands-on stream processing experience complete, the final section covers monitoring, debugging, and troubleshooting your stream processing pipeline in production scenarios.
