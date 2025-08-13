---
title : "6.2 Conditional Updates"
date : "2025-08-11"
weight : 62
chapter : false
pre : " <b> 6.2 </b> "
---

# Conditional Updates

🛡️ **Prevent race conditions with conditional updates**

## Overview

Conditional updates ensure data integrity by only updating items when specific conditions are met. This prevents race conditions in multi-user applications.

### The Problem: Race Conditions

**Without conditions (dangerous)**:
```text
User A reads: stock = 1
User B reads: stock = 1  
User A updates: stock = 0  ✅
User B updates: stock = 0  ❌ (Should fail!)
Result: Oversold!
```

**With conditions (safe)**:
```text
User A: stock = 0 (condition: stock >= 1) ✅
User B: fails (condition: stock >= 1) ❌
Result: Only first succeeds ✅
```

## Exercise 1: Basic Conditional Updates

### Step 1: Create Test Product

**Create a product for testing**:

```bash
aws dynamodb put-item \
  --table-name demo-ecommerce-freetier \
  --item '{
    "PK": {"S": "PRODUCT#conditional-test"},
    "SK": {"S": "DETAILS"},
    "name": {"S": "Test Product"},
    "price": {"N": "100"},
    "stock": {"N": "5"}
  }'
```

![6.2.1](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.1.png?featherlight=false&width=90pc)
![6.2.1(1)](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.1(1).png?featherlight=false&width=90pc)

### Step 2: Safe Price Update

**Update price with condition**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET price = :new_price" \
  --condition-expression "price = :current_price" \
  --expression-attribute-values '{
    ":new_price": {"N": "90"},
    ":current_price": {"N": "100"}
  }'
```

![6.2.2](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.2.png?featherlight=false&width=90pc)

### Step 3: Failed Conditional Update

**Try update with wrong condition**:

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

**Result**: ConditionalCheckFailedException error

![6.2.3](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.3.png?featherlight=false&width=90pc)

## Exercise 2: Prevent Overselling

### Step 1: Safe Stock Decrement

**Buy 2 items safely**:

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

![6.2.4](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.4.png?featherlight=false&width=90pc)

### Step 2: Prevent Overselling

**Try to buy more than available**:

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

**Result**: Fails because only 3 items in stock

![6.2.5](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.5.png?featherlight=false&width=90pc)

## Exercise 3: Version Control

### Step 1: Add Version to Product

**Update product with version number**:

```bash
aws dynamodb update-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#conditional-test"},"SK":{"S":"DETAILS"}}' \
  --update-expression "SET version = :version" \
  --expression-attribute-values '{
    ":version": {"N": "1"}
  }'
```

### Step 2: Version-Based Update

**Update with version check**:

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

![6.2.6](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.6.png?featherlight=false&width=90pc)

### Step 3: Version Conflict

**Try update with old version**:

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

**Result**: Fails because version is now 2, not 1

![6.2.7](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.7.png?featherlight=false&width=90pc)

## Exercise 4: Prevent Duplicates

### Step 1: Create Unique User

**Create user only if doesn't exist**:

```bash
aws dynamodb put-item \
  --table-name demo-ecommerce-freetier \
  --item '{
    "PK": {"S": "USER#unique-user"},
    "SK": {"S": "PROFILE"},
    "email": {"S": "user@example.com"}
  }' \
  --condition-expression "attribute_not_exists(PK)"
```

### Step 2: Try Duplicate

**Try to create same user again**:

```bash
aws dynamodb put-item \
  --table-name demo-ecommerce-freetier \
  --item '{
    "PK": {"S": "USER#unique-user"},
    "SK": {"S": "PROFILE"}, 
    "email": {"S": "different@example.com"}
  }' \
  --condition-expression "attribute_not_exists(PK)"
```

**Result**: Fails - prevents duplicate users

![6.2.8](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.8.png?featherlight=false&width=90pc)

### Step 3: Complex Conditions

**Update with multiple conditions**:

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

![6.2.9](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.2.9.png?featherlight=false&width=90pc)

## Monitoring Conditional Updates

**Check CloudWatch metrics**:

1. **ConditionalCheckFailedException**: Failed condition rate
2. **SuccessfulRequestLatency**: Update performance  
3. **ThrottledRequests**: Capacity issues

**Screenshot Location**: Add screenshot of CloudWatch showing conditional update metrics

## Key Benefits

- **Prevents race conditions**: Multiple users can't oversell inventory
- **Data integrity**: Ensures business rules are enforced
- **Version control**: Prevents overwriting concurrent changes
- **Duplicate prevention**: Unique constraints without database locks

{{% notice success %}}
**Conditional Updates Mastery**: You can now safely handle concurrent access and prevent data corruption!
{{% /notice %}}

## Next Steps

You've learned essential conditional update patterns for production applications.
