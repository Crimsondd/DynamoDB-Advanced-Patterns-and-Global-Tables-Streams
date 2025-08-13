---
title : "4.2 Lambda Function Setup"
date : "2025-08-11"
weight : 42
chapter : false
pre : " <b> 4.2 </b> "
---

## Create Stream Processing Lambda

⚙️ **Build Lambda function để process DynamoDB stream events trong real-time**

### Tổng quan

AWS Lambda provides serverless compute để process DynamoDB stream events. Function của bạn sẽ automatically trigger khi items change trong table của bạn, enabling real-time processing patterns.

### Function Requirements

**Free Tier Optimized Configuration**:
- **Runtime**: Python 3.9 (reliable và well-supported)
- **Memory**: 128 MB (minimum cho Free Tier)
- **Timeout**: 30 seconds (sufficient cho stream processing)
- **Concurrent executions**: 10 (Free Tier safe)

## Exercise 1: Create Lambda Function

### Bước 1: Access Lambda Console

**Navigate to Lambda service**:

1. **AWS Console**: Search "Lambda"
2. **Functions**: Click "Functions" trong left sidebar
3. **Create function**: Click "Create function" button
4. **Author from scratch**: Select option này

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của Lambda console với Create function button highlighted
{{% /notice %}}

### Bước 2: Configure Basic Settings

**Function configuration**:

1. **Function name**: `demo-dynamodb-stream-processor`
2. **Runtime**: Select "Python 3.9"
3. **Architecture**: Leave as "x86_64" 
4. **Permissions**: "Create a new role with basic Lambda permissions"
5. **Create function**: Click to proceed

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của Lambda function creation form với specified settings
{{% /notice %}}

### Bước 3: Configure Function Settings

**Optimize cho Free Tier**:

1. **Configuration tab**: Click after function creation
2. **General configuration**: Click "Edit"
3. **Memory**: Set to 128 MB
4. **Timeout**: Set to 30 seconds
5. **Save**: Click to apply changes

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của Lambda function configuration settings với memory và timeout values
{{% /notice %}}

## Exercise 2: Add Stream Processing Code

### Bước 1: Replace Function Code

**Navigate to code editor**:

1. **Code tab**: Click để open code editor
2. **lambda_function.py**: Replace existing code với:

```python
import json
import boto3
import logging
from datetime import datetime

# Configure logging
logger = logging.getLogger()
logger.setLevel(logging.INFO)

def lambda_handler(event, context):
    """
    Process DynamoDB Stream events
    Optimized for AWS Free Tier
    """
    try:
        processed_records = 0
        
        # Process each record in the batch
        for record in event['Records']:
            event_name = record['eventName']
            
            # Process INSERT, MODIFY, REMOVE events
            if event_name in ['INSERT', 'MODIFY', 'REMOVE']:
                process_stream_record(record)
                processed_records += 1
        
        # Return success response
        return {
            'statusCode': 200,
            'body': json.dumps({
                'message': f'Successfully processed {processed_records} records',
                'timestamp': datetime.utcnow().isoformat(),
                'processed_count': processed_records
            })
        }
        
    except Exception as e:
        logger.error(f"Error processing stream records: {str(e)}")
        # Re-raise for Lambda retry logic
        raise e

def process_stream_record(record):
    """
    Process individual stream record
    Add your business logic here
    """
    event_name = record['eventName']
    
    # Extract key information
    if 'dynamodb' in record:
        keys = record['dynamodb'].get('Keys', {})
        pk = keys.get('PK', {}).get('S', '')
        sk = keys.get('SK', {}).get('S', '')
        
        logger.info(f"Processing {event_name} for item: {pk}#{sk}")
        
        # Handle different event types
        if event_name == 'INSERT':
            handle_insert_event(record)
        elif event_name == 'MODIFY':
            handle_modify_event(record)
        elif event_name == 'REMOVE':
            handle_remove_event(record)

def handle_insert_event(record):
    """
    Handle new item creation
    """
    logger.info("Processing INSERT event")
    
    # Get new item data
    new_image = record['dynamodb'].get('NewImage', {})
    
    # Example: Send notification for new user
    if 'USER#' in str(new_image.get('PK', {})):
        logger.info("New user created - could send welcome email")
    
    # Example: Update inventory for new product
    elif 'PRODUCT#' in str(new_image.get('PK', {})):
        logger.info("New product created - could update search index")
    
    # Example: Process new order
    elif 'ORDER#' in str(new_image.get('SK', {})):
        logger.info("New order created - could trigger fulfillment")

def handle_modify_event(record):
    """
    Handle item updates
    """
    logger.info("Processing MODIFY event")
    
    # Get before and after images
    old_image = record['dynamodb'].get('OldImage', {})
    new_image = record['dynamodb'].get('NewImage', {})
    
    # Example: Check for status changes
    old_status = old_image.get('status', {}).get('S', '')
    new_status = new_image.get('status', {}).get('S', '')
    
    if old_status != new_status:
        logger.info(f"Status changed from {old_status} to {new_status}")
        # Could trigger notifications, cache updates, etc.
    
    # Example: Price change detection
    old_price = old_image.get('price', {}).get('N', '0')
    new_price = new_image.get('price', {}).get('N', '0')
    
    if old_price != new_price:
        logger.info(f"Price changed from {old_price} to {new_price}")
        # Could invalidate cache, update recommendations, etc.

def handle_remove_event(record):
    """
    Handle item deletion
    """
    logger.info("Processing REMOVE event")
    
    # Get deleted item data
    old_image = record['dynamodb'].get('OldImage', {})
    
    # Example: Cleanup related data
    if 'USER#' in str(old_image.get('PK', {})):
        logger.info("User deleted - could cleanup user data")
    
    # Example: Remove from search index
    elif 'PRODUCT#' in str(old_image.get('PK', {})):
        logger.info("Product deleted - could remove from search")
```

