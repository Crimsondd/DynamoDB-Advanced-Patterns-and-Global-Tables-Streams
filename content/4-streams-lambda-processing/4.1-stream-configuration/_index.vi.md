---
title : "4.1 Stream Configuration"
date : "2025-08-11"
weight : 41
chapter : false
pre : " <b> 4.1 </b> "
---

## Enable DynamoDB Streams

🔧 **Configure DynamoDB table của bạn để capture every data change**

### Tổng quan

DynamoDB Streams capture data modification events trong tables của bạn. Khi enabled, streams provide time-ordered sequence của item-level modifications cho up to 24 hours.

### What Streams Capture

**Event Types**:
- **INSERT**: New item added to table
- **MODIFY**: Existing item updated
- **REMOVE**: Item deleted from table

**Stream View Types**:
- **KEYS_ONLY**: Only key attributes của modified item
- **NEW_IMAGE**: Entire item after modification
- **OLD_IMAGE**: Entire item before modification  
- **NEW_AND_OLD_IMAGES**: Both before và after images

## Exercise 1: Enable Streams on Existing Table

### Bước 1: Navigate to DynamoDB Console

**Access table của bạn**:

1. **AWS Console**: Search "DynamoDB"
2. **Tables**: Click "Tables" trong left sidebar
3. **Select table**: Click `demo-ecommerce-freetier`
4. **Exports and streams**: Click "Exports and streams" tab

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của DynamoDB console với Exports and streams tab highlighted
{{% /notice %}}

### Bước 2: Configure DynamoDB Stream

**Enable stream processing**:

1. **DynamoDB stream section**: Scroll to "DynamoDB stream" section
2. **Turn on stream**: Click "Turn on" button
3. **View type selection**: Choose "New and old images"
4. **Confirmation**: Click "Turn on stream"

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của stream configuration dialog với "New and old images" selected
{{% /notice %}}

### Bước 3: Verify Stream Configuration

**Check stream status**:

1. **Stream details**: Note the stream ARN appears
2. **Status**: Should show "Active"
3. **View type**: Confirms "New and old images"
4. **Creation time**: Shows khi stream was enabled

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot showing active stream với ARN và configuration details
{{% /notice %}}

## Exercise 2: Understanding Stream Settings

### Stream View Type Comparison

**Choose the right view type cho use case của bạn**:

| View Type | Use Case | Data Captured |
|-----------|----------|---------------|
| **KEYS_ONLY** | Audit logging | PK, SK only |
| **NEW_IMAGE** | Cache updates | Item after change |
| **OLD_IMAGE** | Change tracking | Item before change |
| **NEW_AND_OLD_IMAGES** | Full audit | Both versions |

### Performance Considerations

**Stream Configuration Impact**:

- **Storage**: NEW_AND_OLD_IMAGES uses most space
- **Lambda payload**: Larger payloads với full images
- **Processing time**: More data = longer processing
- **Cost**: Minimal additional cost cho streams

{{% notice warning %}}
**Free Tier Note**: DynamoDB Streams are included at no additional charge. Lambda processing stays within Free Tier limits.
{{% /notice %}}

## Exercise 3: Test Stream Functionality

### Bước 1: Create Test Item

**Generate một stream event**:

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
**Vị trí Screenshot**: Thêm screenshot của item creation dialog với stream test data
{{% /notice %}}

### Bước 2: Monitor Stream Activity

**Check stream metrics**:

1. **CloudWatch**: Open CloudWatch console trong new tab
2. **Metrics**: Navigate to Metrics
3. **DynamoDB**: Click "DynamoDB" namespace
4. **Stream metrics**: Look for stream-related metrics
5. **IncomingRecords**: Should show 1 new record

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của CloudWatch showing DynamoDB stream metrics
{{% /notice %}}

### Bước 3: Understand Stream Records

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
- **Keys**: Primary key của changed item
- **NewImage**: Complete item data after creation
- **OldImage**: Would be empty cho INSERT events

## Exercise 4: Stream Configuration Best Practices

### Optimal Configuration for Workshop

**Recommended settings**:

- ✅ **View Type**: NEW_AND_OLD_IMAGES (comprehensive audit trail)
- ✅ **Retention**: 24 hours (default, sufficient cho processing)
- ✅ **Shards**: Auto-managed by AWS
- ✅ **Processing**: Lambda với appropriate batch size

### Security Considerations

**Access control**:

1. **IAM permissions**: Lambda needs stream read permissions
2. **Encryption**: Streams inherit table encryption settings
3. **VPC**: Streams work within VPC configuration của bạn
4. **Monitoring**: CloudTrail logs stream access

### Cost Optimization

**Stream cost factors**:

- **Read requests**: No additional charge cho stream writes
- **Lambda invocations**: Count toward Free Tier
- **Data transfer**: Minimal cho in-region processing
- **Storage**: Stream records retained cho 24 hours only

## Exercise 5: Advanced Stream Configuration

### Multiple Consumer Pattern

**Khi bạn need multiple processors**:

1. **Single stream**: One DynamoDB stream per table
2. **Multiple Lambdas**: Each có thể process the same stream
3. **Kinesis Data Streams**: Cho more complex routing
4. **Event filtering**: Lambda-level filtering

### Cross-Region Considerations

**Global Tables + Streams**:

- **Each region**: Has its own stream
- **Replication events**: Generate stream records
- **Filtering**: Distinguish app writes from replication
- **Processing**: Handle regional differences

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của Global Tables với streams enabled trong multiple regions
{{% /notice %}}

## Troubleshooting Common Issues

### Stream Not Appearing

**Check các items này**:

1. **Permissions**: Ensure bạn có DynamoDB full access
2. **Region**: Verify bạn đang ở correct AWS region
3. **Table status**: Table must be ACTIVE để enable streams
4. **Refresh**: Browser refresh có thể needed

### Stream Configuration Failed

**Possible causes**:

1. **Table updating**: Wait cho table to be ACTIVE
2. **Permissions**: Need dynamodb:EnableStream permission
3. **Rate limits**: Wait và retry nếu rate limited
4. **Billing**: Ensure account is trong good standing

### Stream Records Missing

**Debugging steps**:

1. **Stream status**: Confirm stream is ACTIVE
2. **Write operations**: Ensure items are actually changing
3. **Time delay**: Allow 1-2 minutes cho propagation
4. **Metrics**: Check CloudWatch cho IncomingRecords

## Configuration Summary

Bằng cách completing exercise này, bạn đã có:

- ✅ **Enabled DynamoDB Streams** on table của bạn
- ✅ **Configured view type** cho comprehensive change capture  
- ✅ **Tested stream functionality** với sample data
- ✅ **Understood stream record structure** và components
- ✅ **Applied best practices** cho optimal configuration

{{% notice success %}}
**Stream Ready**: DynamoDB table của bạn bây giờ captures every change và ready cho Lambda processing!
{{% /notice %}}

## Next Steps

Với streams configured, bạn ready để create Lambda functions sẽ process các events này real-time. Trong next section, chúng ta sẽ build và deploy Lambda function optimized cho DynamoDB stream processing.
