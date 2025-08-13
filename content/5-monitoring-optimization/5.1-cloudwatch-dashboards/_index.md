---
title : "5.1 CloudWatch Dashboards"
date : "2025-08-11"
weight : 51
chapter : false
pre : " <b> 5.1 </b> "
---

## Visual Monitoring Setup

📊 **Create comprehensive CloudWatch dashboards for real-time DynamoDB monitoring**

### Overview

CloudWatch dashboards provide visual insights into your DynamoDB performance, capacity utilization, and system health. Build production-ready monitoring that helps you detect issues before they impact users.

### Dashboard Strategy

**Multi-layered monitoring approach**:
- **Executive Dashboard**: High-level health indicators
- **Operations Dashboard**: Detailed metrics for daily monitoring
- **Troubleshooting Dashboard**: Deep-dive analysis views

## Exercise 1: Create Primary Monitoring Dashboard

### Step 1: Access CloudWatch Console

**Navigate to dashboard creation**:

1. **AWS Console**: Search "CloudWatch"
2. **Dashboards**: Click "Dashboards" in left sidebar
3. **Create dashboard**: Click "Create dashboard" button
4. **Dashboard name**: `DynamoDB-Production-Monitoring`
5. **Create dashboard**: Click to proceed

{{% notice info %}}
**Screenshot Location**: Add screenshot of CloudWatch console with Dashboards section highlighted
{{% /notice %}}

### Step 2: Add DynamoDB Capacity Widget

**Create capacity utilization monitoring**:

1. **Add widget**: Click "Add widget" button
2. **Widget type**: Select "Line" chart
3. **Data source**: Choose "Metrics"
4. **Browse metrics**: 
   - **AWS/DynamoDB** → **Table Metrics**
   - **Table**: Select `demo-ecommerce-freetier`
   - **Metrics**: Check all these:
     - `ConsumedReadCapacityUnits`
     - `ConsumedWriteCapacityUnits`
     - `ProvisionedReadCapacityUnits`
     - `ProvisionedWriteCapacityUnits`

5. **Configure widget**:
   - **Title**: "DynamoDB Capacity Utilization"
   - **Period**: 5 minutes
   - **Statistic**: Average
   - **Y-axis range**: Auto

{{% notice info %}}
**Screenshot Location**: Add screenshot of widget configuration with DynamoDB capacity metrics selected
{{% /notice %}}

### Step 3: Add Performance Metrics Widget

**Monitor response times and errors**:

1. **Add widget**: Click "Add widget" (second widget)
2. **Widget type**: Line chart
3. **Metrics selection**:
   - **AWS/DynamoDB** → **Table Metrics**
   - **Table**: `demo-ecommerce-freetier`
   - **Select metrics**:
     - `SuccessfulRequestLatency` (Query operations)
     - `SuccessfulRequestLatency` (GetItem operations)
     - `UserErrors`
     - `SystemErrors`
     - `ThrottledRequests`

4. **Widget configuration**:
   - **Title**: "Performance & Error Metrics"
   - **Period**: 5 minutes
   - **Y-axis**: Split axis (latency vs errors)

{{% notice info %}}
**Screenshot Location**: Add screenshot of performance metrics widget configuration
{{% /notice %}}

### Step 4: Add Storage and Item Count Widget

**Track data growth and storage usage**:

1. **Add widget**: Third widget
2. **Widget type**: Line chart  
3. **Metrics**:
   - **AWS/DynamoDB** → **Table Metrics**
   - **Table**: `demo-ecommerce-freetier`
   - **Select**:
     - `ItemCount`
     - `TableSizeBytes`

4. **Configuration**:
   - **Title**: "Storage Utilization"
   - **Period**: 1 hour (slower changing metrics)
   - **Y-axis**: Split axis (count vs bytes)

{{% notice info %}}
**Screenshot Location**: Add screenshot of storage metrics configuration showing ItemCount and TableSizeBytes
{{% /notice %}}

