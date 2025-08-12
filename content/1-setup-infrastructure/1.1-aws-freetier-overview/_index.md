---
title : "1.1 AWS Free Tier Overview"
date : "2025-08-11"
weight : 11
chapter : false
pre : " <b> 1.1 </b> "
---

# AWS Free Tier Overview

🆓 **Understanding AWS Free Tier limits and ensuring zero-cost workshop experience**

## What is AWS Free Tier?

AWS Free Tier provides new customers access to AWS services at no charge for a limited time. It includes three types of offerings:

### 1. 12 Months Free
Available for 12 months following your AWS sign-up date:
- **Amazon DynamoDB**: 25 GB storage, 25 WCU, 25 RCU
- **AWS Lambda**: 1 million requests per month
- **Amazon CloudWatch**: 10 metrics, 10 alarms, 1 million API requests

### 2. Always Free
Available to all AWS customers indefinitely:
- **DynamoDB**: 25 GB storage (always free)
- **Lambda**: 1 million requests, 400,000 GB-seconds compute time
- **CloudWatch**: 10 custom metrics and 10 alarms

### 3. Trials
Short-term free access to certain services

## Free Tier Limits for This Workshop

```text
Service Usage Breakdown:
┌─────────────────┬──────────────┬──────────────┬─────────────┐
│ Service         │ Free Tier    │ Our Usage    │ Safety %    │
├─────────────────┼──────────────┼──────────────┼─────────────┤
│ DynamoDB RCU    │ 25 units     │ 15 units     │ 60%         │
│ DynamoDB WCU    │ 25 units     │ 15 units     │ 60%         │
│ DynamoDB Storage│ 25 GB        │ <1 GB        │ 4%          │
│ Lambda Requests │ 1M/month     │ ~100/day     │ 0.3%        │
│ Lambda Duration │ 400K GB-sec  │ <1K GB-sec   │ 0.25%       │
│ CloudWatch      │ 10 metrics   │ 6 metrics    │ 60%         │
│ Data Transfer   │ 1 GB         │ <100 MB      │ 10%         │
└─────────────────┴──────────────┴──────────────┴─────────────┘

Overall Free Tier Usage: ~50% = 100% SAFE! 🛡️
```

{{% notice tip %}}
**Cost Protection**: We're using only 50% of available Free Tier limits, ensuring zero charges throughout the workshop.
{{% /notice %}}

## Regional Considerations

### Primary Region: US East (N. Virginia)
- **Why chosen**: Highest Free Tier allowances
- **DynamoDB**: Full 25 RCU/WCU available
- **Lambda**: Full 1M requests available
- **Best for**: Primary workloads and testing

### Secondary Region: EU West (Ireland)
- **Purpose**: Global Tables replication
- **Free Tier**: Same limits as primary region
- **Usage**: Minimal (replica table only)
- **Cost impact**: Near zero

## Monitoring Free Tier Usage

### AWS Billing Dashboard
1. **Navigate to**: AWS Console → Billing & Cost Management
2. **Free Tier usage**: Track current consumption
3. **Alerts**: Set up when approaching 80% of limits
4. **Forecasting**: Predict monthly usage

### CloudWatch Billing Alarms
Automatic alerts when:
- DynamoDB consumed units > 20 (80% of limit)
- Lambda invocations > 800K/month (80% of limit)
- Overall estimated charges > $1.00

## Free Tier Best Practices

### 1. Monitor Regularly
- Check Free Tier dashboard daily during workshop
- Set up billing alerts before deployment
- Monitor resource utilization in CloudWatch

### 2. Use Appropriate Capacity
```text
DynamoDB Provisioned Capacity:
- Read Capacity Units (RCU): 5 (well under 25 limit)
- Write Capacity Units (WCU): 5 (well under 25 limit)
- On-Demand: NOT recommended (can exceed Free Tier)
```

### 3. Clean Up Resources
- Delete test data regularly
- Remove unused Lambda functions
- Clean up CloudWatch logs older than 7 days

## Common Free Tier Pitfalls to Avoid

{{% notice warning %}}
**Avoid These Mistakes**: 1. **On-Demand DynamoDB**: Can quickly exceed Free Tier
2. **Multiple Regions**: Deploying same resources in >2 regions
3. **Large Data Sets**: Uploading >25GB to DynamoDB
4. **Forgot Cleanup**: Leaving resources running beyond workshop
{{% /notice %}}

### Mistake #1: Provisioned vs On-Demand
```text
❌ WRONG: DynamoDB On-Demand
- Pay per request (can be expensive)
- Unpredictable costs
- No Free Tier protection

✅ CORRECT: DynamoDB Provisioned
- Fixed capacity units
- Predictable costs
- Protected by Free Tier limits
```

### Mistake #2: Resource Multiplication
```text
❌ WRONG: Deploy to 5 regions
- 5x resource consumption
- 5x Free Tier usage
- Likely to exceed limits

✅ CORRECT: Deploy to 2 regions max
- Minimal resource usage
- Well within Free Tier
- Global availability maintained
```

## Emergency Procedures

### If You See Charges Appearing

1. **STOP IMMEDIATELY**: Pause all workshop activities
2. **Check Billing Dashboard**: Identify source of charges
3. **Review Resources**: List all active resources
4. **Contact Support**: Use AWS Free Tier support if needed
5. **Delete Resources**: Remove anything outside workshop scope

### Quick Resource Audit Commands

```bash
# List all DynamoDB tables
aws dynamodb list-tables --region us-east-1

# List all Lambda functions  
aws lambda list-functions --region us-east-1

# Check CloudFormation stacks
aws cloudformation list-stacks --region us-east-1
```

## Pre-Workshop Checklist

Before starting the infrastructure deployment:

- [ ] AWS account created and verified
- [ ] Free Tier eligibility confirmed (account <12 months old)
- [ ] Billing alerts configured
- [ ] Free Tier dashboard bookmarked
- [ ] Emergency contact information ready
- [ ] Understanding of resource limits confirmed

{{% notice info %}}
**Ready to Continue**: Once you've reviewed the Free Tier limits and understand the safety measures, proceed to the Architecture Overview to understand what we'll be building.
{{% /notice %}}
