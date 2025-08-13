---
title : "6.1 Batch Operations"
date : "2025-08-11"
weight : 61
chapter : false
pre : " <b> 6.1 </b> "
---

# Batch Operations

⚡ **Process multiple items efficiently with batch operations**

## Overview

Batch operations allow you to process multiple items in a single API call, reducing latency and improving performance.

### Key Benefits

- **BatchWriteItem**: Insert or delete up to 25 items at once
- **BatchGetItem**: Retrieve up to 100 items at once
- **Fewer API calls**: Better performance and efficiency
- **Lower latency**: Bulk processing reduces round trips

## Exercise 1: Simple Batch Write

### Step 1: Create Batch Product Data

**Use AWS CloudShell for batch operations**:

1. **Open CloudShell**: From AWS Console
2. **Create batch file**: Generate JSON for multiple products

![6.1.1](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.1.1.png?featherlight=false&width=90pc)

```bash
cat > batch-products.json << 'EOF'
{
  "demo-ecommerce-freetier": [
    {
      "PutRequest": {
        "Item": {
          "PK": {"S": "PRODUCT#gaming-laptop"},
          "SK": {"S": "DETAILS"},
          "GSI1PK": {"S": "CATEGORY#electronics"},
          "GSI1SK": {"S": "PRODUCT#gaming-laptop"},
          "name": {"S": "Gaming Laptop Pro"},
          "price": {"N": "1299"},
          "stock": {"N": "15"},
          "category": {"S": "electronics"}
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "PK": {"S": "PRODUCT#wireless-mouse"},
          "SK": {"S": "DETAILS"},
          "GSI1PK": {"S": "CATEGORY#electronics"},
          "GSI1SK": {"S": "PRODUCT#wireless-mouse"},
          "name": {"S": "Wireless Gaming Mouse"},
          "price": {"N": "79"},
          "stock": {"N": "50"},
          "category": {"S": "electronics"}
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "PK": {"S": "PRODUCT#rgb-keyboard"},
          "SK": {"S": "DETAILS"},
          "GSI1PK": {"S": "CATEGORY#electronics"},
          "GSI1SK": {"S": "PRODUCT#rgb-keyboard"},
          "name": {"S": "RGB Mechanical Keyboard"},
          "price": {"N": "159"},
          "stock": {"N": "25"},
          "category": {"S": "electronics"}
        }
      }
    }
  ]
}
EOF
```

### Step 2: Execute Batch Write

**Perform batch write operation**:

```bash
aws dynamodb batch-write-item --request-items file://batch-products.json
```

**Expected result**: All 3 items created with one API call instead of 3 separate calls.

![6.1.2](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.1.2.png?featherlight=false&width=90pc)

### Step 3: Verify Results

**Check DynamoDB Console**:

1. Navigate to your table items
2. Look for the 3 new products:
   - `PRODUCT#gaming-laptop`
   - `PRODUCT#wireless-mouse`
   - `PRODUCT#rgb-keyboard`

![6.1.3](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.1.3.png?featherlight=false&width=90pc)

## Exercise 2: Batch Read Operations

### Step 1: Batch Get Items

**Retrieve multiple items at once**:

```bash
cat > batch-get.json << 'EOF'
{
  "demo-ecommerce-freetier": {
    "Keys": [
      {
        "PK": {"S": "PRODUCT#gaming-laptop"},
        "SK": {"S": "DETAILS"}
      },
      {
        "PK": {"S": "PRODUCT#wireless-mouse"},
        "SK": {"S": "DETAILS"}
      },
      {
        "PK": {"S": "PRODUCT#rgb-keyboard"},
        "SK": {"S": "DETAILS"}
      }
    ]
  }
}
EOF
```

### Step 2: Execute Batch Get

```bash
aws dynamodb batch-get-item --request-items file://batch-get.json
```

![6.1.4](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/6/6.1.4.png?featherlight=false&width=90pc)

**Performance comparison**:

- **Batch operation**: 1 API call for 3 items
- **Individual operations**: 3 API calls for 3 items
- **Result**: 3x fewer API calls = better performance

## Batch Operation Benefits

### Efficiency Gains

| Operation Type | Individual Calls | Batch Calls | Efficiency |
|----------------|------------------|-------------|------------|
| **Write 25 items** | 25 API calls | 1 API call | 25x faster |
| **Read 100 items** | 100 API calls | 1 API call | 100x faster |
| **Mixed operations** | Multiple calls | 1 API call | Much faster |

### Free Tier Benefits

- **Same RCU/WCU usage**: Capacity consumption unchanged
- **Fewer API calls**: Better throughput within limits
- **Reduced latency**: Faster application response
- **Efficient processing**: More work done per request

## Best Practices

### Batch Limits

- **BatchWriteItem**: Maximum 25 items per request
- **BatchGetItem**: Maximum 100 items per request
- **Item size**: Each item still counts toward size limits
- **Error handling**: Some items may fail while others succeed

### When to Use Batching

**Good for batching**:
- Multiple related items to process
- Bulk data imports/exports
- Initialization or cleanup operations

**Not ideal for batching**:
- Single item operations
- Items requiring individual error handling
- Real-time user interactions

{{% notice success %}}
**Batch Operations Mastery**: You can now process multiple items efficiently, reducing API calls and improving application performance!
{{% /notice %}}

## Next Steps

Continue to learn more advanced DynamoDB patterns or proceed to the next workshop module.