2. **Deploy**: Click "Deploy" để save the code

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của Lambda code editor với stream processing code
{{% /notice %}}

### Bước 2: Test Function Syntax

**Validate the code**:

1. **Test tab**: Click "Test" tab
2. **Create test event**: Click "Create new event"
3. **Event template**: Select "DynamoDB Update" template
4. **Event name**: `test-stream-event`
5. **Test**: Click "Test" để validate syntax

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của Lambda test configuration với DynamoDB Stream template
{{% /notice %}}

## Exercise 3: Configure Event Source Mapping

### Bước 1: Add DynamoDB Trigger

**Connect Lambda to DynamoDB Stream**:

1. **Function overview**: Trong Lambda console
2. **Add trigger**: Click "Add trigger" button
3. **Trigger configuration**: 
   - **Source**: Select "DynamoDB"
   - **DynamoDB table**: Choose `demo-ecommerce-freetier`
   - **Batch size**: Set to 10 (Free Tier optimized)
   - **Starting position**: Select "Trim horizon"
4. **Add**: Click để create trigger

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của trigger configuration dialog với DynamoDB settings
{{% /notice %}}

### Bước 2: Verify Event Source Mapping

**Check trigger configuration**:

1. **Function overview**: Should show DynamoDB trigger
2. **Configuration**: Verify settings:
   - **Batch size**: 10 records
   - **Starting position**: Trim horizon
   - **Status**: Enabled
   - **State**: Creating → Enabled

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot showing successful DynamoDB trigger configuration
{{% /notice %}}

### Bước 3: Configure IAM Permissions

**Update Lambda execution role**:

1. **Configuration tab**: Click "Permissions"
2. **Execution role**: Click role name link
3. **IAM console**: Opens trong new tab
4. **Attach policies**: Add `AWSLambdaDynamoDBExecutionRole`
5. **Save**: Return to Lambda console

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của IAM role với DynamoDB stream permissions
{{% /notice %}}

## Exercise 4: Test Stream Processing

### Bước 1: Create Test Item

**Generate stream event**:

1. **DynamoDB console**: Open trong new tab
2. **Items tab**: Navigate to table của bạn
3. **Create item**: Add test data:

