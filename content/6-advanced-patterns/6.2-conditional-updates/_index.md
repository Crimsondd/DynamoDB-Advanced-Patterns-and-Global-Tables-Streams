---
title : "6.2 Conditional Updates"
date : "2025-08-11"
weight : 62
chapter : false
pre : " <b> 6.2 </b> "
---

## Data Integrity and Race Condition Prevention

🛡️ **Master conditional updates to ensure data integrity and prevent race conditions in concurrent environments**

### Overview

Conditional updates are essential for maintaining data integrity in multi-user applications. They prevent race conditions, ensure business rules are enforced, and protect against data corruption when multiple clients access the same data simultaneously.

### The Race Condition Problem

**Scenario: Two users buying the last item**:
```
Without Conditions (Dangerous):
User A reads: stock = 1
User B reads: stock = 1  
User A updates: stock = 0  ✅
User B updates: stock = 0  ❌ (Should fail!)
Result: Both succeed (oversold!)

With Conditions (Safe):
User A reads: stock = 1
User B reads: stock = 1
User A updates: stock = 0 (condition: stock >= 1) ✅
User B updates: stock = 0 (condition: stock >= 1) ❌ FAILS
Result: Only first succeeds ✅
```

### Conditional Expression Types

**Available condition types**:
- **attribute_exists(path)**: Item/attribute must exist
- **attribute_not_exists(path)**: Item/attribute must not exist
- **Comparison operators**: =, <>, <, <=, >, >=
- **BETWEEN and IN**: Range and membership tests
- **Functions**: begins_with(), contains(), size()

## Exercise 1: Basic Conditional Updates

### Step 1: Setup Test Product

**Create a product for conditional update testing**:

1. **AWS CloudShell**: Open CloudShell from AWS Console
2. **Create test product**:

```bash
aws dynamodb put-item \
  --table-name demo-ecommerce-freetier \
  --item '{
    "PK": {"S": "PRODUCT#conditional-test"},
    "SK": {"S": "DETAILS"},
    "GSI1PK": {"S": "CATEGORY#test"},
    "GSI1SK": {"S": "PRODUCT#conditional-test"},
    "name": {"S": "Conditional Test Product"},
    "price": {"N": "100"},
    "stock": {"N": "5"},
    "version": {"N": "1"}
  }'
```

3. **Verify creation**: Check item exists in DynamoDB Console

{{% notice info %}}
**Screenshot Location**: Add screenshot showing the test product created in DynamoDB console
{{% /notice %}}

### Step 2: Price Update with Condition

**Update price only if current price matches expected value**:

1. **Successful conditional update**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET price = :new_price, last_updated = :timestamp" \
  --condition-expression "price = :current_price" \
  --expression-attribute-values '{
    ":new_price": {"N": "90"},
    ":current_price": {"N": "100"},
    ":timestamp": {"S": "2025-08-12T10:00:00Z"}
  }'
```

2. **Verify price change**: Check updated price in console

{{% notice info %}}
**Screenshot Location**: Add screenshot showing successful price update with condition
{{% /notice %}}

### Step 3: Failed Conditional Update

**Attempt update with wrong condition (should fail)**:

1. **Try update with incorrect current price**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET price = :new_price" \
  --condition-expression "price = :wrong_price" \
  --expression-attribute-values '{
    ":new_price": {"N": "80"},
    ":wrong_price": {"N": "100"}
  }'
```

2. **Expected result**: ConditionalCheckFailedException error
3. **Verify price unchanged**: Price should still be 90

{{% notice info %}}
**Screenshot Location**: Add screenshot showing ConditionalCheckFailedException error in CloudShell
{{% /notice %}}

## Exercise 2: Inventory Management with Conditions

### Step 1: Stock Decrement with Safety Check

**Implement safe inventory decrement**:

1. **Purchase 2 items (should succeed)**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET stock = stock - :quantity" \
  --condition-expression "stock >= :quantity" \
  --expression-attribute-values '{
    ":quantity": {"N": "2"}
  }'
```

2. **Verify stock reduction**: Stock should be 3 (was 5, bought 2)

{{% notice info %}}
**Screenshot Location**: Add screenshot showing stock reduced from 5 to 3
{{% /notice %}}

### Step 2: Prevent Overselling

**Attempt to buy more items than available**:

1. **Try to purchase 5 items (should fail)**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET stock = stock - :quantity" \
  --condition-expression "stock >= :quantity" \
  --expression-attribute-values '{
    ":quantity": {"N": "5"}
  }'
```

2. **Expected result**: ConditionalCheckFailedException (only 3 in stock)
3. **Verify stock unchanged**: Stock should still be 3

**This prevents overselling! 🛡️**

{{% notice info %}}
**Screenshot Location**: Add screenshot showing failed purchase attempt due to insufficient stock
{{% /notice %}}

### Step 3: Advanced Inventory Patterns

