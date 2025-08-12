---
title : "3.4 Replication Monitoring"
date : "2025-08-11"
weight : 34
chapter : false
pre : " <b> 3.4 </b> "
---

## CloudWatch Metrics for Global Tables

🔍 **Monitor replication performance và troubleshoot Global Tables issues**

### Tổng quan

Understanding Global Tables performance là crucial cho production applications. Bạn sẽ học monitor replication latency, track errors, và set up alerts cho global application của bạn.

### Key Metrics to Monitor

**Essential metrics cho Global Tables health**:

- **ReplicationLatency**: Time cho writes to replicate
- **PendingReplicationCount**: Unsynced items awaiting replication  
- **ReplicationMetrics**: Success/failure rates
- **ConsumedReads/Writes**: Per-region capacity usage

## Exercise 1: View Basic Replication Metrics

### Bước 1: Access CloudWatch

**Navigate to monitoring**:

1. **AWS Console**: Search "CloudWatch"
2. **Left sidebar**: Click "Metrics"
3. **Browse metrics**: Click "Browse" tab
4. **Find DynamoDB**: Click "DynamoDB" tile

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của CloudWatch console với DynamoDB metrics selected
{{% /notice %}}

### Bước 2: Locate Global Tables Metrics

**Find replication metrics**:

1. **Metric categories**: Look for "Global Table Metrics"
2. **Table selection**: Choose `demo-ecommerce-freetier`
3. **Region pairs**: See US-East-1 → EU-West-1 metrics
4. **Click checkbox**: Select metrics bạn muốn view

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot showing Global Table metrics selection interface
{{% /notice %}}

### Bước 3: Create Replication Dashboard

**Build monitoring dashboard**:

1. **Select metrics**:
   - `ReplicationLatency` for both region pairs
   - `PendingReplicationCount` for both directions
2. **Time range**: Set to "Last 1 hour"
3. **Refresh**: Set to "1 minute"
4. **Add to dashboard**: Click "Add to dashboard"

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của CloudWatch dashboard showing replication latency graphs
{{% /notice %}}

## Exercise 2: Generate Load and Monitor

### Bước 1: Batch Create Items

**Create monitoring load** với script pattern này:

```bash
# PowerShell script to create multiple items
for ($i=1; $i -le 20; $i++) {
    # Create item in US-East-1
    echo "Creating item $i"
    # Use AWS CLI or console to create items
}
```

**Manual approach**:

1. **US-East-1**: Create 10 products rapidly
2. **EU-West-1**: Create 10 users rapidly  
3. **Both regions**: Create orders referencing both
4. **Monitor**: Watch metrics update

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot showing multiple items being created in rapid succession
{{% /notice %}}

### Bước 2: Watch Replication Latency

**Observe real-time metrics**:

1. **Refresh CloudWatch**: Every 30 seconds
2. **Latency spikes**: Look for increased replication time
3. **Pending count**: Should spike then decrease
4. **Normal range**: Usually < 2 seconds

**Expected patterns**:
- **Initial spike**: High pending count
- **Gradual decline**: Items replicate  
- **Return to baseline**: All items synced

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của CloudWatch graph showing replication latency spike and recovery
{{% /notice %}}

### Bước 3: Cross-Region Verification

**Check replication completion**:

1. **Count items**: Query trong mỗi region
2. **Compare totals**: Should be identical
3. **Random sampling**: Verify specific items exist
4. **Timing analysis**: Note total replication time

## Exercise 3: Set Up Monitoring Alerts

### Bước 1: Create Latency Alert

**High replication latency alarm**:

1. **CloudWatch**: Go to "Alarms" → "Create alarm"
2. **Select metric**: DynamoDB → Global Table → ReplicationLatency
3. **Configuration**:
   - **Statistic**: Average
   - **Period**: 5 minutes
   - **Threshold**: Greater than 10 seconds
   - **Datapoints**: 2 out of 3

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của alarm creation interface với replication latency settings
{{% /notice %}}

### Bước 2: Create Pending Count Alert

**Stuck replication alarm**:

1. **Create new alarm**: For PendingReplicationCount
2. **Configuration**:
   - **Statistic**: Maximum
   - **Period**: 5 minutes  
   - **Threshold**: Greater than 100 items
   - **Datapoints**: 3 out of 3

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot showing pending replication count alarm configuration
{{% /notice %}}

### Bước 3: Configure Notifications

**Set up alert actions**:

1. **SNS Topic**: Create "DynamoDB-GlobalTables-Alerts"
2. **Email subscription**: Add email của bạn
3. **Confirm subscription**: Check email và confirm
4. **Attach to alarms**: Add SNS action to both alarms

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của SNS topic creation và email subscription setup
{{% /notice %}}

## Exercise 4: Regional Performance Analysis

### Bước 1: Compare Regional Metrics

**Analyze per-region performance**:

1. **Create dashboard**: "Global Tables Regional Analysis"
2. **Add metrics for each region**:
   - ConsumedReadCapacityUnits
   - ConsumedWriteCapacityUnits  
   - ThrottledRequests
   - SuccessfulRequestLatency

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của regional performance comparison dashboard
{{% /notice %}}

### Bước 2: Identify Usage Patterns

