---
title : "1.3 CloudFormation Deployment"
date : "2025-08-11"
weight : 13
chapter : false
pre : " <b> 1.3 </b> "
---

# CloudFormation Deployment

🚀 **Step-by-step guide to deploy AWS infrastructure using Infrastructure as Code**

## Overview

In this section, you'll deploy the complete DynamoDB workshop infrastructure using AWS CloudFormation. This approach ensures consistent, reproducible deployments and follows AWS best practices.

## Prerequisites

Before starting deployment, ensure:

- AWS Console access with administrative permissions
- Verified region: **US East (N. Virginia) us-east-1**
- Current billing status: **$0.00**
- CloudFormation template downloaded

## CloudFormation Template Overview

Our template creates the following resources:[Download CloudFormation Template](/files/infrastructure.yaml)

### Core Infrastructure
- **DynamoDB Table**: Single table with Global Tables enabled
- **Lambda Function**: Stream processor for real-time events
- **IAM Roles**: Least privilege access policies
- **DynamoDB Streams**: Change data capture configuration

### Monitoring & Alerting
- **CloudWatch Dashboard**: Real-time metrics visualization
- **CloudWatch Alarms**: Threshold-based alerting
- **Billing Alerts**: Cost protection mechanisms

### Security Configuration
- **IAM Policies**: Fine-grained permissions
- **Resource Encryption**: Data protection at rest
- **VPC Integration**: Network isolation (optional)

## Step-by-Step Deployment

### Step 1: Access CloudFormation Service

1. **Navigate to CloudFormation**
   - Open AWS Management Console
   - Search for "CloudFormation" or find it in services menu
   - Ensure you're in **US East (N. Virginia)** region

2. **Create New Stack**
   - Click "Create stack" button
   - Select "With new resources (standard)"

![create-stack](/images/1/create-stack.png?featherlight=false&width=90pc)

### Step 2: Upload Template

1. **Choose Template Source**
   - Select "Upload a template file"
   - Click "Choose file" button
   - Select the `infrastructure.yaml` file

2. **Validate Template**
   - CloudFormation will automatically validate syntax
   - Review template details if needed
   - Click "Next" to proceed

![Choose_Template](/images/1/Choose-Template.png?featherlight=false&width=90pc)

### Step 3: Configure Stack Parameters

**Stack Details:**
```
Stack name: demo-dynamodb-freetier
Description: DynamoDB Advanced Patterns Workshop Infrastructure
```

**Parameters:**

![ParametersChoose_Template](/images/1/Parameters.png?featherlight=false&width=90pc)

**Resource Configuration:**
```
ReadCapacityUnits: 5
WriteCapacityUnits: 5
StreamViewType: NEW_AND_OLD_IMAGES
```

### Step 4: Configure Stack Options

**Tags (Optional):**
```
Workshop: DynamoDB-Advanced-Patterns
Environment: Demo
CostCenter: Learning
```

**Permissions:**
- Use existing service role (if available)
- Or allow CloudFormation to create new role

**Advanced Options:**
- Keep all defaults
- Rollback on failure: Enabled
- Stack creation timeout: 10 minutes

### Step 5: Review and Deploy

1. **Review Configuration**
   - Verify all parameters are correct
   - Check resource list matches expectations
   - Confirm estimated costs ($0.00 for Free Tier)

2. **Acknowledge Capabilities**
   - ✅ Check: "I acknowledge that AWS CloudFormation might create IAM resources"
   - ✅ Check: "I acknowledge that AWS CloudFormation might create IAM resources with custom names"

3. **Create Stack**
   - Click "Create stack" button
   - Deployment begins immediately

![review-deploy](/images/1/review-deploy.png?featherlight=false&width=90pc)

## Monitoring Deployment

### Stack Events Tab
Monitor real-time deployment progress:

![Stack-Events](/images/1/Stack-Events.png?featherlight=false&width=90pc)

### Expected Timeline
- **Total Duration**: 5-7 minutes
- **IAM Resources**: 1-2 minutes
- **DynamoDB Table**: 2-3 minutes
- **Lambda Function**: 1-2 minutes
- **CloudWatch Components**: 1-2 minutes

## Troubleshooting Common Issues

### Issue: Insufficient Permissions
**Symptoms**: CREATE_FAILED for IAM resources
**Solution**: 
- Verify account has administrator access
- Check IAM permissions for CloudFormation
- Use root account if necessary (for workshop only)

### Issue: Resource Limits Exceeded
**Symptoms**: CREATE_FAILED for DynamoDB or Lambda
**Solution**:
- Check Free Tier usage in billing console
- Verify no existing resources consuming limits
- Contact AWS support if needed

### Issue: Region Mismatch
**Symptoms**: Template validation errors
**Solution**:
- Verify region is us-east-1
- Check all parameters are region-appropriate
- Restart deployment in correct region

## Verification Steps

After successful deployment:

1. **Stack Status**: CREATE_COMPLETE ✅
2. **Navigate to Outputs tab**
3. **Record important values**:
   - Table Name
   - Lambda Function ARN
   - Dashboard URL
   - Stream ARN

{{% notice success %}}
**Deployment Complete!** 
Your DynamoDB Advanced Patterns infrastructure is now running. In the next section, we'll verify all components are working correctly.
{{% /notice %}}

## Next Steps

1. Verify DynamoDB table is active
2. Test Lambda function deployment
3. Check CloudWatch dashboard
4. Confirm zero billing charges
5. Begin data modeling exercises
