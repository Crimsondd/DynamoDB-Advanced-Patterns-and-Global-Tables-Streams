---
title : "2.3 Create Data Items"
date : "2025-08-11"
weight : 23
chapter : false
pre : " <b> 2.3 </b> "
---

# Create Data Items

📝 **Step-by-step guide to create e-commerce data using Single Table Design patterns**

## Overview

In this section, you'll create the core entities for our e-commerce platform: Users, Products, Orders, and Order Items. Each entity type follows specific key patterns to enable efficient querying.

## Entity Types We'll Create

| Entity | PK Pattern | SK Pattern | Purpose |
|--------|------------|------------|---------|
| **User Profile** | `USER#userID` | `PROFILE` | Store customer information |
| **Product** | `PRODUCT#productID` | `DETAILS` | Store product catalog |
| **Order** | `USER#userID` | `ORDER#orderID` | Link orders to customers |
| **Order Item** | `ORDER#orderID` | `ITEM#productID` | Link products to orders |

## Step 1: Create User Profile

### Access Item Creation

1. **Navigate to**: DynamoDB → Tables → `demo-ecommerce-freetier`
2. **Click**: "Items" tab
3. **Click**: "Create item" button
4. **Switch to**: JSON view

{{% notice info %}}
**Screenshot Location**: Add screenshot of Items tab with "Create item" button highlighted
{{% /notice %}}

### User Profile JSON Template

**Copy and paste this template**, then modify with your details:

```json
{
  "PK": "USER#user001",
  "SK": "PROFILE",
  "GSI1PK": "USER#john.doe@email.com",
  "GSI1SK": "PROFILE",
  "user_id": "user001",
  "name": "John Doe",
  "email": "john.doe@email.com",
  "phone": "+1-555-0123",
  "address": {
    "street": "123 Main St",
    "city": "Seattle",
    "state": "WA",
    "zip": "98101"
  },
  "created_at": "2025-08-11T10:00:00Z",
  "status": "active"
}
```

**Key Patterns Explained**:
- **PK**: `USER#user001` - Groups all user data together
- **SK**: `PROFILE` - Identifies this as the user's profile record
- **GSI1PK**: `USER#john.doe@email.com` - Enables email-based lookups
- **GSI1SK**: `PROFILE` - Maintains consistency in GSI

{{% notice info %}}
**Screenshot Location**: Add screenshot of JSON editor with user profile data entered
{{% /notice %}}

### Save Your User

1. **Review the JSON** for syntax errors
2. **Click "Create item"**
3. **Verify creation**: Item should appear in the table view

{{% notice info %}}
**Screenshot Location**: Add screenshot showing successfully created user item in table view
{{% /notice %}}

## Step 2: Create Products

### Electronics Product Example

**Create your first product** - customize the values:

```json
{
  "PK": "PRODUCT#laptop001",
  "SK": "DETAILS",
  "GSI1PK": "CATEGORY#electronics",
  "GSI1SK": "PRODUCT#laptop001",
  "GSI2PK": "PRICE#500-1000",
  "GSI2SK": "PRODUCT#laptop001",
  "product_id": "laptop001",
  "name": "Professional Laptop",
  "description": "High-performance laptop for professionals",
  "category": "electronics",
  "price": 799,
  "stock": 25,
  "brand": "TechCorp",
  "specifications": {
    "processor": "Intel i7",
    "memory": "16GB RAM",
    "storage": "512GB SSD"
  },
  "created_at": "2025-08-11T10:00:00Z",
  "status": "active"
}
```

**Key Patterns Explained**:
- **GSI1PK**: `CATEGORY#electronics` - Enables category-based queries
- **GSI2PK**: `PRICE#500-1000` - Enables price range queries
- **Nested attributes**: Store complex product details

{{% notice info %}}
**Screenshot Location**: Add screenshot of product creation in JSON view
{{% /notice %}}

### Books Product Example

**Create a second product** in a different category:

```json
{
  "PK": "PRODUCT#book001",
  "SK": "DETAILS",
  "GSI1PK": "CATEGORY#books",
  "GSI1SK": "PRODUCT#book001",
  "GSI2PK": "PRICE#10-50",
  "GSI2SK": "PRODUCT#book001",
  "product_id": "book001",
  "name": "DynamoDB Patterns Guide",
  "description": "Complete guide to DynamoDB design patterns",
  "category": "books",
  "price": 29,
  "stock": 100,
  "author": "Database Expert",
  "isbn": "978-1234567890",
  "pages": 350,
  "created_at": "2025-08-11T10:00:00Z",
  "status": "active"
}
```

**Notice the differences**:
- **Different category**: `CATEGORY#books`
- **Different price range**: `PRICE#10-50`
- **Book-specific attributes**: author, isbn, pages

{{% notice info %}}
**Screenshot Location**: Add screenshot showing both products created in table view
{{% /notice %}}

## Step 3: Create Order

### Order Header

**Create an order linked to your user**:

