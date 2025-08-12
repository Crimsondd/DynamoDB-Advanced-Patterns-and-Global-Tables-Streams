---
title : "4.4 Monitoring & Debugging"
date : "2025-08-11"
weight : 44
chapter : false
pre : " <b> 4.4 </b> "
---

## Stream Processing Operations

📊 **Monitor, debug, and optimize your DynamoDB Streams and Lambda processing pipeline**

### Overview

Production stream processing requires comprehensive monitoring, proactive alerting, and effective debugging capabilities. Learn to operate your event-driven system with confidence.

### Monitoring Strategy

**Multi-layered approach**:
- **Infrastructure metrics**: Lambda and DynamoDB performance
- **Application metrics**: Business logic success rates
- **Cost metrics**: Free Tier usage and optimization
- **Error tracking**: Failed processing and retry patterns

## Exercise 1: Comprehensive Monitoring Setup

### Step 1: Create Monitoring Dashboard

**Build CloudWatch dashboard**:

1. **CloudWatch console**: Navigate to Dashboards
2. **Create dashboard**: Click "Create dashboard"
3. **Dashboard name**: `DynamoDB-Streams-Monitoring`
4. **Add widgets**: Start with blank dashboard

{{% notice info %}}
**Screenshot Location**: Add screenshot of CloudWatch dashboard creation interface
{{% /notice %}}

### Step 2: Add Lambda Function Metrics

**Essential Lambda metrics**:

1. **Add widget**: Click "Add widget"
2. **Line graph**: Select line graph type
3. **Metrics**: Choose AWS/Lambda namespace
4. **Select metrics**:
   - `Invocations` (demo-dynamodb-stream-processor)
   - `Duration` (demo-dynamodb-stream-processor)  
   - `Errors` (demo-dynamodb-stream-processor)
   - `Throttles` (demo-dynamodb-stream-processor)
5. **Period**: Set to 5 minutes
6. **Add to dashboard**: Save widget

{{% notice info %}}
**Screenshot Location**: Add screenshot of Lambda metrics widget configuration
{{% /notice %}}

### Step 3: Add DynamoDB Stream Metrics

**Stream-specific monitoring**:

1. **Add widget**: New line graph
2. **Metrics**: AWS/DynamoDB namespace
3. **Stream metrics**:
   - `IncomingRecords` (your table stream)
   - `IteratorAge` (your table stream)
   - `ReadThrottledEvents` (your table stream)
4. **Time range**: Last 1 hour
5. **Refresh**: Auto-refresh every 1 minute

{{% notice info %}}
**Screenshot Location**: Add screenshot of DynamoDB stream metrics configuration
{{% /notice %}}

### Step 4: Add Custom Business Metrics

**Application-level tracking**:

1. **Custom namespace**: "DynamoDB-Workshop/StreamProcessing"
2. **Metrics to track**:
   - Processed INSERT events
   - Processed MODIFY events
   - Processed REMOVE events
   - Business logic success rate
   - Processing latency

**Example custom metric code** (add to Lambda):

```python
import boto3

cloudwatch = boto3.client('cloudwatch')

def send_custom_metric(metric_name, value, unit='Count'):
    """Send custom metric to CloudWatch"""
    try:
        cloudwatch.put_metric_data(
            Namespace='DynamoDB-Workshop/StreamProcessing',
            MetricData=[
                {
                    'MetricName': metric_name,
                    'Value': value,
                    'Unit': unit,
                    'Timestamp': datetime.utcnow()
                }
            ]
        )
    except Exception as e:
        logger.error(f"Failed to send metric {metric_name}: {e}")

# Add to your event handlers:
def handle_insert_event(record):
    logger.info("Processing INSERT event")
    # Your business logic here...
    send_custom_metric('INSERT_Events_Processed', 1)
```

## Exercise 2: Real-Time Alerting

### Step 1: Create Lambda Error Alert

**Alert for processing failures**:

1. **CloudWatch**: Navigate to Alarms
2. **Create alarm**: Click "Create alarm"
3. **Select metric**: AWS/Lambda → Errors
4. **Function**: demo-dynamodb-stream-processor
5. **Conditions**:
   - **Threshold**: Greater than 0
   - **Period**: 5 minutes
   - **Datapoints**: 1 out of 1
6. **Actions**: Create SNS topic for notifications

{{% notice info %}}
**Screenshot Location**: Add screenshot of Lambda error alarm configuration
{{% /notice %}}

### Step 2: Create High Latency Alert

**Alert for slow processing**:

1. **Create alarm**: For Lambda Duration metric
2. **Conditions**:
   - **Statistic**: Average
   - **Threshold**: Greater than 10 seconds
   - **Period**: 5 minutes
   - **Datapoints**: 2 out of 3
3. **Actions**: Same SNS topic as error alert

### Step 3: Create Stream Lag Alert

**Alert for processing delays**:

1. **Create alarm**: For DynamoDB IteratorAge
2. **Conditions**:
   - **Threshold**: Greater than 30 seconds
   - **Period**: 5 minutes
   - **Datapoints**: 2 out of 3
3. **Description**: "Stream processing falling behind"

{{% notice info %}}
**Screenshot Location**: Add screenshot of iterator age alarm showing stream lag monitoring
{{% /notice %}}

### Step 4: Configure SNS Notifications

**Set up email alerts**:

1. **SNS console**: Create topic "StreamProcessing-Alerts"
2. **Subscription**: Add your email address
3. **Confirm subscription**: Check email and confirm
4. **Test notification**: Send test message

## Exercise 3: Debugging Common Issues

### Issue 1: Lambda Function Not Triggering

**Diagnostic checklist**:

1. **Stream status**: Verify DynamoDB stream is active
2. **Event source mapping**: Check if enabled and healthy
3. **IAM permissions**: Verify Lambda execution role
4. **Function state**: Ensure Lambda isn't in failed state

**Debugging steps**:

1. **DynamoDB console**: Check stream status
2. **Lambda console**: Verify trigger configuration
3. **IAM console**: Review execution role permissions
4. **CloudTrail**: Check for permission errors

{{% notice info %}}
**Screenshot Location**: Add screenshot of event source mapping status showing enabled state
{{% /notice %}}

### Issue 2: Processing Errors in Lambda

**Error investigation**:

1. **CloudWatch Logs**: Check for error messages
2. **Common errors**:
   - Timeout errors (increase timeout)
   - Memory errors (increase memory)
   - Permission errors (check IAM)
   - Syntax errors (review code)

**Error log example**:
```
[ERROR] 2025-08-11T17:30:00.000Z Task timed out after 30.00 seconds
```

**Resolution steps**:
1. **Increase timeout**: 30s → 60s
2. **Add error handling**: Try-catch blocks
3. **Optimize code**: Reduce processing time
4. **Monitor memory**: Increase if needed

### Issue 3: High Iterator Age

**Stream processing lag**:

1. **Symptoms**: IteratorAge > 30 seconds consistently
2. **Causes**:
   - High write volume to DynamoDB
   - Lambda function running slowly
   - Insufficient concurrency
   - Error in processing logic

**Resolution**:
1. **Optimize Lambda**: Reduce processing time
2. **Increase concurrency**: Allow more parallel executions
3. **Batch size tuning**: Adjust batch size for efficiency
4. **Error handling**: Fix processing errors

{{% notice info %}}
**Screenshot Location**: Add screenshot of CloudWatch metrics showing high iterator age and resolution
{{% /notice %}}

## Exercise 4: Performance Optimization

### Step 1: Analyze Processing Patterns

**Performance metrics analysis**:

1. **Duration trends**: Identify slow processing periods
2. **Invocation patterns**: Understand batch processing
3. **Error patterns**: Track failure scenarios
4. **Cost patterns**: Monitor Free Tier usage

### Step 2: Optimize Lambda Configuration

**Right-sizing your function**:

```
Performance Testing Results:
┌─────────────┬─────────────┬─────────────┬─────────────┐
│ Memory (MB) │ Duration    │ Cost/Invoke │ Throughput  │
├─────────────┼─────────────┼─────────────┼─────────────┤
│ 128         │ 850ms       │ Lowest      │ Slowest     │
│ 256         │ 425ms       │ Medium      │ Medium      │
│ 512         │ 215ms       │ Higher      │ Fastest     │
└─────────────┴─────────────┴─────────────┴─────────────┘
```

**Optimal Free Tier configuration**:
- **Memory**: 128 MB (cost effective)
- **Timeout**: 30 seconds (sufficient)
- **Batch size**: 10 records (balanced)
- **Concurrency**: 10 (Free Tier safe)

### Step 3: Code Optimization

**Performance improvements**:

```python
# Optimized stream processing code
import json
import boto3
import logging
from datetime import datetime

logger = logging.getLogger()
logger.setLevel(logging.INFO)

# Reuse connections outside handler
cloudwatch = boto3.client('cloudwatch')

def lambda_handler(event, context):
    """
    Optimized stream processing
    """
    start_time = datetime.utcnow()
    processed_records = 0
    errors = 0
    
    try:
        # Batch process records
        for record in event['Records']:
            try:
                process_stream_record(record)
                processed_records += 1
            except Exception as e:
                logger.error(f"Error processing record: {e}")
                errors += 1
                # Continue processing other records
        
        # Send metrics in batch
        end_time = datetime.utcnow()
        processing_duration = (end_time - start_time).total_seconds()
        
        send_batch_metrics(processed_records, errors, processing_duration)
        
        return {
            'statusCode': 200,
            'body': json.dumps({
                'processed': processed_records,
                'errors': errors,
                'duration_seconds': processing_duration
            })
        }
        
    except Exception as e:
        logger.error(f"Critical error in lambda_handler: {e}")
        raise e

def send_batch_metrics(processed, errors, duration):
    """Send multiple metrics in single call"""
    try:
        cloudwatch.put_metric_data(
            Namespace='DynamoDB-Workshop/StreamProcessing',
            MetricData=[
                {
                    'MetricName': 'RecordsProcessed',
                    'Value': processed,
                    'Unit': 'Count'
                },
                {
                    'MetricName': 'ProcessingErrors', 
                    'Value': errors,
                    'Unit': 'Count'
                },
                {
                    'MetricName': 'ProcessingDuration',
                    'Value': duration,
                    'Unit': 'Seconds'
                }
            ]
        )
    except Exception as e:
        logger.error(f"Failed to send metrics: {e}")
```

