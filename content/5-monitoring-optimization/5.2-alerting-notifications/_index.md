---
title : "5.2 Alerting & Notifications"
date : "2025-08-11"
weight : 52
chapter : false
pre : " <b> 5.2 </b> "
---

## Proactive Issue Detection

🚨 **Set up intelligent CloudWatch alarms and notification systems for proactive monitoring**

### Overview

Proactive alerting prevents small issues from becoming big problems. Configure smart alarms that notify you before users experience problems, enabling quick response and maintaining system reliability.

### Alerting Strategy

**Multi-tiered alerting approach**:
- **Critical**: Immediate attention required (system down)
- **Warning**: Issues developing (capacity approaching limits)  
- **Informational**: Trends worth monitoring (usage patterns)

### Alert Design Principles

**Effective alerting characteristics**:
- **Actionable**: Every alert should have a clear response
- **Timely**: Detect issues before user impact
- **Accurate**: Minimize false positives
- **Prioritized**: Critical alerts stand out

## Exercise 1: SNS Topic Setup

### Step 1: Create Notification Topic

**Set up central notification system**:

1. **AWS Console**: Search "SNS" (Simple Notification Service)
2. **Topics**: Click "Topics" in left sidebar
3. **Create topic**: Click "Create topic"
4. **Topic configuration**:
   - **Type**: Standard
   - **Name**: `DynamoDB-Alerts`
   - **Display name**: "DynamoDB Production Alerts"
5. **Create topic**: Click to create

{{% notice info %}}
**Screenshot Location**: Add screenshot of SNS topic creation with DynamoDB-Alerts configuration
{{% /notice %}}

### Step 2: Add Email Subscription

**Configure email notifications**:

1. **Topic details**: Open your created topic
2. **Subscriptions**: Click "Create subscription"
3. **Subscription configuration**:
   - **Protocol**: Email
   - **Endpoint**: Your email address
4. **Create subscription**: Click to create
5. **Confirm subscription**: Check email and click confirmation link

{{% notice info %}}
**Screenshot Location**: Add screenshot of email subscription configuration and confirmation process
{{% /notice %}}

### Step 3: Test Notification System

**Verify notification setup**:

1. **Topic actions**: Click "Publish message"
2. **Test message**:
   - **Subject**: "DynamoDB Alert System Test"
   - **Message**: "This is a test of the DynamoDB alerting system. Please ignore."
3. **Publish**: Send test message
4. **Verify**: Check email reception

{{% notice info %}}
**Screenshot Location**: Add screenshot of test message publication and email notification received
{{% /notice %}}

## Exercise 2: Critical Capacity Alerts

### Step 1: High Read Capacity Alarm

**Prevent read throttling**:

1. **CloudWatch**: Navigate to CloudWatch console
2. **Alarms**: Click "Alarms" → "All alarms"
3. **Create alarm**: Click "Create alarm"
4. **Select metric**:
   - **AWS/DynamoDB** → **Table Metrics**
   - **Table**: `demo-ecommerce-freetier`
   - **Metric**: `ConsumedReadCapacityUnits`

5. **Define conditions**:
   - **Statistic**: Average
   - **Period**: 5 minutes
   - **Threshold type**: Static
   - **Condition**: Greater than `4` (80% of 5 RCU limit)
   - **Datapoints to alarm**: 2 out of 3

{{% notice info %}}
**Screenshot Location**: Add screenshot of read capacity alarm configuration with threshold settings
{{% /notice %}}

### Step 2: Configure Alarm Actions

**Set up notification actions**:

1. **Configure actions**:
   - **Alarm state trigger**: In alarm
   - **SNS topic**: Select `DynamoDB-Alerts`
   - **OK state trigger**: In alarm (recovery notification)
2. **Add description**:
   - **Name**: "DynamoDB-HighReadCapacity"
   - **Description**: "Read capacity utilization exceeds 80% threshold"

### Step 3: High Write Capacity Alarm

**Prevent write throttling**:

1. **Create alarm**: Follow similar process
2. **Metric**: `ConsumedWriteCapacityUnits`
3. **Threshold**: Greater than `4` (80% of 5 WCU)
4. **Name**: "DynamoDB-HighWriteCapacity"

{{% notice info %}}
**Screenshot Location**: Add screenshot of write capacity alarm configuration
{{% /notice %}}

## Exercise 3: Performance and Error Alerts