```json
{
  "PK": "USER#user001",
  "SK": "ORDER#order001",
  "GSI1PK": "ORDER#order001",
  "GSI1SK": "DETAILS",
  "GSI2PK": "STATUS#pending",
  "GSI2SK": "ORDER#order001",
  "order_id": "order001",
  "user_id": "user001",
  "status": "pending",
  "total_amount": 828,
  "tax_amount": 66.24,
  "shipping_cost": 0,
  "shipping_address": {
    "street": "123 Main St",
    "city": "Seattle",
    "state": "WA",
    "zip": "98101"
  },
  "payment_method": "credit_card",
  "created_at": "2025-08-11T11:00:00Z",
  "updated_at": "2025-08-11T11:00:00Z"
}
```

**Key Patterns Explained**:
- **PK**: `USER#user001` - Links order to user (enables "get user's orders")
- **SK**: `ORDER#order001` - Identifies this as an order record
- **GSI1PK**: `ORDER#order001` - Enables direct order lookup
- **GSI2PK**: `STATUS#pending` - Enables status-based queries

{{% notice info %}}
**Screenshot Location**: Add screenshot of order creation showing the relationship structure
{{% /notice %}}

## Step 4: Create Order Items

### First Order Item

**Add the laptop to the order**:

```json
{
  "PK": "ORDER#order001",
  "SK": "ITEM#laptop001",
  "GSI1PK": "PRODUCT#laptop001",
  "GSI1SK": "ORDER#order001",
  "order_id": "order001",
  "product_id": "laptop001",
  "product_name": "Professional Laptop",
  "quantity": 1,
  "unit_price": 799,
  "total_price": 799,
  "added_at": "2025-08-11T11:00:00Z"
}
```

### Second Order Item

**Add the book to the same order**:

```json
{
  "PK": "ORDER#order001",
  "SK": "ITEM#book001",
  "GSI1PK": "PRODUCT#book001",
  "GSI1SK": "ORDER#order001",
  "order_id": "order001",
  "product_id": "book001",
  "product_name": "DynamoDB Patterns Guide",
  "quantity": 1,
  "unit_price": 29,
  "total_price": 29,
  "added_at": "2025-08-11T11:00:00Z"
}
```

**Key Patterns Explained**:
- **PK**: `ORDER#order001` - Groups items with their order
- **SK**: `ITEM#productID` - Identifies specific items
- **GSI1**: Creates product-to-order relationship

{{% notice info %}}
**Screenshot Location**: Add screenshot showing all order items created under the same order
{{% /notice %}}

## Verify Your Data Structure

### Check Table Contents

After creating all items, your table should contain:

```text
Items in Table:
┌─────────────────┬─────────────────┬──────────────────────────┐
│ PK              │ SK              │ Entity Type              │
├─────────────────┼─────────────────┼──────────────────────────┤
│ USER#user001    │ PROFILE         │ User Profile             │
│ USER#user001    │ ORDER#order001  │ Order (linked to user)   │
│ PRODUCT#laptop1 │ DETAILS         │ Product (Electronics)    │
│ PRODUCT#book001 │ DETAILS         │ Product (Books)          │
│ ORDER#order001  │ ITEM#laptop001  │ Order Item (Laptop)      │
│ ORDER#order001  │ ITEM#book001    │ Order Item (Book)        │
└─────────────────┴─────────────────┴──────────────────────────┘
```

{{% notice info %}}
**Screenshot Location**: Add screenshot of complete table view showing all created items
{{% /notice %}}

### Validate Relationships

**Verify these relationships work**:

1. **User → Orders**: `PK=USER#user001` returns profile + orders
2. **Order → Items**: `PK=ORDER#order001` returns order details + items
3. **Category grouping**: GSI1 with `CATEGORY#electronics` returns products
4. **Status grouping**: GSI2 with `STATUS#pending` returns orders

## Data Creation Tips

### JSON Best Practices

1. **Use consistent naming**: Follow the established patterns
2. **Validate JSON syntax**: Check for missing commas, brackets
3. **Include required attributes**: PK, SK, and GSI keys
4. **Use meaningful IDs**: Make them readable and unique

### Common Mistakes to Avoid

- ❌ **Missing GSI keys**: Always populate GSI1PK/GSI1SK and GSI2PK/GSI2SK
- ❌ **Inconsistent patterns**: Stick to `ENTITY#ID` format
- ❌ **Wrong data types**: Use strings for keys, appropriate types for values
- ❌ **JSON syntax errors**: Missing quotes, commas, or brackets

{{% notice warning %}}
**Important**: If you get JSON syntax errors, check your quotation marks, commas, and brackets. The console will highlight syntax issues.
{{% /notice %}}

## Customize Your Data

### Make It Personal

**Customize these values to make the workshop yours**:

- **User names and emails**: Use your own information
- **Product names**: Create products you find interesting
- **Addresses**: Use your city/state
- **Prices**: Realistic values for your products

### Add More Items

**Consider creating additional**:
- More users (friends, family names)
- More products (different categories)
- Additional orders
- Multiple items per order

## Ready for Querying

Now that you have a complete e-commerce dataset with proper Single Table Design patterns, you're ready to explore different query patterns. In the next section, we'll learn how to efficiently retrieve this data using various query techniques.

{{% notice success %}}
**Data Creation Complete**: You've successfully implemented Single Table Design with proper entity relationships, composite keys, and GSI patterns!
{{% /notice %}}
