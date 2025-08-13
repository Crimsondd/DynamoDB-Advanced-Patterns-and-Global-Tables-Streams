---
title : "5.1 CloudWatch Basic Monitoring"
date : "2025-08-11"
weight : 51
chapter : false
pre : " <b> 5.1 </b> "
---

# CloudWatch Basic Monitoring

📊 **View basic DynamoDB metrics in CloudWatch**

## Overview

CloudWatch automatically collects basic metrics from DynamoDB. We'll explore how to view these metrics without creating complex dashboards.

## View DynamoDB Metrics

### Step 1: Access CloudWatch Metrics

1. **AWS Console**: Search "CloudWatch"
2. **Metrics**: Click "Metrics" in left sidebar
3. **AWS/DynamoDB**: Click on this namespace
4. **Table Metrics**: Browse available metrics

![5.1.1](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/5/5.1.1.png?featherlight=false&width=90pc)

### Step 2: Check Key Metrics

**Monitor basic usage**:

1. **ConsumedReadCapacityUnits**: Check read usage
2. **ConsumedWriteCapacityUnits**: Check write usage
3. **ItemCount**: Number of items in table
4. **TableSizeBytes**: Storage usage

![5.1.2](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/5/5.1.2.png?featherlight=false&width=90pc)

### Step 3: View Metric Details

**Click on any metric to see details**:

1. **Select your table**: `demo-ecommerce-freetier`
2. **View graph**: Shows usage over time
3. **Check values**: Ensure staying within Free Tier

![5.1.3](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/5/5.1.3.png?featherlight=false&width=90pc)

## Key Metrics to Check

| Metric | Purpose | Free Tier Limit |
|--------|---------|-----------------|
| **ConsumedReadCapacityUnits** | Read usage | 25 RCU/month |
| **ConsumedWriteCapacityUnits** | Write usage | 25 WCU/month |
| **ItemCount** | Number of items | No limit |
| **TableSizeBytes** | Storage used | 25 GB |

{{% notice info %}}
**Free Tier**: All basic CloudWatch metrics for DynamoDB are free. No dashboard creation needed.
{{% /notice %}}

## Simple Monitoring Tips

- **Check metrics daily**: Ensure staying within Free Tier
- **Look for spikes**: Unusual usage patterns
- **Monitor storage**: Track table size growth
- **No alerts needed**: Visual checking is sufficient for workshop

{{% notice success %}}
**Basic Monitoring**: You can now view essential DynamoDB metrics in CloudWatch!
{{% /notice %}}

## Next Steps

Basic metric viewing is sufficient for this workshop. Move to the next module when ready.