### Step 5: Add Lambda Stream Processing Widget

**Monitor stream processing performance**:

1. **Add widget**: Fourth widget
2. **Metrics**:
   - **AWS/Lambda** → **Function Metrics**
   - **Function**: `demo-dynamodb-stream-processor`
   - **Select**:
     - `Invocations`
     - `Duration`
     - `Errors`
     - `Throttles`

3. **Configuration**:
   - **Title**: "Stream Processing Health"
   - **Period**: 5 minutes

{{% notice info %}}
**Screenshot Location**: Add screenshot of Lambda metrics widget showing stream processing performance
{{% /notice %}}

## Exercise 2: Global Tables Monitoring (if applicable)

### Step 1: Add Replication Metrics

**Monitor cross-region replication**:

1. **Add widget**: Fifth widget
2. **Metrics**:
   - **AWS/DynamoDB** → **Global Table Metrics**
   - **Source/Destination**: Your regions
   - **Select**:
     - `ReplicationLatency`
     - `PendingReplicationCount`

3. **Configuration**:
   - **Title**: "Global Tables Replication"
   - **Period**: 5 minutes

{{% notice info %}}
**Screenshot Location**: Add screenshot of Global Tables replication metrics configuration
{{% /notice %}}

## Exercise 3: Dashboard Customization

### Step 1: Organize Widget Layout

**Optimize dashboard layout**:

1. **Resize widgets**: Drag corners to adjust size
2. **Arrange layout**:
   - **Top row**: Capacity + Performance (most critical)
   - **Second row**: Storage + Lambda processing
   - **Third row**: Global Tables (if applicable)
3. **Widget spacing**: Leave room for annotations

{{% notice info %}}
**Screenshot Location**: Add screenshot of final dashboard layout with all widgets organized
{{% /notice %}}

### Step 2: Configure Time Range and Auto-Refresh

**Set operational parameters**:

1. **Time range**: Set to "Last 3 hours"
2. **Auto-refresh**: Enable 1-minute refresh
3. **Time zone**: Set to your local time zone
4. **Save dashboard**: Click "Save dashboard"

### Step 3: Add Dashboard Annotations

**Add context and documentation**:

1. **Add widget**: Text widget
2. **Widget content**:

```markdown
# DynamoDB Production Dashboard

## Key Metrics:
- **Capacity**: Target <80% utilization
- **Latency**: Target <50ms average
- **Errors**: Target 0% error rate
- **Storage**: Monitor 25GB Free Tier limit

## Alert Thresholds:
- High capacity: >4 RCU/WCU (80% of 5)
- High latency: >100ms sustained
- Any errors: >0 errors per period

Last Updated: [Date]
```

{{% notice info %}}
**Screenshot Location**: Add screenshot of text widget with dashboard documentation
{{% /notice %}}

## Exercise 4: Specialized Dashboards

### Step 1: Create Cost Monitoring Dashboard

**Track Free Tier usage**:

1. **New dashboard**: "DynamoDB-Cost-Analysis"
2. **Add widgets**:
   - **Billing metrics**: EstimatedCharges
   - **Usage metrics**: Free Tier consumption
   - **Capacity trends**: Weekly capacity usage
   - **Storage growth**: Data size over time

### Step 2: Create Troubleshooting Dashboard

**Deep-dive analysis dashboard**:

1. **Dashboard name**: "DynamoDB-Troubleshooting"
2. **Detailed metrics**:
   - **Per-operation latency**: Broken down by operation type
   - **Hot partition detection**: ConsumedCapacity by partition
   - **Error analysis**: Error types and frequencies
   - **Throttling patterns**: When and why throttling occurs

{{% notice info %}}
**Screenshot Location**: Add screenshot of troubleshooting dashboard with detailed operational metrics
{{% /notice %}}

## Exercise 5: Dashboard Sharing and Access

### Step 1: Configure Dashboard Permissions

**Set up team access**:

