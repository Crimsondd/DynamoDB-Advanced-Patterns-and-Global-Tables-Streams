---
title : "6.3 Advanced Query Techniques"
date : "2025-08-11"
weight : 63
chapter : false
pre : " <b> 6.3 </b> "
---

## Performance Optimization and Cost Efficiency

⚡ **Master advanced querying techniques to optimize performance, reduce costs, and improve application responsiveness**

### Overview

Advanced query techniques are crucial for building high-performance, cost-effective DynamoDB applications. Learn how to minimize RCU consumption, reduce data transfer, and optimize query patterns for production workloads.

### Query Optimization Fundamentals

**Key optimization areas**:
- **Projection Expressions**: Fetch only needed attributes
- **Filter Expressions**: Refine results server-side
- **Pagination**: Handle large result sets efficiently
- **Index Optimization**: Choose the right index for each query
- **Parallel Processing**: Scale query throughput

### Performance vs Cost Balance

```
Query Optimization Impact:
┌─────────────────────┬─────────────────────┬─────────────────┐
│ Technique           │ Performance Gain    │ Cost Reduction  │
├─────────────────────┼─────────────────────┼─────────────────┤
│ Projection          │ Faster response     │ 50-80% RCU     │
│ Proper indexing     │ 10x faster queries  │ Avoid scans     │
│ Pagination          │ Consistent latency  │ Memory control  │
│ Parallel queries    │ Higher throughput   │ Better scaling  │
└─────────────────────┴─────────────────────┴─────────────────┘
```

## Exercise 1: Projection Expression Optimization

### Step 1: Full Item Query (Baseline)

**Compare full item retrieval vs optimized projection**:

1. **AWS CloudShell**: Open CloudShell from AWS Console
2. **Query without projection** (retrieves all attributes):

```bash
aws dynamodb query \
  --table-name demo-ecommerce-freetier \
  --key-condition-expression "PK = :pk" \
  --expression-attribute-values '{
    ":pk": {"S": "PRODUCT#conditional-test"}
  }'
```

3. **Note response size**: Large payload with all attributes

{{% notice info %}}
**Screenshot Location**: Add screenshot showing full item query with complete response payload
{{% /notice %}}

### Step 2: Optimized Projection Query

**Fetch only essential attributes**:

1. **Query with projection expression**:

```bash
aws dynamodb query \
  --table-name demo-ecommerce-freetier \
  --key-condition-expression "PK = :pk" \
  --projection-expression "PK, SK, #n, price, stock" \
  --expression-attribute-names '{"#n": "name"}' \
  --expression-attribute-values '{
    ":pk": {"S": "PRODUCT#conditional-test"}
  }'
```

2. **Compare response sizes**:
   - **Full query**: All attributes returned
   - **Projected query**: Only specified attributes
   - **RCU savings**: Proportional to size reduction

{{% notice info %}}
**Screenshot Location**: Add screenshot comparing full vs projected query responses
{{% /notice %}}

### Step 3: GSI Projection Optimization

**Optimize GSI queries with smart projections**:

1. **Query GSI with projection**:

```bash
aws dynamodb query \
  --table-name demo-ecommerce-freetier \
  --index-name GSI1 \
  --key-condition-expression "GSI1PK = :gsi1pk" \
  --projection-expression "GSI1PK, GSI1SK, #n, price" \
  --expression-attribute-names '{"#n": "name"}' \
  --expression-attribute-values '{
    ":gsi1pk": {"S": "CATEGORY#electronics"}
  }'
```

2. **Performance benefits**:
   - **Faster queries**: Less data transfer
   - **Lower RCU costs**: Pay only for retrieved data
   - **Better user experience**: Quicker page loads

{{% notice info %}}
**Screenshot Location**: Add screenshot showing GSI query with projection optimization
{{% /notice %}}

## Exercise 2: Filter Expression Techniques

### Step 1: Basic Filter Expressions

**Apply server-side filtering to refine results**:

1. **Query with price range filter**:

```bash
aws dynamodb query \
  --table-name demo-ecommerce-freetier \
  --index-name GSI1 \
  --key-condition-expression "GSI1PK = :gsi1pk" \
  --filter-expression "price BETWEEN :min_price AND :max_price" \
  --expression-attribute-values '{
    ":gsi1pk": {"S": "CATEGORY#electronics"},
    ":min_price": {"N": "50"},
    ":max_price": {"N": "200"}
  }'
```

2. **Important**: Filters don't reduce RCU (applied after reading)
3. **Use case**: When you need subset of results

{{% notice info %}}
**Screenshot Location**: Add screenshot showing filtered query results within price range
{{% /notice %}}

### Step 2: Complex Filter Conditions

**Combine multiple filter criteria**:

1. **Multi-condition filter**:

```bash
aws dynamodb query \
  --table-name demo-ecommerce-freetier \
  --index-name GSI1 \
  --key-condition-expression "GSI1PK = :gsi1pk" \
  --filter-expression "price < :max_price AND attribute_exists(stock) AND stock > :min_stock" \
  --expression-attribute-values '{
    ":gsi1pk": {"S": "CATEGORY#electronics"},
    ":max_price": {"N": "300"},
    ":min_stock": {"N": "0"}
  }'
```

2. **Filter logic**: Price < $300 AND has stock attribute AND stock > 0

### Step 3: Function-Based Filters

**Use DynamoDB filter functions**:

1. **Contains function filter**:

```bash
aws dynamodb query \
  --table-name demo-ecommerce-freetier \
  --index-name GSI1 \
  --key-condition-expression "GSI1PK = :gsi1pk" \
  --filter-expression "contains(#n, :keyword)" \
  --expression-attribute-names '{"#n": "name"}' \
  --expression-attribute-values '{
    ":gsi1pk": {"S": "CATEGORY#electronics"},
    ":keyword": {"S": "Gaming"}
  }'
```

2. **Use case**: Search for products with "Gaming" in the name

{{% notice info %}}
**Screenshot Location**: Add screenshot showing function-based filter results
{{% /notice %}}

## Exercise 3: Pagination and Large Result Sets

### Step 1: Pagination Setup

**Handle large result sets with pagination**:

1. **First page query**:

```bash
aws dynamodb query \
  --table-name demo-ecommerce-freetier \
  --index-name GSI1 \
  --key-condition-expression "GSI1PK = :gsi1pk" \
  --limit 2 \
  --expression-attribute-values '{
    ":gsi1pk": {"S": "CATEGORY#electronics"}
  }'
```

2. **Note LastEvaluatedKey**: Used for next page
3. **Limit parameter**: Controls page size

{{% notice info %}}
**Screenshot Location**: Add screenshot showing first page with LastEvaluatedKey
{{% /notice %}}

### Step 2: Next Page Retrieval

**Continue pagination with ExclusiveStartKey**:

1. **Second page query** (use LastEvaluatedKey from previous response):

```bash
aws dynamodb query \
  --table-name demo-ecommerce-freetier \
  --index-name GSI1 \
  --key-condition-expression "GSI1PK = :gsi1pk" \
  --limit 2 \
  --exclusive-start-key '{"GSI1PK":{"S":"CATEGORY#electronics"},"GSI1SK":{"S":"PRODUCT#batch-keyboard-003"},"PK":{"S":"PRODUCT#batch-keyboard-003"},"SK":{"S":"DETAILS"}}' \
  --expression-attribute-values '{
    ":gsi1pk": {"S": "CATEGORY#electronics"}
  }'
```

2. **Pagination pattern**: Continue until no LastEvaluatedKey returned

### Step 3: Efficient Pagination Implementation

**Production pagination code pattern**:

```python
import boto3

def paginated_query(table_name, index_name, key_condition, page_size=10):
    """
    Efficiently paginate through large query results
    """
    dynamodb = boto3.client('dynamodb')
    
    paginator = dynamodb.get_paginator('query')
    page_iterator = paginator.paginate(
        TableName=table_name,
        IndexName=index_name,
        KeyConditionExpression=key_condition,
        PaginationConfig={'PageSize': page_size}
    )
    
    all_items = []
    for page in page_iterator:
        items = page.get('Items', [])
        all_items.extend(items)
        print(f"Retrieved {len(items)} items from this page")
    
    return all_items

# Usage example
items = paginated_query(
    'demo-ecommerce-freetier',
    'GSI1',
    'GSI1PK = :pk',
    page_size=5
)
```

## Exercise 4: Parallel Query Processing

### Step 1: Single vs Parallel Scan Comparison

**Compare sequential vs parallel processing**:

1. **Standard scan** (single segment):

```bash
aws dynamodb scan \
  --table-name demo-ecommerce-freetier \
  --filter-expression "attribute_exists(price)"
```

2. **Note scan time**: Baseline performance measurement

{{% notice info %}}
**Screenshot Location**: Add screenshot showing single scan execution time
{{% /notice %}}

### Step 2: Parallel Scan Implementation

**Use parallel scan for faster processing**:

1. **Parallel scan segment 1**:

```bash
aws dynamodb scan \
  --table-name demo-ecommerce-freetier \
  --filter-expression "attribute_exists(price)" \
  --segment 0 \
  --total-segments 2
```

2. **Parallel scan segment 2** (run simultaneously):

```bash
aws dynamodb scan \
  --table-name demo-ecommerce-freetier \
  --filter-expression "attribute_exists(price)" \
  --segment 1 \
  --total-segments 2
```

3. **Performance gain**: 2x throughput with 2 segments

### Step 3: Production Parallel Processing

**Implement robust parallel query system**:

```python
import boto3
import concurrent.futures
from typing import List, Dict, Any

class ParallelQueryProcessor:
    def __init__(self, table_name: str):
        self.table_name = table_name
        self.dynamodb = boto3.client('dynamodb')
    
    def parallel_scan(self, total_segments: int = 4, filter_expression: str = None):
        """
        Execute parallel scan across multiple segments
        """
        def scan_segment(segment: int) -> List[Dict]:
            params = {
                'TableName': self.table_name,
                'Segment': segment,
                'TotalSegments': total_segments
            }
            
            if filter_expression:
                params['FilterExpression'] = filter_expression
            
            response = self.dynamodb.scan(**params)
            return response.get('Items', [])
        
        # Execute segments in parallel
        with concurrent.futures.ThreadPoolExecutor(max_workers=total_segments) as executor:
            futures = [
                executor.submit(scan_segment, i) 
                for i in range(total_segments)
            ]
            
            results = []
            for future in concurrent.futures.as_completed(futures):
                segment_results = future.result()
                results.extend(segment_results)
                print(f"Segment completed: {len(segment_results)} items")
        
        return results
    
    def parallel_query_by_partition(self, partition_keys: List[str]):
        """
        Execute parallel queries across different partitions
        """
        def query_partition(pk: str) -> List[Dict]:
            response = self.dynamodb.query(
                TableName=self.table_name,
                KeyConditionExpression='PK = :pk',
                ExpressionAttributeValues={':pk': {'S': pk}}
            )
            return response.get('Items', [])
        
        with concurrent.futures.ThreadPoolExecutor(max_workers=len(partition_keys)) as executor:
            futures = [
                executor.submit(query_partition, pk) 
                for pk in partition_keys
            ]
            
            results = []
            for future in concurrent.futures.as_completed(futures):
                partition_results = future.result()
                results.extend(partition_results)
        
        return results

# Usage example
processor = ParallelQueryProcessor('demo-ecommerce-freetier')

# Parallel scan with 4 segments
items = processor.parallel_scan(total_segments=4)
print(f"Total items retrieved: {len(items)}")

# Parallel queries across partitions
partitions = ['PRODUCT#batch-laptop-001', 'PRODUCT#batch-mouse-002']
items = processor.parallel_query_by_partition(partitions)
```