**Implement complex inventory logic**:

1. **Conditional stock update with minimum threshold**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET stock = stock - :quantity, last_sold = :timestamp" \
  --condition-expression "stock >= :quantity AND stock > :min_threshold" \
  --expression-attribute-values '{
    ":quantity": {"N": "1"},
    ":min_threshold": {"N": "1"},
    ":timestamp": {"S": "2025-08-12T10:05:00Z"}
  }'
```

2. **Business rule**: Keep at least 1 item in stock (reserve inventory)

## Exercise 3: Optimistic Locking Pattern

### Step 1: Version-Based Updates

**Implement optimistic locking with version numbers**:

1. **Read current version**: Note the version number (should be 1)

```bash
aws dynamodb get-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --projection-expression "version"
```

2. **Update with version check**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET price = :new_price, version = version + :inc" \
  --condition-expression "version = :expected_version" \
  --expression-attribute-values '{
    ":new_price": {"N": "85"},
    ":inc": {"N": "1"},
    ":expected_version": {"N": "1"}
  }'
```

3. **Verify version increment**: Version should now be 2

{{% notice info %}}
**Screenshot Location**: Add screenshot showing version incremented from 1 to 2
{{% /notice %}}

### Step 2: Simulate Concurrent Update Conflict

**Demonstrate optimistic locking protection**:

1. **Attempt update with old version** (should fail):

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET price = :new_price, version = version + :inc" \
  --condition-expression "version = :old_version" \
  --expression-attribute-values '{
    ":new_price": {"N": "75"},
    ":inc": {"N": "1"},
    ":old_version": {"N": "1"}
  }'
```

2. **Expected result**: ConditionalCheckFailedException (version is now 2, not 1)
3. **Protection achieved**: Prevents overwriting changes from other users

{{% notice info %}}
**Screenshot Location**: Add screenshot showing version conflict error
{{% /notice %}}

### Step 3: Proper Version Update Flow

**Correct optimistic locking workflow**:

1. **Read current item with version**:

```bash
aws dynamodb get-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --projection-expression "#v, price, #n" \
  --expression-attribute-names '{
    "#v": "version",
    "#n": "name"
  }'
```

2. **Update with current version** (should succeed):

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET price = :new_price, version = version + :inc" \
  --condition-expression "version = :current_version" \
  --expression-attribute-values '{
    ":new_price": {"N": "88"},
    ":inc": {"N": "1"},
    ":current_version": {"N": "2"}
  }'
```

3. **Version now 3**: Successful update with proper version control

## Exercise 4: Advanced Conditional Patterns

### Step 1: Attribute Existence Conditions

**Create items only if they don't exist**:

1. **Create user profile (should succeed)**:

```bash
aws dynamodb put-item \
  --table-name demo-ecommerce-freetier \
  --item '{
    "PK": {"S": "USER#conditional-user"},
    "SK": {"S": "PROFILE"},
    "email": {"S": "user@example.com"},
    "created_at": {"S": "2025-08-12T10:10:00Z"}
  }' \
  --condition-expression "attribute_not_exists(PK)"
```

2. **Try to create same user again** (should fail):

```bash
aws dynamodb put-item \
  --table-name demo-ecommerce-freetier \
  --item '{
    "PK": {"S": "USER#conditional-user"},
    "SK": {"S": "PROFILE"}, 
    "email": {"S": "different@example.com"},
    "created_at": {"S": "2025-08-12T10:15:00Z"}
  }' \
  --condition-expression "attribute_not_exists(PK)"
```

**Result**: Second attempt fails (prevents duplicate users)

{{% notice info %}}
**Screenshot Location**: Add screenshot showing duplicate prevention with attribute_not_exists
{{% /notice %}}

### Step 2: Complex Conditional Logic

**Combine multiple conditions with AND/OR**:

1. **Update product with complex business rules**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET discount_price = :discount" \
  --condition-expression "price > :min_price AND stock > :min_stock" \
  --expression-attribute-values '{
    ":discount": {"N": "70"},
    ":min_price": {"N": "50"},
    ":min_stock": {"N": "0"}
  }'
```

2. **Business rule**: Only apply discount if price > $50 AND stock > 0

### Step 3: Function-Based Conditions

**Use DynamoDB condition functions**:

1. **Update if attribute contains specific value**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET featured = :featured" \
  --condition-expression "contains(#n, :keyword)" \
  --expression-attribute-names '{"#n": "name"}' \
  --expression-attribute-values '{
    ":featured": {"BOOL": true},
    ":keyword": {"S": "Test"}
  }'
```

2. **Condition**: Only feature products with "Test" in the name

{{% notice info %}}
**Screenshot Location**: Add screenshot showing function-based condition success
{{% /notice %}}

## Exercise 5: Production Conditional Patterns

### Step 1: E-commerce Order Processing

**Implement order creation with inventory check**:

```bash
# Create order only if product has sufficient stock
aws dynamodb put-item \
  --table-name demo-ecommerce-freetier \
  --item '{
    "PK": {"S": "ORDER#order-001"},
    "SK": {"S": "DETAILS"},
    "GSI1PK": {"S": "USER#conditional-user"},
    "GSI1SK": {"S": "ORDER#order-001"},
    "product_id": {"S": "PRODUCT#conditional-test"},
    "quantity": {"N": "1"},
    "status": {"S": "pending"},
    "created_at": {"S": "2025-08-12T10:20:00Z"}
  }' \
  --condition-expression "attribute_not_exists(PK)"

# Simultaneously decrement stock
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET stock = stock - :quantity, reserved_stock = if_not_exists(reserved_stock, :zero) + :quantity" \
  --condition-expression "stock >= :quantity" \
  --expression-attribute-values '{
    ":quantity": {"N": "1"},
    ":zero": {"N": "0"}
  }'
```

### Step 2: Error Handling for Conditions

**Handle conditional failures gracefully**:

```python
import boto3
from botocore.exceptions import ClientError

def safe_inventory_decrement(product_id, quantity):
    """
    Safely decrement inventory with proper error handling
    """
    dynamodb = boto3.client('dynamodb')
    
    try:
        response = dynamodb.update_item(
            TableName='demo-ecommerce-freetier',
            Key={
                'PK': {'S': f'PRODUCT#{product_id}'},
                'SK': {'S': 'DETAILS'}
            },
            UpdateExpression='SET stock = stock - :quantity',
            ConditionExpression='stock >= :quantity',
            ExpressionAttributeValues={
                ':quantity': {'N': str(quantity)}
            },
            ReturnValues='ALL_NEW'
        )
        return {'success': True, 'item': response['Attributes']}
        
    except ClientError as e:
        if e.response['Error']['Code'] == 'ConditionalCheckFailedException':
            return {'success': False, 'error': 'Insufficient stock'}
        else:
            return {'success': False, 'error': f'Database error: {e}'}

# Usage example
result = safe_inventory_decrement('conditional-test', 1)
if result['success']:
    print("Stock decremented successfully")
else:
    print(f"Failed: {result['error']}")
```

### Step 3: Performance Monitoring

**Monitor conditional update patterns**:

1. **CloudWatch metrics to track**:
   - **ConditionalCheckFailedException**: Failed condition rate
   - **SuccessfulRequestLatency**: Update performance
   - **ThrottledRequests**: Capacity issues
   - **ItemCollectionMetrics**: Item size growth

2. **Optimize conditional performance**:
   - **Minimize condition complexity**: Simple conditions perform better
   - **Use efficient operators**: = is faster than contains()
   - **Avoid unnecessary conditions**: Only check what's essential
   - **Monitor failure rates**: High failure rates indicate design issues

{{% notice info %}}
**Screenshot Location**: Add screenshot of CloudWatch showing conditional update metrics
{{% /notice %}}

## Conditional Updates Best Practices

### Design Guidelines

**Effective condition design**:
- **Keep conditions simple**: Complex conditions impact performance
- **Use appropriate operators**: Choose the most efficient operator
- **Minimize attribute references**: Fewer attributes = better performance
- **Plan for failures**: Design application logic to handle condition failures

### Common Patterns

**Production-ready conditional patterns**:

#### Optimistic Locking
```bash
# Always increment version on updates
UPDATE SET data = :new_data, version = version + 1
WHERE version = :expected_version
```

#### Inventory Management
```bash
# Prevent overselling
UPDATE SET stock = stock - :quantity
WHERE stock >= :quantity
```

#### Unique Constraints
```bash
# Prevent duplicates
PUT item
WHERE attribute_not_exists(primary_key)
```

#### Business Rules
```bash
# Enforce business logic
UPDATE SET status = :new_status
WHERE current_status = :expected_status AND user_role = :admin
```

### Error Handling

**Robust conditional update handling**:
- **Expect failures**: Conditions are designed to fail sometimes
- **Retry logic**: Implement appropriate retry strategies
- **User feedback**: Provide meaningful error messages
- **Logging**: Track condition failures for analysis

{{% notice success %}}
**Data Integrity Mastery**: You've mastered conditional updates! Your applications can now handle concurrent access safely, prevent race conditions, and maintain data integrity at scale.
{{% /notice %}}

## Exercise Summary

You've implemented conditional update patterns:

- ✅ **Basic conditional updates** with price and stock management
- ✅ **Inventory safety checks** preventing overselling
- ✅ **Optimistic locking** with version control
- ✅ **Attribute existence conditions** for duplicate prevention
- ✅ **Complex conditional logic** with multiple criteria
- ✅ **Production error handling** for robust applications

## Next Steps

Continue to [6.3 Advanced Query Techniques](../6.3-advanced-query-techniques/) to learn performance optimization strategies and advanced querying patterns.
