---
title : "3.3 Multi-Region Operations"
date : "2025-08-11"
weight : 33
chapter : false
pre : " <b> 3.3 </b> "
---

# Multi-Region Operations

🌍 **Hands-on practice with cross-region read/write operations and replication testing**

## Overview

Now that your Global Tables are verified, let's experience multi-region operations firsthand. You'll create data in one region, verify it replicates to another, and test conflict resolution scenarios.

## Exercise 1: Write to Primary, Read from Replica

### Step 1: Create Global User in US-East-1

**Ensure you're in US-East-1**:
1. **Check region**: Top-right should show "N. Virginia"
2. **Navigate**: DynamoDB → Tables → `demo-ecommerce-freetier`
3. **Go to**: Items tab
4. **Click**: "Create item"

{{% notice info %}}
**Screenshot Location**: Add screenshot of US-East-1 region with Create item dialog open
{{% /notice %}}

### User Creation Template

**Switch to JSON view** and create:

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

**Important**: Replace `[your-name]` with your actual name to make items unique.

{{% notice info %}}
**Screenshot Location**: Add screenshot of JSON editor with global user data being entered
{{% /notice %}}

### Step 2: Note Creation Time

**Record the details**:
1. **Click "Create item"**
2. **Note the time**: Record when you clicked create
3. **Take screenshot**: Of the created item

{{% notice info %}}
**Screenshot Location**: Add screenshot showing successfully created item in US region
{{% /notice %}}

### Step 3: Switch to EU-West-1

**Change regions**:
1. **Region selector**: Click dropdown (top-right)
2. **Select**: "Europe (Ireland)"
3. **Wait**: For region switch to complete
4. **Navigate**: DynamoDB → Tables → `demo-ecommerce-freetier`

{{% notice info %}}
**Screenshot Location**: Add screenshot of region selector with Europe (Ireland) selected
{{% /notice %}}

### Step 4: Query for Replicated Data

**Search for your user**:
1. **Items tab**: Navigate to items view
2. **Click**: "Query" button
3. **Configure query**:
   - **Partition key (PK)**: `USER#global-user-[your-name]`
   - **Sort key (SK)**: `PROFILE`
4. **Click**: "Run"

{{% notice info %}}
**Screenshot Location**: Add screenshot of query setup in EU region looking for the US-created user
{{% /notice %}}

### Step 5: Verify Replication

**Expected results**:
- **If immediate**: Item appears right away
- **If delayed**: Wait 30-60 seconds and try again
- **Replication time**: Note how long it took

**Verify the data**:
- **All attributes**: Should match exactly
- **region_created**: Should still show "us-east-1"
- **Timestamps**: Should be identical

{{% notice info %}}
**Screenshot Location**: Add screenshot showing the replicated item in EU region with identical data
{{% /notice %}}

## Exercise 2: Write from Replica, Read from Primary

### Step 1: Create Product in EU-West-1

**Stay in EU-West-1** and create a product:

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
**Screenshot Location**: Add screenshot of product creation in EU region
{{% /notice %}}

### Step 2: Switch Back to US-East-1

**Return to primary region**:
1. **Region selector**: "US East (N. Virginia)"
2. **Navigate**: DynamoDB → Tables → Items
3. **Query for product**:
   - **PK**: `PRODUCT#eu-product-[unique-id]`
   - **SK**: `DETAILS`

{{% notice info %}}
**Screenshot Location**: Add screenshot of US region query looking for EU-created product
{{% /notice %}}

### Step 3: Verify Reverse Replication

**Check the results**:
- **Product appears**: In US region
- **region_created**: Still shows "eu-west-1"
- **All data intact**: Exact copy from EU

This demonstrates **bi-directional replication** - you can write to any region!

{{% notice info %}}
**Screenshot Location**: Add screenshot showing EU-created product now visible in US region
{{% /notice %}}

## Exercise 3: Conflict Resolution Testing

### Step 1: Create Base Order

**In US-East-1**, create an order:

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

