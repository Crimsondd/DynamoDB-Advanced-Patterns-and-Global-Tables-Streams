---
title : "6.1 Batch Operations"
date : "2025-08-11"
weight : 61
chapter : false
pre : " <b> 6.1 </b> "
---

## Efficient Multi-Item Processing

⚡ **Master batch operations to process multiple items with single API calls for maximum efficiency**

### Overview

Batch operations are the key to efficient DynamoDB usage. Instead of making individual API calls for each item, batch operations allow you to process up to 25 items in a single request, dramatically reducing latency and improving performance.

### Batch Operation Types

**Available batch operations**:
- **BatchWriteItem**: Insert or delete up to 25 items
- **BatchGetItem**: Retrieve up to 100 items
- **Reduced API calls**: Fewer round trips to DynamoDB
- **Lower latency**: Bulk processing efficiency

### Performance Comparison

```
Single Operations (Inefficient):
- 100 items = 100 API calls
- Higher latency per operation
- More request units consumed

Batch Operations (Efficient):
- 100 items = 4 API calls (25 items each)
- Lower overall latency
- Optimal request unit usage
```

## Exercise 1: Batch Write Operations

### Step 1: Create Batch Product Data

**Prepare batch data for multiple product insertion**:

1. **AWS CloudShell**: Open AWS CloudShell from the AWS Console
2. **Create batch file**: Generate JSON for multiple products

**Create batch-products.json file**:

```bash
cat > batch-products.json << 'EOF'
{
  "demo-ecommerce-freetier": [
    {
      "PutRequest": {
        "Item": {
          "PK": {"S": "PRODUCT#batch-laptop-001"},
          "SK": {"S": "DETAILS"},
          "GSI1PK": {"S": "CATEGORY#electronics"},
          "GSI1SK": {"S": "PRODUCT#batch-laptop-001"},
          "name": {"S": "Gaming Laptop Pro"},
          "price": {"N": "1299"},
          "stock": {"N": "15"},
          "category": {"S": "electronics"},
          "brand": {"S": "TechPro"}
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "PK": {"S": "PRODUCT#batch-mouse-002"},
          "SK": {"S": "DETAILS"},
          "GSI1PK": {"S": "CATEGORY#electronics"},
          "GSI1SK": {"S": "PRODUCT#batch-mouse-002"},
          "name": {"S": "Wireless Gaming Mouse"},
          "price": {"N": "79"},
          "stock": {"N": "50"},
          "category": {"S": "electronics"},
          "brand": {"S": "GamerGear"}
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "PK": {"S": "PRODUCT#batch-keyboard-003"},
          "SK": {"S": "DETAILS"},
          "GSI1PK": {"S": "CATEGORY#electronics"},
          "GSI1SK": {"S": "PRODUCT#batch-keyboard-003"},
          "name": {"S": "Mechanical Keyboard RGB"},
          "price": {"N": "159"},
          "stock": {"N": "25"},
          "category": {"S": "electronics"},
          "brand": {"S": "KeyMaster"}
        }
      }
    }
  ]
}
EOF
```

{{% notice info %}}
**Screenshot Location**: Add screenshot of CloudShell with the batch-products.json file created
{{% /notice %}}

### Step 2: Execute Batch Write

**Perform batch write operation**:

1. **Execute batch write command**:

```bash
aws dynamodb batch-write-item --request-items file://batch-products.json
```

2. **Verify successful execution**:
   - Check for no error messages
   - Note the response showing processed items
   - All 3 items created with single API call

{{% notice info %}}
**Screenshot Location**: Add screenshot showing successful batch write response in CloudShell
{{% /notice %}}

### Step 3: Verify Batch Creation

**Confirm items were created in DynamoDB Console**:

1. **DynamoDB Console**: Navigate to DynamoDB service
2. **Select table**: Click on `demo-ecommerce-freetier` table
3. **View items**: Click "Explore table items"
4. **Verify batch items**: Look for the 3 newly created products

**Items to verify**:
- **PRODUCT#batch-laptop-001**: Gaming Laptop Pro
- **PRODUCT#batch-mouse-002**: Wireless Gaming Mouse  
- **PRODUCT#batch-keyboard-003**: Mechanical Keyboard RGB

{{% notice info %}}
**Screenshot Location**: Add screenshot of DynamoDB console showing the 3 batch-created items
{{% /notice %}}

## Exercise 2: Batch Read Operations

### Step 1: Create Batch Get Request

**Prepare batch read operation**:

1. **Create batch-get.json file**:

```bash
cat > batch-get.json << 'EOF'
{
  "demo-ecommerce-freetier": {
    "Keys": [
      {
        "PK": {"S": "PRODUCT#batch-laptop-001"},
        "SK": {"S": "DETAILS"}
      },
      {
        "PK": {"S": "PRODUCT#batch-mouse-002"},
        "SK": {"S": "DETAILS"}
      },
      {
        "PK": {"S": "PRODUCT#batch-keyboard-003"},
        "SK": {"S": "DETAILS"}
      }
    ]
  }
}
EOF
```

