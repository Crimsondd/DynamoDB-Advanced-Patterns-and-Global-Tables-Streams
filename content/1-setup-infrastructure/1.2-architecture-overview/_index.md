---
title : "1.2 Architecture Overview"
date : "2025-08-11"
weight : 12
chapter : false
pre : " <b> 1.2 </b> "
---

# Architecture Overview

🏗️ **Understanding the complete infrastructure we'll deploy for the DynamoDB workshop**

## High-Level Architecture

Our workshop infrastructure spans two AWS regions to demonstrate Global Tables functionality while maintaining Free Tier compliance.

![global-tables-architecture](/images/1/global-tables-architecture-improved.png?featherlight=false&width=90pc)

## Core Components

### 1. DynamoDB Global Table

**Primary Table (US-East-1)**:

- **Table Name**: `demo-ecommerce-freetier`
- **Partition Key**: `PK` (String)
- **Sort Key**: `SK` (String)
- **Capacity**: 5 RCU / 5 WCU (Provisioned)
- **Streams**: Enabled (NEW_AND_OLD_IMAGES)
- **Point-in-time Recovery**: Enabled

**Replica Table (EU-West-1)**:

- **Synchronized**: Automatic replication
- **Read Capacity**: 5 RCU
- **Eventual Consistency**: Cross-region
- **Local Reads**: Low latency for EU users

### 2. Lambda Stream Processor

**Function Configuration**:

```python
Runtime: Python 3.9
Memory: 128 MB (Free Tier optimized)
Timeout: 30 seconds
Environment: Demo
Trigger: DynamoDB Streams
```

**Processing Logic**:

- **Stream Records**: Process INSERT, MODIFY, REMOVE events
- **Data Validation**: Ensure data integrity
- **Audit Logging**: Track all changes
- **Error Handling**: Dead letter queue for failed records

### 3. CloudWatch Monitoring

**Dashboard Components**:

- **DynamoDB Metrics**: Read/Write capacity utilization
- **Lambda Metrics**: Invocation count, duration, errors
- **Cost Tracking**: Real-time Free Tier usage
- **Performance**: Latency and throughput metrics

**Billing Alarms**:

- **Alert at $1.00**: Early warning system
- **Alert at 80% Free Tier**: Usage monitoring
- **Email Notifications**: Immediate awareness

## Data Model Architecture

### Single Table Design Pattern

Our e-commerce platform uses a single DynamoDB table with multiple entity types:

```text
Entity Types and Access Patterns:
┌─────────────────┬─────────────────┬─────────────────┬─────────────────┐
│ Entity Type     │ Partition Key   │ Sort Key        │ Purpose         │
├─────────────────┼─────────────────┼─────────────────┼─────────────────┤
│ User Profile    │ USER#id         │ PROFILE         │ User metadata   │
│ User Addresses  │ USER#id         │ ADDRESS#id      │ Shipping info   │
│ Product         │ PRODUCT#id      │ DETAILS         │ Product catalog │
│ Product Reviews │ PRODUCT#id      │ REVIEW#user_id  │ Customer reviews│
│ Order Header    │ ORDER#id        │ DETAILS         │ Order metadata  │
│ Order Items     │ ORDER#id        │ ITEM#product_id │ Order contents  │
│ Category        │ CATEGORY#id     │ DETAILS         │ Product groups  │
└─────────────────┴─────────────────┴─────────────────┴─────────────────┘
```

### Access Patterns

1. **Get User Profile**: `PK=USER#123, SK=PROFILE`
2. **Get User's Orders**: `PK=USER#123, SK begins_with ORDER#`
3. **Get Product Details**: `PK=PRODUCT#456, SK=DETAILS`
4. **Get Order with Items**: `PK=ORDER#789, SK begins_with ITEM#`
5. **Get Product Reviews**: `PK=PRODUCT#456, SK begins_with REVIEW#`

## Security Architecture

### IAM Roles and Policies

**Lambda Execution Role**:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:DescribeStream",
        "dynamodb:GetRecords",
        "dynamodb:GetShardIterator",
        "dynamodb:ListStreams"
      ],
      "Resource": "arn:aws:dynamodb:*:*:table/demo-ecommerce-freetier/stream/*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:*:*:*"
    }
  ]
}
```

**Principle of Least Privilege**:

- Lambda: Only stream read permissions
- CloudWatch: Only metrics and logs write
- DynamoDB: Table-specific access only

## Network Architecture

### Regional Deployment Strategy

**Primary Region (US-East-1)**:

- **Availability**: 99.99% SLA
- **Latency**: <10ms for US users
- **Capacity**: Full read/write operations
- **Backup**: Point-in-time recovery

**Secondary Region (EU-West-1)**:

- **Availability**: 99.99% SLA (independent)
- **Latency**: <10ms for EU users
- **Capacity**: Read optimized
- **Sync**: Eventually consistent replication

### Data Flow

1. **Write Operations**: Always to primary region
2. **Read Operations**: Can be from either region
3. **Replication**: Automatic cross-region sync
4. **Conflict Resolution**: Last-writer-wins
5. **Failover**: Manual promotion if needed

## Cost Architecture

### Free Tier Optimization

**DynamoDB Costs**:

```text
Primary Table (US-East-1):
- Provisioned RCU: 5 units (Free: 25) = $0.00
- Provisioned WCU: 5 units (Free: 25) = $0.00
- Storage: <1 GB (Free: 25 GB) = $0.00

Replica Table (EU-West-1):
- Provisioned RCU: 5 units (Free: 25) = $0.00
- Cross-region replication: <1 GB/month = $0.00
```

**Lambda Costs**:

```text
Stream Processor:
- Invocations: ~100/day (Free: 1M/month) = $0.00
- Duration: 128MB × 1s × 100 = <1 GB-second = $0.00
```

**Total Workshop Cost**: **$0.00** ✅

## Scalability Considerations

### Horizontal Scaling

**DynamoDB**:

- **Auto Scaling**: Can enable if needed
- **On-Demand**: Switch from provisioned
- **Global Secondary Indexes**: Add for new access patterns

**Lambda**:

- **Concurrent Executions**: Up to 1000 default
- **Dead Letter Queue**: Handle failures
- **Reserved Concurrency**: Control scaling

### Performance Optimization

**Read Performance**:

- **Consistent Reads**: When data consistency required
- **Eventually Consistent**: For better performance
- **DAX**: DynamoDB Accelerator for caching

**Write Performance**:

- **Batch Operations**: Reduce API calls
- **Parallel Writes**: Multiple partition keys
- **Write Sharding**: Distribute hot partitions

## Monitoring and Observability

### Key Metrics to Monitor

**DynamoDB**:

- **Consumed Read Capacity**: Target <80% of provisioned
- **Consumed Write Capacity**: Target <80% of provisioned
- **Throttled Requests**: Should be 0
- **Error Rate**: Target <1%

**Lambda**:

- **Invocation Count**: Track processing volume
- **Duration**: Monitor performance trends
- **Error Rate**: Target <1%
- **Dead Letter Queue**: Monitor failed records

### Alerting Strategy

**Critical Alerts**:

- DynamoDB throttling events
- Lambda function errors
- Billing threshold exceeded
- Free Tier limit approaching

**Warning Alerts**:

- High capacity utilization (>70%)
- Increased error rates
- Performance degradation

{{% notice info %}}
**Architecture Ready**: This architecture provides a production-ready foundation for learning DynamoDB advanced patterns while staying within AWS Free Tier limits.
{{% /notice %}}

## Next Steps

With the architecture understanding complete, we'll now proceed to deploy this infrastructure using CloudFormation templates in the next section.
