---
title : "5.2 Cost Analysis & Optimization"
date : "2025-08-11"
weight : 53
chapter : false
pre : " <b> 5.2 </b> "
---

# Cost Analysis & Free Tier Monitoring

💰 **Monitor AWS Free Tier usage to avoid unexpected charges**

## Overview

Learn to track your DynamoDB costs and stay within Free Tier limits during the workshop.

## Free Tier Limits

| Service | Free Tier Limit | Workshop Usage | Status |
|---------|-----------------|----------------|---------|
| **DynamoDB Read** | 25 RCU/month | ~5-10 RCU | ✅ Safe |
| **DynamoDB Write** | 25 WCU/month | ~5-10 WCU | ✅ Safe |
| **DynamoDB Storage** | 25 GB | <1 GB | ✅ Safe |
| **Lambda Requests** | 1M/month | <1,000 | ✅ Safe |
| **CloudWatch Metrics** | 10 custom | 5-8 used | ✅ Safe |

## Exercise 1: Check Current Costs

### Step 1: Access Billing Dashboard

1. **AWS Console**: Click account name (top-right)
2. **Billing and Cost Management**: Select from dropdown
3. **Cost Explorer**: Click "Cost Explorer" in left sidebar
4. **Expected result**: $0.00 for all services

### Step 2: View Free Tier Usage

1. **Billing console**: Click "Free Tier" tab
2. **Check usage**:
   - **DynamoDB**: Should show low usage percentages
   - **Lambda**: Minimal invocations
   - **CloudWatch**: Basic metrics usage

## Exercise 2: Set Up Billing Alerts

### Step 1: Enable Billing Alerts

1. **Billing preferences**: Navigate to preferences
2. **Billing alerts**: Enable "Receive Free Tier Usage Alerts"
3. **Email**: Confirm notification email
4. **Thresholds**: Set alerts at 80% of Free Tier limits

### Step 2: Create Cost Budget

1. **AWS Budgets**: Navigate to Budgets service
2. **Create budget**:
   - **Budget type**: Cost budget
   - **Amount**: $1.00 (safety buffer)
   - **Period**: Monthly
   - **Alert at**: $0.80 (80% threshold)

{{% notice warning %}}
**Important**: This workshop is designed to stay within Free Tier. Any charges indicate unexpected usage.
{{% /notice %}}

## Cost Optimization Tips

### Keep Costs at Zero

- **Monitor usage**: Check Free Tier dashboard weekly
- **Clean up resources**: Delete resources when workshop complete
- **Avoid over-provisioning**: Stick to workshop configurations
- **Set alerts**: Get notified before any charges

### Workshop Resource Cleanup

**When finished with workshop**:

1. **Delete DynamoDB table**: Removes storage costs
2. **Delete Lambda functions**: Removes compute costs  
3. **Delete CloudWatch dashboards**: Removes monitoring costs
4. **Remove IAM roles**: Clean up permissions

{{% notice success %}}
**Cost Control**: Following workshop instructions keeps you within Free Tier limits.
{{% /notice %}}

## Troubleshooting Costs

### If You See Charges

1. **Check region**: Ensure using correct AWS region
2. **Review resources**: Look for unexpected resource creation
3. **Contact support**: AWS Free Tier support available
4. **Stop resources**: Immediately stop any billable resources

### Common Cost Issues

- **Wrong region**: Resources in non-Free Tier regions
- **Over-provisioning**: Capacity above Free Tier limits
- **Forgotten resources**: Resources left running after workshop
- **Data transfer**: Cross-region data movement costs

## Next Steps

With cost monitoring in place, you can continue the workshop with confidence that you'll stay within Free Tier limits.
