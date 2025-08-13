---
title : "5.1 CloudWatch Dashboards"
date : "2025-08-11"
weight : 51
chapter : false
pre : " <b> 5.1 </b> "
---

# CloudWatch Dashboards

📊 **Create essential CloudWatch dashboards for DynamoDB monitoring**

## Overview

CloudWatch dashboards provide visual insights into your DynamoDB performance, capacity utilization, and system health. We'll create a simple monitoring dashboard to track key metrics.

## Create Primary Monitoring Dashboard

### Step 1: Access CloudWatch Console

1. **AWS Console**: Search "CloudWatch"
2. **Dashboards**: Click "Dashboards" in left sidebar  
3. **Create dashboard**: Click "Create dashboard" button
4. **Dashboard name**: `DynamoDB-Demo-Monitoring`
5. **Create dashboard**: Click to proceed

### Step 2: Add DynamoDB Capacity Widget

**Monitor capacity utilization**:

1. **Add widget**: Click "Add widget" button
2. **Widget type**: Select "Line" chart
3. **Data source**: Choose "Metrics"
4. **Browse metrics**: 
   - **AWS/DynamoDB** → **Table Metrics**
   - **Table**: Select `demo-ecommerce-freetier`
   - **Metrics**: Check these:
     - `ConsumedReadCapacityUnits`
     - `ConsumedWriteCapacityUnits`
     - `ProvisionedReadCapacityUnits`
     - `ProvisionedWriteCapacityUnits`

5. **Configure widget**:
   - **Title**: "DynamoDB Capacity Utilization"
   - **Period**: 5 minutes
   - **Statistic**: Average

### Step 3: Add Performance Metrics Widget

**Monitor response times and errors**:

1. **Add widget**: Click "Add widget" (second widget)
2. **Widget type**: Line chart
3. **Metrics selection**:
   - **AWS/DynamoDB** → **Table Metrics**
   - **Table**: `demo-ecommerce-freetier`
   - **Select metrics**:
     - `SuccessfulRequestLatency` (Query operations)
     - `UserErrors`
     - `ThrottledRequests`

4. **Widget configuration**:
   - **Title**: "Performance & Error Metrics"
   - **Period**: 5 minutes

### Step 4: Configure Dashboard

**Set operational parameters**:

1. **Time range**: Set to "Last 3 hours"
2. **Auto-refresh**: Enable 1-minute refresh
3. **Save dashboard**: Click "Save dashboard"

## Key Metrics to Monitor

| Metric | Purpose | Target |
|--------|---------|--------|
| **ConsumedReadCapacityUnits** | Monitor read usage | <4 units (80% of 5) |
| **ConsumedWriteCapacityUnits** | Monitor write usage | <4 units (80% of 5) |
| **SuccessfulRequestLatency** | Response time | <50ms average |
| **UserErrors** | Application errors | 0 errors |
| **ThrottledRequests** | Capacity exceeded | 0 throttles |

{{% notice warning %}}
**Free Tier Note**: CloudWatch provides 10 custom metrics and 3 dashboards free. This basic dashboard fits within limits.
{{% /notice %}}

## Dashboard Best Practices

- **Focus on key metrics**: Capacity, latency, errors
- **Set appropriate time ranges**: 3-24 hours for operational monitoring  
- **Use auto-refresh**: 1-5 minutes for real-time visibility
- **Monitor Free Tier limits**: Stay within AWS Free Tier boundaries

{{% notice success %}}
**Dashboard Created**: You now have essential visual monitoring for your DynamoDB table!
{{% /notice %}}

## Next Steps

With basic dashboard monitoring in place, you can expand monitoring as needed or move to the next workshop module.
