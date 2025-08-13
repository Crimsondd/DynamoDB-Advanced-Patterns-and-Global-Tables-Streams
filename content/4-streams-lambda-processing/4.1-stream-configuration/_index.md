---
title : "4.1 Stream Configuration"
date : "2025-08-11"
weight : 41
chapter : false
pre : " <b> 4.1 </b> "
---

## Enable DynamoDB Streams

🔧 **Turn on change tracking for your DynamoDB table**

### Overview

DynamoDB Streams capture every change to your table items. This enables real-time processing with Lambda functions.

### Stream View Types

Choose what data to capture:
- **NEW_AND_OLD_IMAGES**: Complete before/after data (recommended for demo)
- **NEW_IMAGE**: Only updated data
- **OLD_IMAGE**: Only previous data
- **KEYS_ONLY**: Just the keys

## Step-by-Step: Enable Streams

### Step 1: Open DynamoDB Console

1. AWS Console → Search "DynamoDB"
2. Tables → Click your table `demo-ecommerce-freetier`
3. Click **"Exports and streams"** tab

### Step 2: Turn On Stream

1. Find "DynamoDB stream" section
2. Click **"Turn on"** button
3. Select **"New and old images"**
4. Click **"Turn on stream"**

### Step 3: Note Stream ARN

Copy the Stream ARN - you'll need it for Lambda setup:
```
arn:aws:dynamodb:us-east-1:123456789012:table/demo-ecommerce-freetier/stream/2025-08-13T10:00:00.000
```

{{% notice success %}}
**Stream Enabled!** Your table now captures all changes. Next, we'll create a Lambda function to process these events.
{{% /notice %}}

### Verification

✅ Stream status shows "Enabled"  
✅ Stream ARN is displayed  
✅ View type is "New and old images"
3. **View type selection**: Choose "New and old images"
4. **Confirmation**: Click "Turn on stream"

{{% notice info %}}
**Screenshot Location**: Add screenshot of stream configuration dialog with "New and old images" selected
{{% /notice %}}

### Step 3: Verify Stream Configuration

**Check stream status**:

1. **Stream details**: Note the stream ARN appears
2. **Status**: Should show "Active"
3. **View type**: Confirms "New and old images"
4. **Creation time**: Shows when stream was enabled

{{% notice info %}}
**Screenshot Location**: Add screenshot showing active stream with ARN and configuration details
{{% /notice %}}

## Exercise 2: Understanding Stream Settings

### Stream View Type Comparison

**Choose the right view type for your use case**:

| View Type | Use Case | Data Captured |
|-----------|----------|---------------|
| **KEYS_ONLY** | Audit logging | PK, SK only |
| **NEW_IMAGE** | Cache updates | Item after change |
| **OLD_IMAGE** | Change tracking | Item before change |
| **NEW_AND_OLD_IMAGES** | Full audit | Both versions |

### Performance Considerations

**Stream Configuration Impact**:

- **Storage**: NEW_AND_OLD_IMAGES uses most space
- **Lambda payload**: Larger payloads with full images
- **Processing time**: More data = longer processing
- **Cost**: Minimal additional cost for streams

{{% notice warning %}}
**Free Tier Note**: DynamoDB Streams are included at no additional charge. Lambda processing stays within Free Tier limits.
{{% /notice %}}

## Exercise 3: Test Stream Functionality

### Step 1: Create Test Item

**Generate a stream event**:

1. **Items tab**: Go back to "Items" tab
2. **Create item**: Click "Create item"
3. **Add test data**:

```json
{
  "PK": "STREAM#test-item",
  "SK": "EVENT#001",
  "event_type": "stream_test",
  "description": "Testing DynamoDB Stream functionality",
  "created_at": "2025-08-11T16:00:00Z",
  "test_purpose": "verify_stream_capture"
}
```

4. **Create**: Click "Create item"

{{% notice info %}}
**Screenshot Location**: Add screenshot of item creation dialog with stream test data
{{% /notice %}}

### Step 2: Monitor Stream Activity

**Check stream metrics**:

1. **CloudWatch**: Open CloudWatch console in new tab
2. **Metrics**: Navigate to Metrics
3. **DynamoDB**: Click "DynamoDB" namespace
4. **Stream metrics**: Look for stream-related metrics
5. **IncomingRecords**: Should show 1 new record