### Step 2: Execute Batch Get

**Retrieve multiple items with single API call**:

1. **Execute batch get command**:

```bash
aws dynamodb batch-get-item --request-items file://batch-get.json
```

2. **Analyze response**:
   - All 3 items returned in single response
   - Complete item data for each product
   - Much faster than 3 separate GetItem calls

{{% notice info %}}
**Screenshot Location**: Add screenshot of batch get response showing all 3 items retrieved at once
{{% /notice %}}

### Step 3: Performance Comparison

**Compare batch vs individual operations**:

**Single GetItem (for comparison)**:
```bash
# Single item retrieval (less efficient)
aws dynamodb get-item \
  --table-name demo-ecommerce-freetier \
  --key '{"PK":{"S":"PRODUCT#batch-laptop-001"},"SK":{"S":"DETAILS"}}'
```

**Performance analysis**:
- **Batch operation**: 1 API call for 3 items
- **Individual operations**: 3 API calls for 3 items
- **Efficiency gain**: 3x fewer API calls
- **Latency reduction**: Significant improvement

{{% notice success %}}
**Performance Benefit**: Batch operations reduce API calls by up to 25x for writes and 100x for reads, dramatically improving application performance!
{{% /notice %}}

## Exercise 3: Advanced Batch Patterns

### Step 1: Mixed Batch Operations

**Combine PutRequest and DeleteRequest in single batch**:

1. **Create mixed-batch.json**:

```bash
cat > mixed-batch.json << 'EOF'
{
  "demo-ecommerce-freetier": [
    {
      "PutRequest": {
        "Item": {
          "PK": {"S": "PRODUCT#batch-headset-004"},
          "SK": {"S": "DETAILS"},
          "GSI1PK": {"S": "CATEGORY#electronics"},
          "GSI1SK": {"S": "PRODUCT#batch-headset-004"},
          "name": {"S": "Wireless Headset Pro"},
          "price": {"N": "199"},
          "stock": {"N": "20"},
          "category": {"S": "electronics"}
        }
      }
    },
    {
      "DeleteRequest": {
        "Key": {
          "PK": {"S": "PRODUCT#batch-mouse-002"},
          "SK": {"S": "DETAILS"}
        }
      }
    }
  ]
}
EOF
```

2. **Execute mixed batch operation**:

```bash
aws dynamodb batch-write-item --request-items file://mixed-batch.json
```

{{% notice info %}}
**Screenshot Location**: Add screenshot showing mixed batch operation execution (add + delete)
{{% /notice %}}

### Step 2: Error Handling in Batch Operations

**Understanding batch operation limitations**:

1. **Create oversized batch** (to demonstrate limits):

```bash
# Create a batch with more than 25 items (will be split automatically by AWS CLI)
cat > large-batch.json << 'EOF'
{
  "demo-ecommerce-freetier": [
EOF

# Add multiple items to demonstrate batch limits
for i in {1..30}; do
  cat >> large-batch.json << EOF
    {
      "PutRequest": {
        "Item": {
          "PK": {"S": "PRODUCT#batch-test-$i"},
          "SK": {"S": "DETAILS"},
          "name": {"S": "Test Product $i"},
          "price": {"N": "10"}
        }
      }
    }$([ $i -lt 30 ] && echo ",")
EOF
done

cat >> large-batch.json << 'EOF'
  ]
}
EOF
```

2. **Execute and observe AWS CLI handling**:

```bash
aws dynamodb batch-write-item --request-items file://large-batch.json
```

**Batch operation best practices**:
- **Maximum 25 items** per BatchWriteItem request
- **Maximum 100 items** per BatchGetItem request
- **Handle UnprocessedItems** in response for retries
- **Implement exponential backoff** for failed items

### Step 3: Batch Operations with Projections

**Optimize batch reads with projection expressions**:

1. **Create projection batch get**:

```bash
cat > batch-get-projection.json << 'EOF'
{
  "demo-ecommerce-freetier": {
    "Keys": [
      {
        "PK": {"S": "PRODUCT#batch-laptop-001"},
        "SK": {"S": "DETAILS"}
      },
      {
        "PK": {"S": "PRODUCT#batch-keyboard-003"},
        "SK": {"S": "DETAILS"}
      },
      {
        "PK": {"S": "PRODUCT#batch-headset-004"},
        "SK": {"S": "DETAILS"}
      }
    ],
    "ProjectionExpression": "PK, SK, #n, price, stock",
    "ExpressionAttributeNames": {
      "#n": "name"
    }
  }
}
EOF
```

2. **Execute optimized batch get**:

```bash
aws dynamodb batch-get-item --request-items file://batch-get-projection.json
```