```json
{
  "PK": "LAMBDA#test-processing",
  "SK": "EVENT#001",
  "event_type": "lambda_test",
  "description": "Testing Lambda stream processing",
  "created_at": "2025-08-11T16:30:00Z",
  "test_purpose": "verify_lambda_trigger"
}
```

4. **Create**: Save the item

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của DynamoDB item creation cho Lambda testing
{{% /notice %}}

### Bước 2: Monitor Lambda Execution

**Check function invocation**:

1. **Lambda console**: Return to function của bạn
2. **Monitor tab**: Click để view metrics
3. **Invocations**: Should show 1 new invocation
4. **Duration**: Typically < 1 second
5. **Errors**: Should be 0

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của Lambda monitoring tab showing successful invocation
{{% /notice %}}

### Bước 3: Check Processing Logs

**View detailed logs**:

1. **CloudWatch logs**: Click "View CloudWatch logs"
2. **Log stream**: Click latest log stream
3. **Log entries**: Look for:
   - START RequestId: [uuid]
   - Processing INSERT for item: LAMBDA#test-processing#EVENT#001
   - Processing INSERT event
   - Successfully processed 1 records
   - END RequestId: [uuid]

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của CloudWatch logs showing successful stream processing
{{% /notice %}}

## Exercise 5: Advanced Configuration

### Error Handling Configuration

**Configure retry và error handling**:

1. **Event source mapping**: Edit DynamoDB trigger của bạn
2. **Additional settings**:
   - **Retry attempts**: 3 (default)
   - **Maximum record age**: 3600 seconds
   - **Split batch on error**: Enable
   - **Dead letter queue**: Configure SNS/SQS (optional)

### Performance Optimization

**Free Tier optimization settings**:

- **Parallelization factor**: 1 (avoid excess concurrency)
- **Batch size**: 10 records (balance latency vs cost)
- **Reserved concurrency**: 10 (control costs)
- **Provisioned concurrency**: 0 (not needed cho streams)

### Monitoring and Alerting

**Set up basic monitoring**:

1. **CloudWatch Alarms**: Create cho:
   - Function errors > 0
   - Function duration > 20 seconds
   - Iterator age > 30 seconds
2. **Notifications**: SNS topic cho alerts
3. **Dashboard**: Add metrics to CloudWatch dashboard

{{% notice info %}}
**Vị trí Screenshot**: Thêm screenshot của CloudWatch alarm configuration cho Lambda function
{{% /notice %}}

## Function Testing Patterns

### Test Different Event Types

**Comprehensive testing**:

1. **INSERT**: Create new items
2. **MODIFY**: Update existing items
3. **REMOVE**: Delete items
4. **Batch**: Multiple rapid changes

### Validation Checklist

**Verify setup của bạn**:

- ✅ **Lambda function created** với correct runtime
- ✅ **Stream trigger configured** với proper permissions
- ✅ **Code deployed** và syntax validated
- ✅ **Test successful** với sample data
- ✅ **Logs showing** processing details
- ✅ **Metrics indicating** healthy execution

## Troubleshooting Common Issues

### Lambda Not Triggering

**Check các items này**:

1. **Stream enabled**: DynamoDB stream is active
2. **Permissions**: Lambda có stream read permissions
3. **Event source mapping**: Trigger is enabled
4. **Function state**: Lambda is active (not failed)

### Processing Errors

**Debug steps**:

1. **CloudWatch logs**: Check cho error messages
2. **Timeout issues**: Increase timeout nếu needed
3. **Memory errors**: Monitor memory usage
4. **Permissions**: Verify all required permissions

### Performance Issues

**Optimization tips**:

1. **Batch size**: Adjust based on processing time
2. **Memory allocation**: Right-size cho workload của bạn
3. **Cold starts**: Consider provisioned concurrency nếu needed
4. **Error handling**: Implement proper retry logic

{{% notice success %}}
**Lambda Ready**: Function của bạn bây giờ processing DynamoDB stream events trong real-time!
{{% /notice %}}

## Next Steps

Với Lambda function của bạn processing stream events, bạn ready để practice với real data changes và explore different event-driven patterns. Next section covers hands-on stream processing exercises.
