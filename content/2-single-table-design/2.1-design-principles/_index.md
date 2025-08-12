---
title : "2.1 Design Principles"
date : "2025-08-11"
weight : 21
chapter : false
pre : " <b> 2.1 </b> "
---

# Single Table Design Principles

🎯 **Understanding the core concepts that make Single Table Design powerful**

## The Paradigm Shift

### From Relational to NoSQL Thinking

Traditional relational databases organize data by **entities** (separate tables for Users, Products, Orders). DynamoDB organizes data by **access patterns** (how you'll query the data).

**Relational Approach**:
```text
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│    Users    │    │  Products   │    │   Orders    │
├─────────────┤    ├─────────────┤    ├─────────────┤
│ user_id     │    │ product_id  │    │ order_id    │
│ name        │    │ name        │    │ user_id     │
│ email       │    │ category    │    │ status      │
└─────────────┘    └─────────────┘    └─────────────┘
```

**Single Table Approach**:
```text
┌───────────────────────────────────────────────────────────────┐
│                    One EcommerceTable                         │
├─────────────┬─────────────┬─────────────┬─────────────────────┤
│     PK      │     SK      │   Entity    │    Additional Data  │
├─────────────┼─────────────┼─────────────┼─────────────────────┤
│ USER#user1  │ PROFILE     │ User        │ name, email, phone  │
│ USER#user1  │ ORDER#ord1  │ Order       │ status, total, date │
│ PRODUCT#p1  │ DETAILS     │ Product     │ name, price, stock  │
│ ORDER#ord1  │ ITEM#p1     │ OrderItem   │ quantity, price     │
└─────────────┴─────────────┴─────────────┴─────────────────────┘
```

## Core Principles

### 1. Composite Primary Key Strategy

**Partition Key (PK) + Sort Key (SK)** creates unique item identification and enables relationships:

- **PK**: Groups related items together (like a namespace)
- **SK**: Sorts items within a partition and creates hierarchies
- **Together**: Enable 1-to-1, 1-to-many, and many-to-many relationships

### 2. Entity Namespacing

Use prefixes to create logical separation:

| Entity Type | PK Pattern | SK Pattern | Purpose |
|-------------|------------|------------|---------|
| **User Profile** | `USER#user123` | `PROFILE` | Store user information |
| **User Orders** | `USER#user123` | `ORDER#order456` | Link orders to users |
| **Product Details** | `PRODUCT#prod789` | `DETAILS` | Store product information |
| **Order Items** | `ORDER#order456` | `ITEM#prod789` | Link products to orders |

### 3. Access Pattern First Design

**Start with questions**, then design the key structure:

1. "How will I get user profile?" → `PK=USER#id, SK=PROFILE`
2. "How will I get user's orders?" → `PK=USER#id, SK begins_with ORDER#`
3. "How will I get order details?" → `PK=ORDER#id, SK begins_with ITEM#`
4. "How will I get products by category?" → Use GSI with `CATEGORY#` keys

## Global Secondary Index (GSI) Strategy

### When to Use GSIs

Use GSIs when you need to query data by attributes other than the primary key:

- **Different grouping**: Products by category instead of by product ID
- **Different sorting**: Orders by status instead of by user
- **Cross-entity queries**: All pending orders across all users

### GSI Key Design

**GSI1** - Category-based queries:
```text
GSI1PK: CATEGORY#electronics    GSI1SK: PRODUCT#prod1
GSI1PK: CATEGORY#electronics    GSI1SK: PRODUCT#prod2
GSI1PK: CATEGORY#books         GSI1SK: PRODUCT#prod3
```

**GSI2** - Status/Price-based queries:
```text
GSI2PK: STATUS#pending         GSI2SK: ORDER#order1
GSI2PK: STATUS#shipped         GSI2SK: ORDER#order2
GSI2PK: PRICE#100-500         GSI2SK: PRODUCT#prod1
```

## Benefits in Practice

### Performance Benefits

- **Single Query**: Get user profile + all orders in one query
- **Predictable Latency**: Single-digit millisecond response times
- **No JOINs**: All related data retrieved together
- **Efficient Scaling**: Consistent performance at any scale

### Cost Benefits

- **Fewer Tables**: Lower DynamoDB table costs
- **Fewer Operations**: Batch queries instead of multiple calls
- **Optimized Capacity**: Better utilization of provisioned capacity
- **Reduced Data Transfer**: Less network overhead

### Operational Benefits

- **Atomic Transactions**: Update related items together
- **Simplified Backup**: One table to backup/restore
- **Easier Monitoring**: Single table metrics to track
- **Consistent Security**: One set of IAM policies

## Key Design Patterns

### 1. Adjacency List Pattern
Store related items next to each other:
```text
PK=USER#user1, SK=PROFILE        (User details)
PK=USER#user1, SK=ORDER#order1   (Order 1)
PK=USER#user1, SK=ORDER#order2   (Order 2)
```

### 2. Hierarchical Data Pattern
Use sort key to represent hierarchy:
```text
PK=ORDER#order1, SK=DETAILS      (Order header)
PK=ORDER#order1, SK=ITEM#prod1   (Order item 1)
PK=ORDER#order1, SK=ITEM#prod2   (Order item 2)
```

### 3. GSI Overloading Pattern
Use same GSI for multiple query patterns:
```text
GSI1PK=CATEGORY#electronics, GSI1SK=PRODUCT#prod1
GSI1PK=USER#user1@email.com, GSI1SK=PROFILE
```

{{% notice tip %}}
**Design Rule**: Always start with your access patterns, then design your key structure. Don't start with entities!
{{% /notice %}}

## Common Anti-Patterns to Avoid

### ❌ Don't Use Scan Operations
- **Wrong**: Scan entire table to find items
- **Right**: Use Query with proper key structure

### ❌ Don't Create Too Many GSIs
- **Wrong**: One GSI per query pattern
- **Right**: Overload GSIs for multiple patterns

### ❌ Don't Ignore Hot Partitions
- **Wrong**: All items have same partition key
- **Right**: Distribute items across multiple partitions

### ❌ Don't Use Relational Patterns
- **Wrong**: Normalize data across multiple items
- **Right**: Denormalize related data together

{{% notice warning %}}
**Remember**: Single Table Design requires a mindset shift. Think in terms of access patterns, not entity relationships!
{{% /notice %}}

## Ready for Implementation

Now that you understand the principles, let's move to the AWS Console to see these concepts in action. In the next section, we'll navigate the DynamoDB Console and start creating our e-commerce data model.