## Exercise 5: Index Strategy Optimization

### Step 1: Query Pattern Analysis

**Analyze and optimize access patterns**:

1. **Inefficient table scan**:

```bash
aws dynamodb scan \
  --table-name demo-ecommerce-freetier \
  --filter-expression "#s = :status" \
  --expression-attribute-names '{"#s": "status"}' \
  --expression-attribute-values '{":status": {"S": "pending"}}'
```

2. **Performance issues**: Scans entire table (expensive)

{{% notice info %}}
**Screenshot Location**: Add screenshot showing expensive scan operation
{{% /notice %}}

### Step 2: GSI Optimization

**Use appropriate GSI for efficient queries**:

1. **Create status-based GSI** (if not exists):

```bash
# Query using optimized GSI pattern instead
aws dynamodb query \
  --table-name demo-ecommerce-freetier \
  --index-name GSI2 \
  --key-condition-expression "GSI2PK = :status" \
  --expression-attribute-values '{
    ":status": {"S": "STATUS#pending"}
  }'
```

2. **Performance improvement**: Direct key access vs full scan

### Step 3: Sparse Index Patterns

**Implement sparse indexes for efficient filtering**:

1. **Query sparse index** (only items with specific attributes):

```bash
aws dynamodb query \
  --table-name demo-ecommerce-freetier \
  --index-name GSI1 \
  --key-condition-expression "GSI1PK = :category AND begins_with(GSI1SK, :prefix)" \
  --expression-attribute-values '{
    ":category": {"S": "CATEGORY#electronics"},
    ":prefix": {"S": "PRODUCT#"}
  }'
```

2. **Sparse index benefit**: Only indexed items appear in results

{{% notice info %}}
**Screenshot Location**: Add screenshot showing efficient GSI query results
{{% /notice %}}

## Exercise 6: Performance Monitoring and Optimization

### Step 1: Query Performance Metrics

**Monitor query performance in CloudWatch**:

1. **Key metrics to track**:
   - **SuccessfulRequestLatency**: Query response time
   - **ConsumedReadCapacityUnits**: RCU usage per query
   - **ThrottledRequests**: Capacity exceeded events
   - **ItemCount**: Number of items processed

2. **DynamoDB Console**: Navigate to Metrics tab for performance data

{{% notice info %}}
**Screenshot Location**: Add screenshot of CloudWatch metrics showing query performance
{{% /notice %}}

### Step 2: Query Cost Analysis

**Understand and optimize query costs**:

1. **RCU consumption analysis**:

```bash
# Compare RCU usage between operations
aws dynamodb query \
  --table-name demo-ecommerce-freetier \
  --key-condition-expression "PK = :pk" \
  --projection-expression "PK, SK" \
  --expression-attribute-values '{":pk": {"S": "PRODUCT#conditional-test"}}' \
  --return-consumed-capacity TOTAL
```

2. **Optimization strategies**:
   - **Use projections**: Reduce data transfer
   - **Efficient key design**: Minimize query scope
   - **Proper indexing**: Avoid scans
   - **Batch operations**: Reduce API calls

### Step 3: Production Query Patterns

**Implement enterprise-grade query optimization**:

```python
import boto3
import time
from typing import Dict, List, Optional

class OptimizedQueryManager:
    def __init__(self, table_name: str):
        self.table_name = table_name
        self.dynamodb = boto3.client('dynamodb')
        self.performance_stats = {}
    
    def optimized_query(
        self, 
        key_condition: str,
        projection: Optional[str] = None,
        filter_expr: Optional[str] = None,
        index_name: Optional[str] = None
    ) -> Dict:
        """
        Execute optimized query with performance tracking
        """
        start_time = time.time()
        
        params = {
            'TableName': self.table_name,
            'KeyConditionExpression': key_condition,
            'ReturnConsumedCapacity': 'TOTAL'
        }
        
        if projection:
            params['ProjectionExpression'] = projection
        if filter_expr:
            params['FilterExpression'] = filter_expr
        if index_name:
            params['IndexName'] = index_name
        
        try:
            response = self.dynamodb.query(**params)
            
            # Track performance metrics
            duration = time.time() - start_time
            consumed_rcu = response.get('ConsumedCapacity', {}).get('CapacityUnits', 0)
            item_count = response.get('Count', 0)
            
            self.performance_stats = {
                'duration_ms': round(duration * 1000, 2),
                'consumed_rcu': consumed_rcu,
                'items_returned': item_count,
                'efficiency': round(item_count / max(consumed_rcu, 1), 2)
            }
            
            return {
                'items': response.get('Items', []),
                'performance': self.performance_stats,
                'last_key': response.get('LastEvaluatedKey')
            }
            
        except Exception as e:
            return {'error': str(e), 'performance': None}
    
    def compare_query_strategies(self, strategies: List[Dict]) -> Dict:
        """
        Compare multiple query strategies for performance
        """
        results = {}
        
        for strategy in strategies:
            name = strategy.get('name', 'unnamed')
            result = self.optimized_query(**strategy.get('params', {}))
            results[name] = result.get('performance')
        
        return results

# Usage example
query_manager = OptimizedQueryManager('demo-ecommerce-freetier')

# Compare different query approaches
strategies = [
    {
        'name': 'full_scan',
        'params': {
            'key_condition': 'PK = :pk',
        }
    },
    {
        'name': 'projected_query',
        'params': {
            'key_condition': 'PK = :pk',
            'projection': 'PK, SK, name, price'
        }
    },
    {
        'name': 'gsi_query',
        'params': {
            'key_condition': 'GSI1PK = :gsi1pk',
            'index_name': 'GSI1',
            'projection': 'name, price'
        }
    }
]

performance_comparison = query_manager.compare_query_strategies(strategies)
```

## Advanced Query Best Practices

### Optimization Guidelines

**Query performance optimization**:
- **Use projections**: Fetch only needed attributes (50-80% RCU savings)
- **Choose right index**: GSI vs table scan performance difference is 10x+
- **Implement pagination**: Handle large result sets efficiently
- **Monitor RCU usage**: Track consumption patterns

### Cost Efficiency

**Free Tier optimization strategies**:
- **Projection expressions**: Minimize data transfer
- **Efficient key design**: Reduce query scope
- **Batch related queries**: Group similar operations
- **Use sparse indexes**: Index only relevant items

### Performance Patterns

**Production-ready query patterns**:

#### High-Performance Queries
```bash
# Efficient GSI query with projection
QUERY IndexName=GSI1 
WHERE GSI1PK = :category 
PROJECT name, price, stock
```

#### Cost-Optimized Queries
```bash
# Minimal data transfer
QUERY WHERE PK = :product 
PROJECT PK, SK, name
```

#### Scalable Pagination
```bash
# Consistent page sizes
QUERY LIMIT 50 
CONTINUE WITH LastEvaluatedKey
```

{{% notice success %}}
**Query Optimization Mastery**: You've mastered advanced query techniques! Your applications can now achieve optimal performance, minimize costs, and scale efficiently.
{{% /notice %}}

## Exercise Summary

You've mastered advanced query optimization:

- ✅ **Projection expressions** for 50-80% RCU reduction
- ✅ **Filter expressions** for precise result refinement
- ✅ **Pagination patterns** for large dataset handling
- ✅ **Parallel processing** for improved throughput
- ✅ **Index optimization** for 10x+ performance gains
- ✅ **Performance monitoring** for continuous improvement

## Next Steps

Continue to [6.4 Production Patterns](../6.4-production-patterns/) to learn enterprise-grade implementation patterns and best practices for production DynamoDB applications.
