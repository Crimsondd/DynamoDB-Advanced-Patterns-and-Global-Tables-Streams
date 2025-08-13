---
title : "3.2 Verify Global Setup"
date : "2025-08-11"
weight : 32
chapter : false
pre : " <b> 3.2 </b> "
---

# Verify Global Setup

✅ **Step-by-step verification of your Global Tables configuration through AWS Console**

## Overview

Your CloudFormation template has automatically configured Global Tables between US-East-1 and EU-West-1. Let's verify everything is working correctly before we start testing replication.

## Step 1: Access Primary Region

### Navigate to US-East-1

1. **Open AWS Console**: Ensure you're logged in
2. **Check Region**: Top-right corner should show "N. Virginia"
3. **Switch if needed**: Click region dropdown → "US East (N. Virginia)"

![US_East](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/US_East.png?featherlight=false&width=50pc)

### Find Your DynamoDB Table

1. **Services**: Navigate to DynamoDB service
2. **Tables**: Click "Tables" in left sidebar
3. **Locate**: Find `demo-ecommerce-freetier`
4. **Status**: Verify table shows "Active"

![DynamoDB_Tables](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/DynamoDB_Tables.png?featherlight=false&width=90pc)

## Step 2: Check Global Tables Configuration

### Access Global Tables Tab

1. **Click**: Table name `demo-ecommerce-freetier`
2. **Navigate**: Click "Global tables" tab
3. **Review**: Global Tables configuration

![Global_tables_tab](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/Global_tables_tab.png?featherlight=false&width=90pc)

### Verify Global Tables Status

**Expected Configuration**:

| Region | Status | Role | Health |
|--------|--------|------|--------|
| **us-east-1** | Active | Primary | ✅ Healthy |
| **eu-west-1** | Active | Replica | ✅ Healthy |

**Key indicators to verify**:
- **Replication Status**: "Healthy" or "Active"
- **Last Replication**: Recent timestamp
- **Pending Updates**: Should be 0

![Global_tables](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/Global_tables.png?featherlight=false&width=90pc)

## Step 3: Verify Secondary Region

### Switch to EU-West-1

1. **Region Selector**: Click region dropdown (top-right)
2. **Select**: "Europe (Ireland)" - eu-west-1
3. **Wait**: Allow console to switch regions

![Europe](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/Europe.png?featherlight=false&width=50pc)

### Check Replica Table

1. **Navigate**: DynamoDB → Tables
2. **Find**: Same table name `demo-ecommerce-freetier`
3. **Verify**: Table exists and shows "Active"
4. **Check**: Global tables tab shows replica status

**Expected in EU-West-1**:
- **Table Name**: `demo-ecommerce-freetier` (identical)
- **Status**: Active
- **Role**: Replica table
- **Primary Region**: us-east-1

![EU_region_showing](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/EU_region_showing.png?featherlight=false&width=90pc)

## Step 4: Compare Table Schemas

### Verify Schema Consistency

Both regions should have identical table schema:

**Primary Keys**:
- **Partition Key**: PK (String)
- **Sort Key**: SK (String)

**Global Secondary Indexes**:
- **GSI1**: GSI1PK (String), GSI1SK (String)
- **GSI2**: GSI2PK (String), GSI2SK (String)

**Settings**:
- **Read Capacity**: 5 units (provisioned)
- **Write Capacity**: 5 units (provisioned)
- **Point-in-time Recovery**: Enabled
- **DynamoDB Streams**: Enabled

![table_schema](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/table_schema.png?featherlight=false&width=90pc)


## Step 5: Check Current Data

### Verify Existing Data Replication

If you've completed Module 2, check that your existing data appears in both regions:

**Switch to US-East-1**:
1. **Go to**: Items tab
2. **Count items**: Note the number of items

**Switch to EU-West-1**:
1. **Go to**: Items tab  
2. **Compare count**: Should match US region exactly

**If item counts don't match**:
- Wait 1-2 minutes for replication
- Refresh the browser page
- Check Global Tables health status

![item_counts1](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/item_counts1.png?featherlight=false&width=90pc)
![item_counts2](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/item_counts2.png?featherlight=false&width=90pc)

## Step 6: Verify Stream Configuration

### Check DynamoDB Streams

**In US-East-1**:
1. **Table Overview**: Go to table details
2. **Exports and streams**: Click tab
3. **Stream details**: Verify settings

**Expected Stream Configuration**:
- **Stream Status**: Enabled
- **Stream view type**: New and old images
- **Stream ARN**: Should be present
- **Shard count**: 1 or more active shards

![DynamoDB_Streams_config](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/DynamoDB_Streams_config.png?featherlight=false&width=90pc)

## Step 7: Health Check Dashboard

### Monitor Replication Health

**Access Monitoring**:
1. **Metrics Tab**: Click in table view
2. **Global Tables metrics**: Look for replication metrics
3. **Key metrics**: 
   - Replication latency
   - Pending replication count
   - Failed replication events

**Healthy Indicators**:
- **Replication Latency**: < 2 seconds average
- **Pending Count**: 0 or very low
- **Error Rate**: 0%

![replication_metrics](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/replication_metrics.png?featherlight=false&width=90pc)

## Step 8: Network Connectivity Test

### Test Cross-Region Communication

**Simple connectivity verification**:
1. **Create test item** in US-East-1
2. **Wait 30 seconds**
3. **Check EU-West-1** for the item
4. **Delete test item** from either region

**Test Item Example**:
```json
{
  "PK": "TEST#connectivity",
  "SK": "VERIFICATION",
  "timestamp": "2025-08-11T15:00:00Z",
  "test_purpose": "global_tables_verification"
}
```

![test_item_appearing](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/3/test_item_appearing.png?featherlight=false&width=90pc)

## Troubleshooting Common Issues

### Issue: Table Not Found in EU-West-1

**Possible Causes**:
- Wrong region selected
- CloudFormation deployment incomplete
- Global Tables setup failed

**Solutions**:
1. **Double-check region** in top-right corner
2. **Verify CloudFormation** stack completed successfully
3. **Check IAM permissions** for cross-region access

### Issue: Replication Status Unhealthy

**Check These Items**:
- **Network connectivity** between regions
- **DynamoDB Streams** enabled on source table
- **IAM permissions** for Global Tables service
- **Table capacity** not throttling

### Issue: Item Counts Don't Match

**Troubleshooting Steps**:
1. **Wait longer** (up to 2 minutes)
2. **Refresh browser** page
3. **Check for errors** in CloudWatch logs
4. **Verify no throttling** in metrics

## Verification Checklist

Before proceeding to multi-region operations:

- [ ] **Both regions accessible** through AWS Console
- [ ] **Table exists** in both us-east-1 and eu-west-1
- [ ] **Global Tables status** shows "Healthy" or "Active"
- [ ] **Schema identical** between regions
- [ ] **DynamoDB Streams** enabled
- [ ] **Existing data replicated** (if any)
- [ ] **Test connectivity** working
- [ ] **Monitoring metrics** available

{{% notice success %}}
**Verification Complete**: Your Global Tables setup is healthy and ready for multi-region operations!
{{% /notice %}}

## Next Steps

With Global Tables verified and healthy, you're ready to experience multi-region operations. In the next section, we'll create data in one region and watch it automatically appear in another!

{{% notice tip %}}
**Pro Tip**: Keep both region tabs open in your browser (US-East-1 and EU-West-1) to easily switch between them during exercises.
{{% /notice %}}
