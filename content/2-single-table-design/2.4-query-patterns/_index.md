---
title : "2.4 Query Patterns"
date : "2025-08-11"
weight : 24
chapter : false
pre : " <b> 2.4 </b> "
---

# Query Patterns

🔍 **Learn efficient query techniques for Single Table Design using DynamoDB Console**

## Overview

Now that you have created your e-commerce data, let's explore the powerful query patterns that make Single Table Design so effective. You'll learn how to retrieve data efficiently using both table queries and Global Secondary Index (GSI) queries.

## Query vs Scan - Critical Difference

### Always Use Query (Not Scan)

- **Query**: Fast, efficient, cost-effective (uses primary keys)
- **Scan**: Slow, expensive, reads entire table (avoid in production)

{{% notice warning %}}
**Important**: Always use Query operations in this workshop. Scan operations are inefficient and can quickly exceed Free Tier limits.
{{% /notice %}}

## Pattern 1: Get User Profile

### Single Item Lookup

**Objective**: Retrieve a specific user's profile information

**Access the Query Interface**:
1. **Go to**: Items tab in your DynamoDB table
2. **Click**: "Query" button (not Scan)
3. **Ensure**: Table query is selected (not index)

### Configure User Profile Query

**Query Parameters**:
- **Partition key (PK)**: `USER#user001`
- **Sort key (SK)**: `PROFILE`
- **Query condition**: Use "equals" (default)

**Expected Result**: Single item containing user profile data

### Execute and Verify

1. **Click "Run"**
2. **Check results**: Should return 1 item
3. **Verify data**: Profile information should be displayed

**Performance**: ~1-2ms latency, 1 RCU consumed


## Pattern 2: Get User's Orders

### One-to-Many Relationship Query

**Objective**: Retrieve all orders for a specific user

**Query Configuration**:
- **Partition key (PK)**: `USER#user001`
- **Sort key condition**: "begins_with"
- **Sort key value**: `ORDER#`

This pattern retrieves both the user profile AND all their orders in a single query.

### Advanced Sort Key Options

**Available sort key conditions**:
- **=** (equals): Exact match
- **begins_with**: Prefix matching
- **between**: Range queries
- **>**, **>=**, **<**, **<=**: Comparison operators

**For this pattern**: Use "begins_with" to get all items where SK starts with "ORDER#"

### Execute User Orders Query

**Expected Results**:
- User profile (`SK = PROFILE`)
- All user orders (`SK = ORDER#order001`, etc.)

**Why this works**: All items with `PK = USER#user001` are stored together and can be retrieved in one efficient query.


## Pattern 3: Get Order Details with Items

### Hierarchical Data Query

**Objective**: Get complete order information including all items

**Query Configuration**:
- **Partition key (PK)**: `ORDER#order001`
- **Sort key**: Leave empty (gets all items in partition)

**Expected Results**:
- Order details (`SK = DETAILS`)  
- All order items (`SK = ITEM#laptop001`, `SK = ITEM#book001`)

## Pattern 4: Products by Category (GSI Query)

### Using Global Secondary Index

**Objective**: Find all products in a specific category

**Switch to GSI Query**:
1. **Click Query dropdown**: Select "Query (index)"
2. **Choose Index**: GSI1
3. **Query the GSI**: Use GSI key structure

### Configure Category Query

**GSI1 Query Parameters**:
- **GSI1 Partition key**: `CATEGORY#electronics`
- **GSI1 Sort key**: Leave empty (gets all products in category)

**Why this works**: All electronics products have `GSI1PK = CATEGORY#electronics`

### Execute Category Query

**Expected Results**: All products where category = "electronics"

**Try additional categories**:
- `CATEGORY#books`
- `CATEGORY#clothing` (if you created any)

## Pattern 5: Orders by Status (GSI Query)

### Status-based Filtering

**Objective**: Find all orders with a specific status

**GSI2 Query Configuration**:
- **Choose Index**: GSI2
- **GSI2 Partition key**: `STATUS#pending`
- **GSI2 Sort key**: Leave empty

**Expected Results**: All orders with status = "pending"

### Try Different Status Values

**Query other statuses**:
- `STATUS#shipped`
- `STATUS#delivered`
- `STATUS#cancelled`

## Pattern 6: Price Range Queries (GSI Query)

### Range-based Product Search

**Objective**: Find products within a price range

**GSI2 Query Configuration**:
- **Choose Index**: GSI2  
- **GSI2 Partition key**: `PRICE#500-1000`
- **GSI2 Sort key**: Leave empty

**Price Ranges Used in Our Data**:
- `PRICE#10-50` (books, accessories)
- `PRICE#50-200` (mid-range items)
- `PRICE#200-500` (premium items)
- `PRICE#500-1000` (high-end items)

## Query Performance Analysis

### Monitor Query Efficiency

**Check Performance Metrics**:
1. **Go to**: Metrics tab
2. **Monitor**: Consumed read capacity
3. **Verify**: No throttled requests

**Expected Performance**:
- **Single item queries**: ~1-2ms, 1 RCU
- **Multi-item queries**: ~3-5ms, 2-5 RCU
- **GSI queries**: ~2-4ms, 1-3 RCU

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


## Query Pattern Summary

### Patterns You've Mastered

| Pattern | Query Type | Key Structure | Use Case |
|---------|------------|---------------|----------|
| **User Profile** | Table | PK + SK exact | Get specific user |
| **User Orders** | Table | PK + SK prefix | Get user's orders |
| **Order Items** | Table | PK all items | Get order details |
| **Category Products** | GSI1 | Category grouping | Product catalog |
| **Status Orders** | GSI2 | Status grouping | Order management |
| **Price Range** | GSI2 | Price grouping | Product search |

### Query Best Practices

1. **Always use Query**: Never use Scan for production workloads
2. **Design keys for queries**: Think about access patterns first
3. **Use GSIs strategically**: Enable multiple query patterns
4. **Monitor performance**: Track consumed capacity and latency
5. **Test query patterns**: Verify they return expected results

{{% notice tip %}}
**Key Insight**: Single Table Design enables all these query patterns with consistent performance and minimal cost. Traditional relational approaches would require multiple queries and JOINs.
{{% /notice %}}

## Query Troubleshooting

### Common Issues

**No Results Returned**:
- ✅ Check partition key spelling
- ✅ Verify sort key conditions
- ✅ Ensure data exists with those keys

**Unexpected Results**:
- ✅ Review sort key conditions (exact vs begins_with)
- ✅ Check index selection (table vs GSI)
- ✅ Verify data was created correctly

**Performance Issues**:
- ✅ Avoid Scan operations
- ✅ Use specific partition keys
- ✅ Monitor consumed capacity

## Ready for Global Scale

You've now mastered Single Table Design query patterns! In the next module, we'll take this data global by configuring Global Tables for multi-region deployment, enabling users worldwide to access your e-commerce platform with low latency.

{{% notice success %}}
**Query Mastery Achieved**: You can now efficiently retrieve data using all major Single Table Design patterns with consistent performance and cost optimization!
{{% /notice %}}