**Benefits of projection in batch operations**:
- **Reduced data transfer**: Only fetch needed attributes
- **Lower RCU consumption**: Pay only for retrieved data
- **Faster response times**: Less data to process
- **Network efficiency**: Smaller payload sizes

{{% notice info %}}
**Screenshot Location**: Add screenshot comparing full item vs projected item responses
{{% /notice %}}

## Exercise 4: Production Batch Patterns

### Step 1: Batch Processing with Error Handling

**Implement robust batch processing**:

```python
import boto3
import json
import time
from botocore.exceptions import ClientError

class BatchProcessor:
    def __init__(self, table_name):
        self.dynamodb = boto3.client('dynamodb')
        self.table_name = table_name
    
    def batch_write_with_retry(self, items, max_retries=3):
        """
        Batch write with automatic retry for unprocessed items
        """
        request_items = {self.table_name: items}
        
        for attempt in range(max_retries):
            try:
                response = self.dynamodb.batch_write_item(
                    RequestItems=request_items
                )
                
                # Check for unprocessed items
                unprocessed = response.get('UnprocessedItems', {})
                if not unprocessed:
                    return True  # All items processed successfully
                
                # Retry unprocessed items with exponential backoff
                wait_time = (2 ** attempt) * 0.1
                time.sleep(wait_time)
                request_items = unprocessed
                
            except ClientError as e:
                print(f"Batch write error (attempt {attempt + 1}): {e}")
                if attempt == max_retries - 1:
                    raise
                time.sleep(2 ** attempt)
        
        return False  # Some items remained unprocessed
    
    def batch_get_with_projection(self, keys, projection_expression=None):
        """
        Batch get with optional projection
        """
        request_items = {
            self.table_name: {'Keys': keys}
        }
        
        if projection_expression:
            request_items[self.table_name]['ProjectionExpression'] = projection_expression
        
        response = self.dynamodb.batch_get_item(RequestItems=request_items)
        return response.get('Responses', {}).get(self.table_name, [])
```

### Step 2: Batch Operation Monitoring

**Monitor batch operation performance**:

1. **CloudWatch metrics to monitor**:
   - **UserErrors**: Failed batch operations
   - **SystemErrors**: DynamoDB-side issues  
   - **ThrottledRequests**: Capacity exceeded
   - **ConsumedReadCapacityUnits**: RCU usage
   - **ConsumedWriteCapacityUnits**: WCU usage

2. **Performance optimization**:
   - **Batch size tuning**: Use maximum batch sizes
   - **Parallel processing**: Multiple concurrent batches
   - **Error handling**: Implement retry logic
   - **Monitoring**: Track success rates and latency

{{% notice info %}}
**Screenshot Location**: Add screenshot of CloudWatch metrics showing batch operation performance
{{% /notice %}}

### Step 3: Cost Optimization with Batching

**Maximize Free Tier efficiency**:

**Cost comparison**:
```
Individual Operations:
- 100 items × 1 WCU each = 100 WCU
- 100 separate API calls
- Higher latency

Batch Operations:  
- 100 items ÷ 25 per batch = 4 API calls
- Same 100 WCU total
- 25x fewer API calls
- Lower latency
```

**Free Tier benefits**:
- **Efficient RCU/WCU usage**: Same capacity consumption
- **Reduced API overhead**: Fewer requests
- **Better throughput**: More items processed per second
- **Improved user experience**: Faster application response

## Batch Operations Best Practices

### Efficiency Guidelines

**Maximize batch effectiveness**:
- **Use maximum batch sizes**: 25 items for writes, 100 for reads
- **Group related operations**: Batch similar operations together
- **Implement retry logic**: Handle UnprocessedItems properly
- **Monitor performance**: Track batch success rates

### Error Handling

**Robust batch processing**:
- **Exponential backoff**: Gradual retry delays
- **Partial success handling**: Process successful items
- **Logging and monitoring**: Track failed operations
- **Graceful degradation**: Fall back to individual operations

### Performance Optimization

**Batch operation tuning**:
- **Parallel batches**: Process multiple batches concurrently
- **Projection expressions**: Reduce data transfer
- **Capacity planning**: Ensure adequate throughput
- **Connection pooling**: Reuse DynamoDB connections

{{% notice success %}}
**Batch Mastery**: You've mastered batch operations! You can now process multiple items efficiently, reduce API calls by up to 25x, and optimize for both performance and cost.
{{% /notice %}}

## Exercise Summary

You've learned batch operation techniques:

- ✅ **Batch write operations** for efficient data insertion
- ✅ **Batch read operations** for fast data retrieval  
- ✅ **Mixed batch operations** combining puts and deletes
- ✅ **Error handling patterns** for robust processing
- ✅ **Performance optimization** with projections
- ✅ **Production patterns** for enterprise applications

## Next Steps

Continue to [6.2 Conditional Updates](../6.2-conditional-updates/) to learn data integrity patterns and prevent race conditions in concurrent environments.
