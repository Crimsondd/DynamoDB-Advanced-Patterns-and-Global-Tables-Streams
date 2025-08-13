---
title : "3.1 Global Tables Overview"
date : "2025-08-11"
weight : 31
chapter : false
pre : " <b> 3.1 </b> "
---

# Global Tables Overview

🌐 **Understanding DynamoDB Global Tables architecture and replication mechanisms**

## What Are Global Tables?

Global Tables enable you to create a multi-region, multi-master database that provides local read and write performance for globally distributed applications. Your CloudFormation deployment has already configured this for you.

## Architecture Components

### Current Workshop Setup

Your infrastructure already includes:

| Component | US-East-1 | EU-West-1 | Status |
|-----------|------------|-----------|---------|
| **DynamoDB Table** | Primary | Replica | ✅ Active |
| **Table Name** | `demo-ecommerce-freetier` | `demo-ecommerce-freetier` | ✅ Synced |
| **DynamoDB Streams** | Enabled | Enabled | ✅ Replicating |
| **Replication** | Bi-directional | Bi-directional | ✅ Healthy |

## Replication Process

### How Data Flows Between Regions

| Step | Action | Details |
|------|--------|---------|
| **Step 1** | **User writes to US-EAST-1** | |
| | Item | `USER#john, SK: PROFILE` |
| | Local write | Immediate success |
| | Stream record | Created |
| **Step 2** | **DynamoDB Streams captures change** | |
| | Stream record | `NEW_AND_OLD_IMAGES` |
| | Timestamp | `2025-08-11T15:30:00.123Z` |
| | Event | `INSERT` |
| **Step 3** | **Cross-region replication** | |
| | Source | us-east-1 stream |
| | Target | eu-west-1 table |
| | Latency | 500ms - 2 seconds |
| **Step 4** | **EU-WEST-1 receives update** | |
| | Item appears | `USER#john, SK: PROFILE` |
| | Available for reads | Immediately |
| | Status | Replicated ✅ |

## Consistency Model

### Eventually Consistent

Global Tables provides **eventual consistency** across regions:

- **Immediate**: Write succeeds immediately in the source region
- **Propagation**: Changes replicate to other regions within 0.5-2 seconds
- **Convergence**: All regions eventually have identical data
- **Durability**: Data is never lost during replication

### Conflict Resolution

When the same item is modified in multiple regions simultaneously:

**Last Writer Wins** strategy:
1. **Compare timestamps** of conflicting updates
2. **Keep the later timestamp** (more recent change)
3. **Overwrite earlier changes** in all regions
4. **Notify through CloudWatch** metrics

Example conflict scenario:
```text
Time: 15:30:00 - US user updates: name = "John Smith"
Time: 15:30:01 - EU user updates: name = "John Doe"

Result: All regions will have name = "John Doe"
(EU update wins due to later timestamp)
```

## Global Tables Benefits

### Performance Benefits

- **Local Latency**: Sub-10ms response times in each region
- **Global Scale**: Serve users worldwide without performance penalty
- **Load Distribution**: Traffic distributed across regions

### Availability Benefits

- **Regional Failover**: Automatic failover if one region becomes unavailable
- **Disaster Recovery**: Built-in DR across geographic regions
- **99.999% Availability**: Higher availability than single-region deployments

### Operational Benefits

- **No Code Changes**: Applications work with any region
- **Automatic Scaling**: Each region scales independently
- **Unified Management**: Single table view across all regions

## Key Concepts to Remember

### Multi-Master Replication
- **Any region can accept writes**
- **All regions can serve reads**
- **No single point of failure**

### Stream-Based Replication
- **DynamoDB Streams** power the replication
- **Ordered delivery** ensures consistency
- **Retry logic** handles temporary failures

### Region Independence
- **Each region** operates independently
- **Network partitions** don't affect local operations
- **Cross-region connectivity** only needed for replication

{{% notice tip %}}
**Workshop Advantage**: Your CloudFormation template has already configured all Global Tables components. You can focus on understanding and testing the functionality!
{{% /notice %}}

## Limitations to Understand

### Eventual Consistency Challenges
- **Temporary inconsistencies** possible for 0.5-2 seconds
- **Application design** must handle eventual consistency
- **Strong consistency** only available within single region

### Conflict Resolution Limitations
- **Last Writer Wins** can overwrite changes
- **No custom conflict resolution** logic
- **Application-level** conflict handling may be needed

### Cross-Region Dependencies
- **Network connectivity** required for replication
- **Regional outages** can delay replication
- **Cross-region latency** affects replication speed

## Real-World Use Cases

### Ideal for Global Tables

| Use Case | Why It Works | Considerations |
|----------|--------------|----------------|
| **User Profiles** | Infrequent updates, read-heavy | Handle profile conflicts |
| **Product Catalogs** | Content distribution, global access | Inventory sync challenges |
| **Gaming Leaderboards** | Global competition, eventual consistency OK | Score conflicts possible |
| **IoT Sensor Data** | Time-series data, append-only | High write volume |

### Challenging Scenarios

- **Financial transactions** (require strong consistency)
- **Inventory management** (stock levels need accuracy)
- **Real-time collaboration** (immediate consistency needed)

## Next Steps

Now that you understand Global Tables architecture, let's verify your multi-region setup and see replication in action through the AWS Console.

{{% notice info %}}
**Ready to Explore**: Your Global Tables are already configured and running. Time to see them in action!
{{% /notice %}}