**Wait 2 minutes** for replication to complete.

{{% notice info %}}
**Screenshot Location**: Add screenshot of conflict test order creation in US region
{{% /notice %}}

### Step 2: Simultaneous Updates (Advanced)

**If working with a partner**:
1. **Partner A**: Update order in US-East-1
2. **Partner B**: Update same order in EU-West-1
3. **Both execute**: Within 10 seconds of each other

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
**Screenshot Location**: Add screenshot showing edit dialog for conflict testing
{{% /notice %}}

### Step 3: Observe Conflict Resolution

**After 2-3 minutes**:
1. **Check both regions**: Query the same order
2. **Compare results**: Which update won?
3. **Understand why**: Later timestamp wins

**Expected outcome**: EU update wins because timestamp 15:40:05 > 15:40:00

{{% notice info %}}
**Screenshot Location**: Add screenshot showing final conflict resolution result in both regions
{{% /notice %}}

## Exercise 4: Query Patterns Across Regions

### Step 1: Category Query in EU

**Test GSI queries** work across regions:
1. **Stay in EU-West-1**
2. **Query Index**: GSI1
3. **GSI1PK**: `CATEGORY#eu-electronics`
4. **Run query**

**Expected**: Shows products created in EU region

{{% notice info %}}
**Screenshot Location**: Add screenshot of GSI category query in EU region
{{% /notice %}}

### Step 2: Status Query in US

**Test cross-region status queries**:
1. **Switch to US-East-1**
2. **Query Index**: GSI2
3. **GSI2PK**: `STATUS#pending`
4. **Run query**

**Expected**: Shows orders from both regions with pending status

{{% notice info %}}
**Screenshot Location**: Add screenshot of GSI status query showing orders from multiple regions
{{% /notice %}}

## Exercise 5: Replication Timing Analysis

### Step 1: Measure Replication Speed

**Create timestamped items**:
1. **Record start time**: Note exact time before creation
2. **Create item**: In one region
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
**Screenshot Location**: Add screenshot showing timing test item creation with precise timestamps
{{% /notice %}}

### Step 2: Document Results

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
4. **App checks**: Profile from EU
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
4. **Fulfillment**: Reads from nearest region

## Performance Monitoring

### Check Replication Metrics

**During exercises**:
1. **Monitor**: CloudWatch metrics
2. **Watch**: Replication latency
3. **Observe**: Pending replication count
4. **Track**: Error rates (should be 0)

{{% notice info %}}
**Screenshot Location**: Add screenshot of CloudWatch metrics showing replication performance during exercises
{{% /notice %}}

## Troubleshooting Guide

### Replication Not Working

**Common issues**:
- **Wrong region**: Double-check region selection
- **Typos in keys**: Exact match required for queries
- **Browser cache**: Refresh page
- **Wait longer**: Up to 2 minutes possible

### Queries Returning Empty

**Checklist**:
- **Correct PK/SK**: Exact string match
- **Region correct**: Item exists in queried region
- **GSI populated**: GSI keys included in item
- **Query type**: Using Query, not Scan

### Conflict Resolution Unexpected

**Understanding**:
- **Timestamp precision**: Millisecond level
- **Clock synchronization**: AWS handles timing
- **Application design**: Plan for overwrites

{{% notice warning %}}
**Important**: If you experience issues, check the Global Tables health status in the console and verify network connectivity.
{{% /notice %}}

## Exercise Summary

By completing these exercises, you've experienced:

- ✅ **Cross-region replication** in both directions
- ✅ **Eventual consistency** timing
- ✅ **Conflict resolution** with Last Writer Wins
- ✅ **Query patterns** working across regions
- ✅ **Real-world scenarios** and timing analysis

{{% notice success %}}
**Multi-Region Mastery**: You now understand how Global Tables enables truly global applications with local performance!
{{% /notice %}}

## Next Steps

With hands-on Global Tables experience complete, let's monitor the replication performance and understand the metrics that help you operate global applications in production.
