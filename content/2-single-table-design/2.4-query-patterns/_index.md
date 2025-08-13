---
title : "2.4 Query Patterns"
date : "2025-08-11"
weight : 24
chapter : false
pre : " <b> 2.4 </b> "
---

# Query Patterns

🔍 **Learn efficient query techniques using DynamoDB Console**

## Overview

Learn how to retrieve data efficiently using both table queries and Global Secondary Index (GSI) queries.

## Query vs Scan

- **Query**: Fast, efficient, uses primary keys ✅
- **Scan**: Slow, expensive, reads entire table ❌

{{% notice warning %}}
**Important**: Always use Query operations. Scan operations can exceed Free Tier limits.
{{% /notice %}}

## Access Query Interface

1. **Go to**: Items tab in your DynamoDB table
2. **Click**: "Query" button (not Scan)
3. **Select**: Table query or Index query

## Pattern 1: Get User Profile

**Objective**: Get specific user information

**Query Configuration**:
- **Partition key (PK)**: `USER#user001`
- **Sort key (SK)**: `PROFILE`

**Result**: Single item with user profile data

## Pattern 2: Get User's Orders

**Objective**: Get all orders for a user

**Query Configuration**:
- **Partition key (PK)**: `USER#user001`
- **Sort key condition**: "begins_with"
- **Sort key value**: `ORDER#`

**Result**: User profile + all their orders

## Pattern 3: Get Order Details

**Objective**: Get complete order information

**Query Configuration**:
- **Partition key (PK)**: `ORDER#order001`
- **Sort key**: Leave empty (gets all items)

**Result**: Order details + all order items

## Pattern 4: Products by Category (GSI)

**Objective**: Find products in specific category

**Switch to GSI Query**:
1. Click "Query (index)"
2. Choose Index: GSI1

**Query Configuration**:
- **GSI1 Partition key**: `CATEGORY#electronics`
- **GSI1 Sort key**: Leave empty

**Result**: All electronics products

## Pattern 5: Orders by Status (GSI)

**Objective**: Find orders with specific status

**Query Configuration**:
- **Choose Index**: GSI2
- **GSI2 Partition key**: `STATUS#pending`

**Result**: All pending orders

## Query Performance

**Expected Performance**:
- **Single item**: ~1-2ms, 1 RCU
- **Multi-item**: ~3-5ms, 2-5 RCU
- **GSI queries**: ~2-4ms, 1-3 RCU

## Best Practices

1. **Always use Query** (never Scan)
2. **Design keys for queries** first
3. **Use GSIs strategically** for different access patterns
4. **Monitor performance** and consumed capacity

{{% notice success %}}
**Query Mastery**: You can now efficiently retrieve data using Single Table Design patterns!
{{% /notice %}}

## Next Steps

Ready for global scale! Next module covers Global Tables for multi-region deployment.