{{% notice info %}}
**Screenshot Location**: Add screenshot of CloudWatch showing DynamoDB stream metrics
{{% /notice %}}

### Step 3: Understand Stream Records

**Stream record structure** (for reference):

```json
{
  "Records": [
    {
      "eventID": "12345...",
      "eventName": "INSERT",
      "eventVersion": "1.1",
      "eventSource": "aws:dynamodb",
      "awsRegion": "us-east-1",
      "dynamodb": {
        "ApproximateCreationDateTime": 1642857600,
        "Keys": {
          "PK": {"S": "STREAM#test-item"},
          "SK": {"S": "EVENT#001"}
        },
        "NewImage": {
          "PK": {"S": "STREAM#test-item"},
          "SK": {"S": "EVENT#001"},
          "event_type": {"S": "stream_test"},
          "description": {"S": "Testing DynamoDB Stream functionality"},
          "created_at": {"S": "2025-08-11T16:00:00Z"},
          "test_purpose": {"S": "verify_stream_capture"}
        },
        "SequenceNumber": "123456789",
        "SizeBytes": 245,
        "StreamViewType": "NEW_AND_OLD_IMAGES"
      }
    }
  ]
}
```

**Key components**:
- **eventName**: INSERT (since this is a new item)
- **Keys**: Primary key of the changed item
- **NewImage**: Complete item data after creation
- **OldImage**: Would be empty for INSERT events

## Exercise 4: Stream Configuration Best Practices

### Optimal Configuration for Workshop

**Recommended settings**:

- ✅ **View Type**: NEW_AND_OLD_IMAGES (comprehensive audit trail)
- ✅ **Retention**: 24 hours (default, sufficient for processing)
- ✅ **Shards**: Auto-managed by AWS
- ✅ **Processing**: Lambda with appropriate batch size

### Security Considerations

**Access control**:

1. **IAM permissions**: Lambda needs stream read permissions
2. **Encryption**: Streams inherit table encryption settings
3. **VPC**: Streams work within your VPC configuration
4. **Monitoring**: CloudTrail logs stream access

### Cost Optimization

**Stream cost factors**:

- **Read requests**: No additional charge for stream writes
- **Lambda invocations**: Count toward Free Tier
- **Data transfer**: Minimal for in-region processing
- **Storage**: Stream records retained for 24 hours only

## Exercise 5: Advanced Stream Configuration

### Multiple Consumer Pattern

**When you need multiple processors**:

1. **Single stream**: One DynamoDB stream per table
2. **Multiple Lambdas**: Each can process the same stream
3. **Kinesis Data Streams**: For more complex routing
4. **Event filtering**: Lambda-level filtering

### Cross-Region Considerations

**Global Tables + Streams**:

- **Each region**: Has its own stream
- **Replication events**: Generate stream records
- **Filtering**: Distinguish app writes from replication
- **Processing**: Handle regional differences

## Troubleshooting Common Issues

### Stream Not Appearing

**Check these items**:

1. **Permissions**: Ensure you have DynamoDB full access
2. **Region**: Verify you're in the correct AWS region
3. **Table status**: Table must be ACTIVE to enable streams
4. **Refresh**: Browser refresh may be needed

### Stream Configuration Failed

**Possible causes**:

1. **Table updating**: Wait for table to be ACTIVE
2. **Permissions**: Need dynamodb:EnableStream permission
3. **Rate limits**: Wait and retry if rate limited
4. **Billing**: Ensure account is in good standing

### Stream Records Missing

**Debugging steps**:

1. **Stream status**: Confirm stream is ACTIVE
2. **Write operations**: Ensure items are actually changing
3. **Time delay**: Allow 1-2 minutes for propagation
4. **Metrics**: Check CloudWatch for IncomingRecords

## Configuration Summary

By completing this exercise, you have:

- ✅ **Enabled DynamoDB Streams** on your table
- ✅ **Configured view type** for comprehensive change capture  
- ✅ **Tested stream functionality** with sample data
- ✅ **Understood stream record structure** and components
- ✅ **Applied best practices** for optimal configuration

{{% notice success %}}
**Stream Ready**: Your DynamoDB table now captures every change and is ready for Lambda processing!
{{% /notice %}}

## Next Steps

With streams configured, you're ready to create Lambda functions that will process these events in real-time. In the next section, we'll build and deploy a Lambda function optimized for DynamoDB stream processing.