### Step 1: High Latency Alert

**Monitor response time degradation**:

1. **Create alarm**: New alarm
2. **Metric**: 
   - **AWS/DynamoDB** → **Table Metrics**
   - **Table**: `demo-ecommerce-freetier`
   - **Metric**: `SuccessfulRequestLatency` (Query operations)

3. **Conditions**:
   - **Statistic**: Average
   - **Period**: 5 minutes
   - **Threshold**: Greater than `100` milliseconds
   - **Datapoints**: 3 out of 3 (avoid false alarms)

4. **Configuration**:
   - **Name**: "DynamoDB-HighLatency"
   - **Description**: "Query latency exceeds 100ms threshold"

{{% notice info %}}
**Screenshot Location**: Add screenshot of latency alarm showing threshold and datapoint configuration
{{% /notice %}}

### Step 2: Error Rate Alert

**Detect application errors**:

1. **Create alarm**: Error monitoring
2. **Metric**: `UserErrors`
3. **Conditions**:
   - **Statistic**: Sum
   - **Period**: 5 minutes
   - **Threshold**: Greater than or equal to `1`
   - **Datapoints**: 1 out of 1 (immediate alert)

4. **Name**: "DynamoDB-UserErrors"

### Step 3: Throttling Alert

**Critical throttling detection**:

1. **Metric**: `ThrottledRequests`
2. **Conditions**:
   - **Threshold**: Greater than or equal to `1`
   - **Period**: 1 minute
   - **Immediate**: 1 out of 1

3. **Name**: "DynamoDB-Throttling-CRITICAL"

{{% notice info %}}
**Screenshot Location**: Add screenshot of throttling alarm configuration with critical priority
{{% /notice %}}

## Exercise 4: Lambda Stream Processing Alerts

### Step 1: Lambda Error Alert

**Monitor stream processing failures**:

1. **Create alarm**: Lambda function monitoring
2. **Metric**:
   - **AWS/Lambda** → **Function Metrics**
   - **Function**: `demo-dynamodb-stream-processor`
   - **Metric**: `Errors`

3. **Conditions**:
   - **Threshold**: Greater than or equal to `1`
   - **Period**: 5 minutes
   - **Datapoints**: 1 out of 1

4. **Name**: "Lambda-StreamProcessor-Errors"

{{% notice info %}}
**Screenshot Location**: Add screenshot of Lambda error alarm configuration
{{% /notice %}}

### Step 2: Lambda Duration Alert

**Detect processing performance issues**:

1. **Metric**: `Duration`
2. **Conditions**:
   - **Statistic**: Average
   - **Threshold**: Greater than `20000` milliseconds (20 seconds)
   - **Period**: 5 minutes

3. **Name**: "Lambda-StreamProcessor-SlowExecution"

### Step 3: Iterator Age Alert (Advanced)

**Monitor stream processing lag**:

1. **Metric**: DynamoDB Streams `IteratorAge`
2. **Conditions**:
   - **Threshold**: Greater than `30000` milliseconds (30 seconds)
   - **Period**: 5 minutes

3. **Name**: "DynamoDB-StreamLag"

## Exercise 5: Cost and Storage Alerts

### Step 1: Storage Size Alert

**Free Tier limit monitoring**:

1. **Create alarm**: Storage monitoring
2. **Metric**: `TableSizeBytes`
3. **Conditions**:
   - **Threshold**: Greater than `21474836480` (20GB - 80% of Free Tier)
   - **Period**: 1 hour
   - **Datapoints**: 2 out of 2

4. **Name**: "DynamoDB-StorageWarning"

{{% notice info %}}
**Screenshot Location**: Add screenshot of storage size alarm with Free Tier threshold
{{% /notice %}}

### Step 2: Estimated Charges Alert

**Billing protection**:

1. **Create alarm**: Billing monitoring
2. **Metric**:
   - **AWS/Billing** → **Total Estimated Charge**
   - **Currency**: USD
   - **ServiceName**: AmazonDynamoDB

3. **Conditions**:
   - **Threshold**: Greater than `1.00` USD
   - **Period**: 6 hours

4. **Name**: "DynamoDB-UnexpectedCharges"

## Exercise 6: Advanced Alerting Patterns

### Step 1: Composite Alarms

**Multi-metric alerting**:

1. **Create composite alarm**: Combine multiple conditions
2. **Rule expression**: 
   ```
   ALARM("DynamoDB-HighReadCapacity") OR ALARM("DynamoDB-HighWriteCapacity")
   ```
3. **Name**: "DynamoDB-CapacityIssues"

### Step 2: Anomaly Detection

**ML-based alerting**:

1. **Create alarm**: With anomaly detection
2. **Metric**: `ConsumedReadCapacityUnits`
3. **Anomaly detection**: Enable anomaly detection
4. **Threshold**: Outside of 2 standard deviations

{{% notice info %}}
**Screenshot Location**: Add screenshot of anomaly detection alarm configuration
{{% /notice %}}

### Step 3: Alarm Scheduling

**Time-based alert suppression**:

1. **Maintenance windows**: Suppress non-critical alerts
2. **Business hours**: Different thresholds for business vs off-hours
3. **Weekend adjustments**: Relaxed thresholds for expected low usage

## Exercise 7: Alert Testing and Validation

### Step 1: Generate Test Conditions

**Trigger alarms safely**:

1. **High capacity**: Create multiple items rapidly
2. **Latency test**: Use complex queries
3. **Error simulation**: Query non-existent items

### Step 2: Verify Alert Flow

**End-to-end testing**:

1. **Trigger alarm**: Generate alert condition
2. **Check alarm state**: Verify alarm triggers
3. **Confirm notification**: Check email delivery
4. **Recovery test**: Verify OK state notifications

{{% notice info %}}
**Screenshot Location**: Add screenshot of alarm in ALARM state with notification sent
{{% /notice %}}

### Step 3: Response Time Measurement

**Alert performance metrics**:
- **Detection time**: Condition to alarm state
- **Notification time**: Alarm to email delivery
- **Total time**: End-to-end alerting latency

## Alert Management Best Practices

### Alarm Organization

**Structured approach**:

```
Alarm Naming Convention:
[Service]-[Resource]-[Metric]-[Severity]

Examples:
- DynamoDB-Table-HighCapacity-WARNING
- Lambda-StreamProcessor-Errors-CRITICAL
- DynamoDB-Storage-Approaching-INFO
```

### Escalation Strategy

**Alert priority levels**:

| Priority | Response Time | Examples |
|----------|---------------|----------|
| **CRITICAL** | Immediate | Throttling, System errors |
| **HIGH** | 15 minutes | High latency, Capacity warnings |
| **MEDIUM** | 1 hour | Storage growth, Usage patterns |
| **LOW** | Next business day | Optimization opportunities |

### Alert Fatigue Prevention

**Maintain alerting effectiveness**:

- **Tune thresholds**: Adjust based on actual patterns
- **Group related alerts**: Avoid duplicate notifications
- **Suppress during maintenance**: Planned outage handling
- **Regular review**: Remove obsolete alerts

## Troubleshooting Alerts

### Common Alert Issues

**Alerts not triggering**:
1. **Threshold values**: Check if realistic for your usage
2. **Datapoint configuration**: Too strict requirements
3. **Metric delay**: CloudWatch metric propagation lag
4. **Period settings**: Too short for metric collection

**False positive alerts**:
1. **Threshold tuning**: Adjust based on normal patterns
2. **Datapoint requirements**: Require multiple breaches
3. **Anomaly detection**: Use ML-based thresholds
4. **Composite alarms**: Combine multiple indicators

**Missing notifications**:
1. **SNS subscription**: Verify email confirmation
2. **Email filtering**: Check spam/junk folders
3. **Topic permissions**: Ensure CloudWatch can publish
4. **Subscription limits**: SNS subscription quotas

{{% notice warning %}}
**Free Tier Limits**: CloudWatch allows 10 alarms in the Free Tier. Plan your most critical alerts carefully.
{{% /notice %}}

## Exercise Summary

You've built a comprehensive alerting system:

- ✅ **SNS notification system** for centralized alerting
- ✅ **Capacity alerts** to prevent throttling
- ✅ **Performance monitoring** for latency and errors
- ✅ **Stream processing alerts** for Lambda monitoring
- ✅ **Cost protection** with billing alerts
- ✅ **Advanced patterns** with composite alarms

{{% notice success %}}
**Alerting Excellence**: Your system now detects issues proactively and notifies you before users are impacted!
{{% /notice %}}

## Next Steps

With proactive alerting in place, the next step is analyzing costs and optimizing your Free Tier usage to maximize value while minimizing expenses.