1. **Dashboard actions**: Click "Actions" menu
2. **Share dashboard**: Select sharing options
3. **Access controls**:
   - **Public**: No (keep private)
   - **Account access**: Specific IAM users/roles
   - **Read-only**: Recommended for most users

### Step 2: Create Dashboard URLs

**Generate shareable links**:

1. **Get shareable URL**: Copy dashboard URL
2. **Embed options**: For inclusion in other tools
3. **Export options**: PNG/PDF for reports

{{% notice info %}}
**Screenshot Location**: Add screenshot of dashboard sharing configuration options
{{% /notice %}}

## Exercise 6: Dashboard Maintenance

### Step 1: Set Up Dashboard Alerts

**Monitor dashboard health**:

1. **Missing data alerts**: When metrics stop reporting
2. **Dashboard access logs**: Who is viewing dashboards
3. **Widget performance**: Load times and responsiveness

### Step 2: Regular Review Process

**Dashboard optimization schedule**:

- **Daily**: Quick health check of all dashboards
- **Weekly**: Review metric relevance and thresholds
- **Monthly**: Optimize layout and add new metrics
- **Quarterly**: Archive unused dashboards

## Dashboard Best Practices

### Widget Organization

**Effective dashboard design**:

| Row | Left Widget | Right Widget |
|-----|-------------|--------------|
| **Top** | **Critical Metrics** | **Performance Trends** |
| | (Capacity, Errors) | (Latency, Success) |
| **Middle** | **Resource Usage** | **Stream Processing** |
| | (Storage, Items) | (Lambda, Events) |
| **Bottom** | **Global Tables & Replication** | |
| | (if applicable) | |

### Color Coding Strategy

**Visual consistency**:
- **Green**: Normal/healthy metrics
- **Yellow**: Warning thresholds approached
- **Red**: Critical issues requiring attention
- **Blue**: Informational/baseline metrics

### Metric Selection Guidelines

**Choose the right metrics**:

| Purpose | Primary Metrics | Secondary Metrics |
|---------|----------------|-------------------|
| **Health** | Errors, Throttles | Success rate, Availability |
| **Performance** | Latency, Duration | Throughput, Concurrency |
| **Capacity** | Consumed vs Provisioned | Utilization percentage |
| **Cost** | Storage size, Operations | Free Tier consumption |

## Troubleshooting Dashboard Issues

### Common Problems

**Dashboard not loading**:
1. **Check region**: Verify correct AWS region
2. **Permissions**: Ensure CloudWatch read access
3. **Browser cache**: Clear cache and reload
4. **Service status**: Check AWS status page

**Missing metrics data**:
1. **Time range**: Adjust time window
2. **Metric delay**: Wait for data propagation
3. **Resource activity**: Generate activity to create metrics
4. **Metric retention**: Check data retention periods

**Performance issues**:
1. **Too many widgets**: Reduce widget count
2. **Long time ranges**: Shorten time periods
3. **High resolution**: Use lower resolution for overview
4. **Auto-refresh**: Reduce refresh frequency

{{% notice warning %}}
**Free Tier Note**: CloudWatch provides 10 custom metrics and 3 dashboards free. Plan your dashboard strategy accordingly.
{{% /notice %}}

## Exercise Summary

You've created comprehensive monitoring dashboards:

- ✅ **Primary dashboard** with essential DynamoDB metrics
- ✅ **Performance monitoring** for latency and errors
- ✅ **Capacity tracking** to prevent throttling
- ✅ **Storage monitoring** for cost management
- ✅ **Stream processing** health visibility
- ✅ **Specialized dashboards** for cost and troubleshooting

{{% notice success %}}
**Dashboard Mastery**: You now have production-grade visual monitoring that provides complete visibility into your DynamoDB system!
{{% /notice %}}

## Next Steps

With dashboards providing visual monitoring, the next step is setting up proactive alerting to detect issues before they impact users. We'll configure intelligent alarms and notification systems.