**Look for patterns**:

- **Read distribution**: Which regions get more reads?
- **Write distribution**: Which regions get more writes?
- **Peak times**: When is each region busiest?
- **Throttling**: Any capacity issues?

### Bước 3: Capacity Planning Insights

**Use metrics for planning**:

1. **Peak usage**: Note highest consumption
2. **Regional differences**: Compare usage patterns
3. **Growth trends**: Look at usage over time
4. **Capacity needs**: Plan for scaling

## Exercise 5: Troubleshooting Scenarios

### Scenario 1: High Replication Latency

**Simulated problem investigation**:

1. **Symptoms**: Latency > 30 seconds consistently
2. **Check metrics**: PendingReplicationCount growing
3. **Investigate**:
   - Large item sizes?
   - Network issues?
   - High write volume?
   - Regional capacity throttling?

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot showing troubleshooting dashboard với high latency indicators
{{% /notice %}}

### Scenario 2: Replication Failures

**Error investigation**:

1. **Metrics to check**:
   - ReplicationLatency (would be missing data points)
   - DynamoDB error logs
   - Failed request counts
2. **Common causes**:
   - Item too large (>400KB)
   - Provisioned capacity exceeded
   - Service limits reached

### Scenario 3: Inconsistent Data

**Data consistency issues**:

1. **Symptoms**: Different data trong different regions
2. **Investigation steps**:
   - Check replication lag
   - Verify conflict resolution
   - Review application write patterns
   - Check for failed replications

## Exercise 6: Performance Optimization

### Bước 1: Identify Bottlenecks

**Analysis checklist**:

- **Item sizes**: Large items replicate slowly
- **Hot partitions**: Uneven write distribution
- **Write patterns**: Batch vs individual writes
- **Regional capacity**: Adequate trong all regions

### Bước 2: Optimization Strategies

**Performance improvements**:

1. **Reduce item size**: Remove unnecessary attributes
2. **Batch operations**: Group writes when possible
3. **Distribute load**: Avoid hot partitions
4. **Capacity planning**: Right-size each region

### Bước 3: Monitor Improvements

**Track optimization results**:

1. **Before/after metrics**: Compare replication latency
2. **Capacity utilization**: More even distribution
3. **Error rates**: Should decrease
4. **Application performance**: Improved response times

## Real-World Monitoring Strategy

### Production Monitoring Checklist

**Essential alerts**:
- ✅ **Replication latency** > 10 seconds
- ✅ **Pending replication** > 100 items  
- ✅ **Failed replications** > 0
- ✅ **Capacity utilization** > 80%
- ✅ **Throttling events** > 0

### Dashboard Organization

**Recommended dashboards**:

1. **Executive summary**: High-level health
2. **Operations dashboard**: Detailed metrics
3. **Regional comparison**: Cross-region analysis
4. **Troubleshooting**: Drill-down views

### Alerting Best Practices

**Alert configuration**:
- **Severity levels**: Critical vs warning
- **Notification timing**: Immediate vs batched
- **Escalation paths**: On-call rotation
- **Runbook links**: Troubleshooting guides

## Cost Monitoring

### Track Global Tables Costs

**Monitor expenses**:

1. **Cost Explorer**: Filter by DynamoDB service
2. **Region breakdown**: See per-region costs
3. **Feature costs**: Global Tables premium
4. **Data transfer**: Cross-region charges

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của Cost Explorer showing DynamoDB Global Tables costs by region
{{% /notice %}}

### Cost Optimization

**Reduce expenses**:
- **Right-size capacity**: Avoid over-provisioning
- **Regional placement**: Optimize for user proximity
- **Data lifecycle**: Archive old data
- **Feature usage**: Evaluate Global Tables necessity

## Integration with Application Monitoring

### Application-Level Metrics

**Track trong application của bạn**:

```javascript
// Example: Track replication lag from application
const metrics = {
  writeRegion: 'us-east-1',
  writeTimestamp: Date.now(),
  readRegion: 'eu-west-1',
  readTimestamp: Date.now(),
  replicationDelay: readTimestamp - writeTimestamp
};
```

### Custom CloudWatch Metrics

**Send application metrics**:

1. **Custom namespace**: "MyApp/GlobalTables"
2. **Key metrics**:
   - Perceived replication lag
   - Cross-region read success rate
   - User experience impact
   - Business KPIs affected

{{% notice warning %}}
**Important**: Monitor both AWS-provided metrics và application-specific performance indicators cho complete visibility.
{{% /notice %}}

## Exercise Summary

Bây giờ bạn đã mastered Global Tables monitoring:

- ✅ **CloudWatch metrics** setup và interpretation
- ✅ **Performance monitoring** dashboards
- ✅ **Alerting configuration** cho proactive management
- ✅ **Troubleshooting** common replication issues
- ✅ **Cost monitoring** cho Global Tables
- ✅ **Integration** với application monitoring

{{% notice success %}}
**Monitoring Mastery**: Bây giờ bạn có thể operate Global Tables confidently trong production với comprehensive observability!
{{% /notice %}}

## Next Steps

Với Global Tables setup và monitoring complete, bạn ready để explore advanced DynamoDB patterns including streams processing, which enables real-time data processing và event-driven architectures.
