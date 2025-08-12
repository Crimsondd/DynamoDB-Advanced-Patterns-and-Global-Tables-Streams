---
title : "3.1 Tổng quan Global Tables"
date : "2025-08-11"
weight : 31
chapter : false
pre : " <b> 3.1 </b> "
---

# Tổng quan Global Tables

🌐 **Hiểu về kiến trúc DynamoDB Global Tables và cơ chế replication**

## Global Tables là gì?

Global Tables cho phép bạn tạo một database multi-region, multi-master cung cấp hiệu suất read và write local cho các ứng dụng phân tán toàn cầu. CloudFormation deployment của bạn đã cấu hình sẵn điều này.

## Các thành phần Kiến trúc

### Workshop Setup hiện tại

Infrastructure của bạn đã bao gồm:

| Component | US-East-1 | EU-West-1 | Status |
|-----------|------------|-----------|---------|
| **DynamoDB Table** | Primary | Replica | ✅ Active |
| **Table Name** | `demo-ecommerce-freetier` | `demo-ecommerce-freetier` | ✅ Synced |
| **DynamoDB Streams** | Enabled | Enabled | ✅ Replicating |
| **Replication** | Bi-directional | Bi-directional | ✅ Healthy |

## Quá trình Replication

### Cách dữ liệu Flow giữa các Regions

```text
Write Operation Flow:
┌─────────────────────────────────────────────────────────┐
│ Bước 1: User writes to US-EAST-1                       │
│ ├─ Item: USER#john, SK: PROFILE                        │
│ ├─ Local write: Immediate success                      │
│ └─ Stream record: Created                              │
│                                                         │
│ Bước 2: DynamoDB Streams captures change               │
│ ├─ Stream record: NEW_AND_OLD_IMAGES                   │
│ ├─ Timestamp: 2025-08-11T15:30:00.123Z                │
│ └─ Event: INSERT                                       │
│                                                         │
│ Bước 3: Cross-region replication                       │
│ ├─ Source: us-east-1 stream                            │
│ ├─ Target: eu-west-1 table                             │
│ └─ Latency: 500ms - 2 seconds                          │
│                                                         │
│ Bước 4: EU-WEST-1 receives update                      │
│ ├─ Item appears: USER#john, SK: PROFILE                │
│ ├─ Available for reads: Immediately                    │
│ └─ Status: Replicated ✅                               │
└─────────────────────────────────────────────────────────┘
```

## Consistency Model

### Eventually Consistent

Global Tables cung cấp **eventual consistency** across regions:

- **Immediate**: Write thành công ngay lập tức ở source region
- **Propagation**: Changes replicate đến các regions khác trong 0.5-2 giây
- **Convergence**: Tất cả regions cuối cùng có dữ liệu identical
- **Durability**: Dữ liệu không bao giờ bị mất trong quá trình replication

### Conflict Resolution

Khi cùng một item được modified ở nhiều regions đồng thời:

**Last Writer Wins** strategy:
1. **Compare timestamps** của các conflicting updates
2. **Keep the later timestamp** (thay đổi gần đây hơn)
3. **Overwrite earlier changes** trong tất cả regions
4. **Notify through CloudWatch** metrics

Ví dụ conflict scenario:
```text
Time: 15:30:00 - US user updates: name = "John Smith"
Time: 15:30:01 - EU user updates: name = "John Doe"

Result: Tất cả regions sẽ có name = "John Doe"
(EU update wins do later timestamp)
```

## Global Tables Benefits

### Performance Benefits

- **Local Latency**: Sub-10ms response times trong mỗi region
- **Global Scale**: Serve users worldwide không có performance penalty
- **Load Distribution**: Traffic được distributed across regions

### Availability Benefits

- **Regional Failover**: Automatic failover nếu một region becomes unavailable
- **Disaster Recovery**: Built-in DR across geographic regions
- **99.999% Availability**: Higher availability hơn single-region deployments

### Operational Benefits

- **No Code Changes**: Applications work với bất kỳ region nào
- **Automatic Scaling**: Mỗi region scales independently
- **Unified Management**: Single table view across tất cả regions

## Key Concepts cần nhớ

### Multi-Master Replication
- **Bất kỳ region nào có thể accept writes**
- **Tất cả regions có thể serve reads**
- **Không có single point of failure**

### Stream-Based Replication
- **DynamoDB Streams** power the replication
- **Ordered delivery** ensures consistency
- **Retry logic** handles temporary failures

### Region Independence
- **Mỗi region** operates independently
- **Network partitions** không affect local operations
- **Cross-region connectivity** chỉ cần cho replication

{{% notice tip %}}
**Workshop Advantage**: CloudFormation template của bạn đã configured tất cả Global Tables components. Bạn có thể focus vào understanding và testing functionality!
{{% /notice %}}

## Limitations cần hiểu

### Eventual Consistency Challenges
- **Temporary inconsistencies** có thể xảy ra trong 0.5-2 giây
- **Application design** phải handle eventual consistency
- **Strong consistency** chỉ available trong single region

### Conflict Resolution Limitations
- **Last Writer Wins** có thể overwrite changes
- **Không có custom conflict resolution** logic
- **Application-level** conflict handling có thể cần thiết

### Cross-Region Dependencies
- **Network connectivity** required cho replication
- **Regional outages** có thể delay replication
- **Cross-region latency** affects replication speed

## Real-World Use Cases

### Ideal cho Global Tables

| Use Case | Tại sao It Works | Considerations |
|----------|-------------------|----------------|
| **User Profiles** | Infrequent updates, read-heavy | Handle profile conflicts |
| **Product Catalogs** | Content distribution, global access | Inventory sync challenges |
| **Gaming Leaderboards** | Global competition, eventual consistency OK | Score conflicts possible |
| **IoT Sensor Data** | Time-series data, append-only | High write volume |

### Challenging Scenarios

- **Financial transactions** (require strong consistency)
- **Inventory management** (stock levels need accuracy)
- **Real-time collaboration** (immediate consistency needed)

## Next Steps

Bây giờ bạn đã hiểu Global Tables architecture, hãy verify multi-region setup của bạn và xem replication in action thông qua AWS Console.

{{% notice info %}}
**Ready to Explore**: Global Tables của bạn đã được configured và running. Time to see them in action!
{{% /notice %}}
