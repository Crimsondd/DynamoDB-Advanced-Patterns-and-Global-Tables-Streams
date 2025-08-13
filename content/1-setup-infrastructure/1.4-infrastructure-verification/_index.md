---
title : "1.4 Infrastructure Verification"
date : "2025-08-11"
weight : 14
chapter : false
pre : " <b> 1.4 </b> "
---

# Infrastructure Verification

✅ **Comprehensive testing to ensure all AWS resources are properly deployed and functioning**

## Overview

After CloudFormation deployment, it's critical to verify that all resources are working correctly. This section provides step-by-step verification procedures to ensure your infrastructure is ready for the workshop.

## Verification Checklist

Use this checklist to systematically verify each component:

- [ ] CloudFormation stack status: CREATE_COMPLETE
- [ ] DynamoDB table: Active and accessible
- [ ] DynamoDB Global Tables: Replication configured
- [ ] Lambda function: Deployed and connected to stream
- [ ] CloudWatch dashboard: Metrics visible
- [ ] IAM roles: Properly configured permissions
- [ ] Billing status: $0.00 charges
- [ ] Free Tier usage: Within limits

## Step 1: Verify CloudFormation Stack

### 1.1 Check Stack Status

**Navigate to CloudFormation**:
1. AWS Console → CloudFormation → Stacks
2. Find stack: `demo-dynamodb-freetier`
3. **Status should be**: `CREATE_COMPLETE` ✅

**If status shows anything else**:
- `CREATE_IN_PROGRESS`: Wait for completion
- `CREATE_FAILED`: Check Events tab for errors
- `ROLLBACK_COMPLETE`: Delete and redeploy

### 1.2 Review Stack Outputs

**Click on your stack** → **Outputs tab**:

![OutPut-Stack](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/1/Output-Stack.png?featherlight=false&width=90pc)

**Record these values** - you'll use them for further verification.

## Step 2: Verify DynamoDB Table

### 2.1 Access DynamoDB Console

**Navigate to DynamoDB**:
1. AWS Console → Services → DynamoDB
2. Click **Tables** in left sidebar
3. Find table: `demo-ecommerce-freetier`

### 2.2 Check Table Status

**Table Overview**:

| Property | Expected Value |
|----------|----------------|
| Table Status | Active ✅ |
| Partition Key | PK (String) |
| Sort Key | SK (String) |
| Read Capacity | 5 (Provisioned) |
| Write Capacity | 5 (Provisioned) |
| Point-in-time Rec. | Enabled |
| Streams | Enabled (NEW_AND_OLD_IMAGES) |

### 2.3 Verify Table Configuration

**Click on table name** to view details:

**General Tab**:
- **Table name**: `demo-ecommerce-freetier`
- **Primary key**: `PK` (String), `SK` (String)
- **Table status**: Active
- **Creation date**: Today's date

**Capacity Tab**:
- **Read capacity**: 5 units (Provisioned)
- **Write capacity**: 5 units (Provisioned)
- **Auto scaling**: Disabled (for Free Tier safety)

### 2.4 Check DynamoDB Streams

**Exports and streams Tab**:
- **DynamoDB stream**: Enabled ✅
- **Stream view type**: New and old images
- **Stream ARN**: Should match CloudFormation output

## Step 3: Verify Global Tables Setup

### 3.1 Check Global Tables Configuration

**Global Tables Tab**:
- **Primary region**: us-east-1 (US East N. Virginia)
- **Replica regions**: eu-west-1 (Europe Ireland)
- **Replication status**: Active

### 3.2 Verify Secondary Region

**Switch to EU-West-1**:
1. **Change region** in AWS Console to "Europe (Ireland)"
2. **Navigate to DynamoDB** → Tables
3. **Find replica table**: `demo-ecommerce-freetier`
4. **Status should be**: Active

**Replica Table Properties**:
- **Table status**: Active
- **Read capacity**: 5 units
- **Global table**: Yes (replica)
- **Primary region**: us-east-1

## Step 4: Verify Lambda Function

### 4.1 Access Lambda Console

**Navigate to Lambda**:
1. **Switch back to US-East-1** region
2. AWS Console → Services → Lambda
3. Click **Functions** in left sidebar
4. Find function: `demo-dynamodb-stream-processor`

### 4.2 Check Function Configuration

**Function Overview**:

| Property | Expected Value |
|----------|----------------|
| Function Name | demo-dynamodb-stream-processor |
| Runtime | Python 3.9 |
| Memory | 128 MB |
| Timeout | 30 seconds |
| Handler | lambda_function.lambda_handler |
| Last Modified | Today's date |

### 4.3 Verify Stream Trigger

**Configuration Tab**:
- **Triggers**: DynamoDB stream should be listed
- **Source**: `demo-ecommerce-freetier` table
- **State**: Enabled
- **Batch size**: 100 (default)

### 4.4 Test Function Permissions

**Permissions Tab**:
- **Execution role**: Should have DynamoDB stream read permissions
- **Resource-based policy**: Should be configured automatically

## Step 5: Verify CloudWatch Dashboard

### 5.1 Access CloudWatch Console

**Navigate to CloudWatch**:
1. AWS Console → Services → CloudWatch
2. Click **Dashboards** in left sidebar
3. Find dashboard: `demo-dynamodb-freetier-monitoring`

### 5.2 Check Dashboard Widgets

**Expected Widgets**:

| Widget | Description |
|--------|-------------|
| DynamoDB RCU | Read Capacity Utilization |
| DynamoDB WCU | Write Capacity Utilization |
| DynamoDB Throttles | Throttled Read/Write Requests |
| Lambda Invocations | Function invocation count |
| Lambda Errors | Function error rate |
| Lambda Duration | Function execution duration |

### 5.3 Verify Metrics Data

**Initial State** (no activity yet):
- **DynamoDB metrics**: Should show 0 consumed capacity
- **Lambda metrics**: Should show 0 invocations
- **All metrics**: Should be visible but with no data points yet

## Step 6: Verify IAM Roles and Policies

### 6.1 Check Lambda Execution Role

**Navigate to IAM**:
1. AWS Console → Services → IAM
2. Click **Roles** in left sidebar
3. Find role with name containing: `demo-dynamodb-freetier`

### 6.2 Verify Role Permissions

**Attached Policies**:
- **AWS managed policy**: `AWSLambdaBasicExecutionRole`
- **Inline policy**: Custom DynamoDB stream permissions

**Custom Policy Permissions**:
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
      "Resource": "arn:aws:dynamodb:us-east-1:*:table/demo-ecommerce-freetier/stream/*"
    }
  ]
}
```

## Step 7: Test Data Operations

### 7.1 Create Test Item

**Add sample data to verify table functionality**:

1. **Go to DynamoDB Console** → Tables → `demo-ecommerce-freetier`
2. **Click "Explore table items"**
3. **Click "Create item"**

**Test Item**:
```json
{
  "PK": "USER#test123",
  "SK": "PROFILE",
  "name": "Test User",
  "email": "test@example.com",
  "created_at": "2025-08-11T10:00:00Z"
}
```

### 7.2 Verify Item Creation

**Confirm item appears**:
- Item should be visible in table
- Item count should increase to 1
- No errors should appear

### 7.3 Check Lambda Trigger

**Verify stream processing**:
1. **Go to Lambda Console**
2. **Click on stream processor function**
3. **Check "Monitor" tab**
4. **Should see 1 invocation** (from the item creation)

## Step 8: Cost and Free Tier Verification

### 8.1 Check Current Billing

**Navigate to Billing**:
1. AWS Console → Services → Billing & Cost Management
2. **Current charges**: Should show **$0.00** ✅
3. **Month-to-date**: Should show **$0.00** ✅

### 8.2 Verify Free Tier Usage

**Free Tier Dashboard**:
- **DynamoDB**: Should show minimal usage
- **Lambda**: Should show <10 invocations
- **CloudWatch**: Should show active metrics

**Usage Breakdown**:

| Service | Used | Available | % Used |
|---------|------|-----------|--------|
| DynamoDB RCU | <1 unit | 25 units | <4% |
| DynamoDB WCU | <1 unit | 25 units | <4% |
| DynamoDB Storage | <0.01 GB | 25 GB | <0.1% |
| Lambda Requests | 1 request | 1M requests | <0.001% |
| Lambda Duration | <1 GB-sec | 400K GB-sec | <0.001% |

Total Usage: <1% of Free Tier limits ✅

## Step 9: Troubleshooting Common Issues

### 9.1 DynamoDB Table Issues

**Table not found**:
- Check region (must be us-east-1)
- Verify CloudFormation stack completed
- Check table name spelling

**Table not Active**:
- Wait 2-3 minutes for creation
- Check CloudFormation Events for errors
- Verify account limits not exceeded

### 9.2 Lambda Function Issues

**Function not found**:
- Check region
- Verify CloudFormation outputs
- Check IAM permissions

**No invocations after adding data**:
- Verify stream is enabled on table
- Check trigger configuration
- Review function logs for errors

### 9.3 Permission Issues

**Access denied errors**:
- Verify IAM role attached to Lambda
- Check execution role permissions
- Ensure resource ARNs match

### 9.4 Billing Issues

**Unexpected charges appearing**:
1. **STOP all activities immediately**
2. **Check Billing dashboard** for charge sources
3. **Review resource configuration**
4. **Contact AWS support if needed**
5. **Consider deleting and redeploying resources**

## Step 10: Final Verification Summary

### 10.1 Complete Verification Checklist

**Infrastructure Status**:
- [x] CloudFormation: CREATE_COMPLETE
- [x] DynamoDB: Active table with streams
- [x] Global Tables: Replication working
- [x] Lambda: Function deployed and triggered
- [x] CloudWatch: Dashboard accessible
- [x] IAM: Proper permissions configured
- [x] Billing: $0.00 charges
- [x] Test data: Successfully created and processed

### 10.2 Ready for Next Module

**Infrastructure Health Check**:

| Component | Status | Health |
|-----------|---------|---------|
| DynamoDB Table | Active | 🟢 Healthy |
| Global Tables | Replicating | 🟢 Healthy |
| Lambda Function | Active | 🟢 Healthy |
| CloudWatch | Monitoring | 🟢 Healthy |
| Cost Management | $0.00 | 🟢 On Track |

{{% notice success %}}
**Infrastructure Verification Complete**: All resources are properly deployed and functioning. You're ready to proceed to Module 2: Single Table Design.
{{% /notice %}}

## Next Steps

With infrastructure successfully verified, you now have:

- **Production-ready DynamoDB table** with Global Tables
- **Fully functional Lambda stream processor**
- **Complete monitoring and alerting setup**
- **Zero-cost Free Tier deployment**

**Ready for**: [Module 2: Single Table Design](/2-single-table-design/)

{{% notice tip %}}
**Keep This Environment**: Don't delete these resources yet - we'll use them throughout the workshop for hands-on exercises.
{{% /notice %}}