## Exercise 5: Cost Monitoring

### Step 1: Track Free Tier Usage

**Monitor AWS Free Tier limits**:

1. **Billing console**: Navigate to AWS Billing
2. **Free Tier**: Check usage against limits
3. **Key metrics**:
   - Lambda invocations: 1M/month limit
   - Lambda compute time: 400,000 GB-seconds/month
   - CloudWatch logs: 5 GB/month
   - CloudWatch metrics: 10 custom metrics

{{% notice info %}}
**Screenshot Location**: Add screenshot of AWS Free Tier usage dashboard
{{% /notice %}}

### Step 2: Cost Optimization Strategies

**Reduce costs while maintaining functionality**:

```
Cost Optimization Checklist:
┌─────────────────────────┬─────────────────────────┬─────────────────────┐
│ Area                    │ Current                 │ Optimized           │
├─────────────────────────┼─────────────────────────┼─────────────────────┤
│ Lambda Memory           │ 256 MB                  │ 128 MB              │
│ Lambda Timeout          │ 60 seconds              │ 30 seconds          │
│ Batch Size              │ 1 record                │ 10 records          │
│ Log Retention           │ Never expire            │ 7 days              │
│ Custom Metrics          │ Unlimited               │ Essential only      │
└─────────────────────────┴─────────────────────────┴─────────────────────┘
```

### Step 3: Set Billing Alerts

**Prevent unexpected charges**:

1. **CloudWatch alarm**: For estimated charges
2. **Threshold**: $1.00 (early warning)
3. **Actions**: Email notification
4. **Monthly budget**: Set $5 limit with alerts

## Exercise 6: Production Readiness

### Checklist for Production Deployment

**Infrastructure readiness**:
- ✅ **Monitoring**: Comprehensive dashboard setup
- ✅ **Alerting**: Critical alerts configured
- ✅ **Error handling**: Robust error processing
- ✅ **Performance**: Optimized for workload
- ✅ **Security**: Proper IAM permissions
- ✅ **Cost control**: Billing alerts active

### Operational Procedures

**Standard operating procedures**:

1. **Daily monitoring**: Check dashboard health
2. **Weekly optimization**: Review performance metrics
3. **Monthly cost review**: Analyze spending trends
4. **Incident response**: Follow alert procedures
5. **Capacity planning**: Monitor growth trends

### Documentation Requirements

**Essential documentation**:
- Architecture diagrams
- Runbook procedures
- Alert response guides
- Performance baselines
- Cost optimization notes

{{% notice info %}}
**Screenshot Location**: Add screenshot of complete monitoring dashboard with all metrics and health indicators
{{% /notice %}}

## Troubleshooting Guide

### Quick Reference

**Common issues and solutions**:

| Issue | Symptoms | Quick Fix |
|-------|----------|-----------|
| **No processing** | No Lambda invocations | Check event source mapping |
| **High errors** | Error rate > 0% | Review CloudWatch logs |
| **Slow processing** | High duration | Optimize code/increase memory |
| **High costs** | Exceeding Free Tier | Reduce batch size/frequency |
| **Missing alerts** | No notifications | Verify SNS subscriptions |

### Advanced Debugging

**Deep dive investigation**:

1. **X-Ray tracing**: Enable for detailed function analysis
2. **VPC flow logs**: Network connectivity issues
3. **CloudTrail**: API call investigation
4. **Config rules**: Compliance monitoring

## Exercise Summary

You've mastered stream processing operations:

- ✅ **Comprehensive monitoring** with dashboards and metrics
- ✅ **Proactive alerting** for critical issues
- ✅ **Debugging techniques** for common problems
- ✅ **Performance optimization** for efficiency
- ✅ **Cost monitoring** and Free Tier management
- ✅ **Production readiness** with operational procedures

{{% notice success %}}
**Operations Excellence**: You can now confidently operate DynamoDB Streams and Lambda processing in production!
{{% /notice %}}

## Next Steps

With stream processing mastered, you're ready to tackle advanced monitoring and optimization patterns in Module 5, where we'll build comprehensive dashboards for the entire DynamoDB ecosystem.
