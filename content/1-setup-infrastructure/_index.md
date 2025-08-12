---
title : "1. Setup & Infrastructure Deployment"
date : "2025-08-11"
weight : 1
chapter : true
pre : " <b> 1. </b> "
---

# Setup & Infrastructure Deployment

🚀 **Complete guide for setting up AWS infrastructure for DynamoDB Advanced Patterns workshop**

This module provides the foundational setup required for the DynamoDB Advanced Patterns workshop, ensuring all participants have a working environment using AWS Free Tier.

## 📋 Learning Objectives

#### By the end of this module, you will:

- ✅ Verify AWS account and Free Tier eligibility
- ✅ Navigate AWS Console efficiently
- ✅ Deploy infrastructure via CloudFormation
- ✅ Verify all resources created successfully
- ✅ Setup monitoring and billing alerts

## ⏱️ Module Duration: 90 minutes

- **Theory**: 15 minutes
- **Demo**: 25 minutes  
- **Hands-on**: 40 minutes
- **Review**: 10 minutes

## 📚 AWS Free Tier Overview

```
AWS Free Tier Limits for our workshop:
┌─────────────────┬──────────────┬──────────────┐
│ Service         │ Free Tier    │ Our Usage    │
├─────────────────┼──────────────┼──────────────┤
│ DynamoDB        │ 25 RCU/WCU   │ 15 RCU/WCU   │
│ Lambda          │ 1M requests  │ ~100/day     │
│ CloudWatch      │ 10 metrics   │ 6 metrics    │
│ Data Transfer   │ 1GB          │ <100MB       │
└─────────────────┴──────────────┴──────────────┘

We're using only 60% of limits = 100% safe!
```

{{% notice warning %}}
**CRITICAL SAFETY NOTE**: We're using AWS Free Tier exclusively. If you see ANY charges appearing during this workshop, please stop immediately and verify your configuration. All resources will be deployed within Free Tier limits.
{{% /notice %}}

## 🏗️ Architecture Overview

#### We'll deploy infrastructure across two AWS regions:

![global-tables-architecture](/images/1/global-tables-architecture-improved.png?featherlight=false&width=90pc)

## 🎯 What We'll Build

#### We're building a simplified e-commerce platform with:

- **Users**: Customer profiles and authentication
- **Products**: Catalog with categories and pricing
- **Orders**: Shopping cart and order management
- **Real-time processing**: Stream-based updates
- **Global availability**: Multi-region deployment

## 📦 Resources Created

#### This CloudFormation template will create:

- **DynamoDB Table**: Global table with streams enabled
- **Lambda Function**: Stream processor for real-time updates
- **IAM Roles**: Secure access policies
- **CloudWatch Dashboard**: Monitoring and metrics
- **Billing Alerts**: Cost protection mechanisms

## 🚀 Prerequisites

#### Before starting, ensure you have:

- AWS Account with Free Tier eligibility
- Administrative access to AWS Console
- Basic understanding of AWS services
- Modern web browser (Chrome, Firefox, Safari)

#### Let's begin with the infrastructure setup that will support our multi-region DynamoDB implementation.

- You can choose **Personal** or **Business** account

![Create Account](/images/1/0009.png?featherlight=false&width=90pc)

#### Add payment method

- Enter your credit card information and select **Verify and Add**.
    - ***Note**: You can choose a different address for your account by selecting **Use a new address** before **Verify and Add**.*

![Create Account](/images/1/00010.png?featherlight=false&width=90pc)
#### Verify your phone number

1. Enter the phone number.
2. Enter the security check code then select **Call me now**.
3. AWS will contact and verify account opening.

![Create Account](/images/1/00011.png?featherlight=false&width=90pc)

#### Select Support Plan

- In the **Select a support plan** page, select an effective plan, to compare plans, see [Compare AWS Support Plans](https://aws.amazon.com/premiumsupport/plans/ ).

#### Wait for your account to be activated

- After selecting **Support plan**, the account is usually activated after a few minutes, but the process can take up to 24 hours. You will still be able to log in to your AWS account at this time, the AWS Home page may show a “Complete Sign Up” button during this time, even if you have completed all the steps in the registration section.
- After receiving an email confirming your account has been activated, you can access all AWS services.       
  
#### Important

- The following AWS Identity and Access Management (IAM) actions will reach the end of standard support on July 2023: `aws-portal:ModifyAccount` and `aws-portal:ViewAccount`. See the [Using fine-grained AWS Billing actions](link_to_documentation) to replace these actions with fine-grained actions so you have access to AWS Billing, AWS Cost Management, and AWS accounts consoles.
- If you created your AWS account or AWS Organizations Management account before March 6, 2023, the fine-grained actions will be effective starting July 2023. We recommend you to add the fine-grained actions, but not remove your existing permissions with `aws-portal` or `purchase-orders` prefixes.
- If you created your AWS account or AWS Organizations Management account on or after March 6, 2023, the fine-grained actions are effective immediately.
- AWS assigns the following unique identifiers to each AWS account:
- **AWS account ID**: A 12-digit number, such as `012345678901`, that uniquely identifies an AWS account. Many AWS resources include the account ID in their Amazon Resource Names (ARNs). The account ID portion distinguishes resources in one account from the resources in another account. If you're an AWS Identity and Access Management (IAM) user, you can sign in to the AWS Management Console using either the account ID or account alias. While account IDs, like any identifying information, should be used and shared carefully, they are not considered secret, sensitive, or confidential information.
- **Canonical user ID**: An alpha-numeric identifier, such as `79a59df900b949e55d96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2be`, that is an obfuscated form of the AWS account ID. You can use this ID to identify an AWS account when granting cross-account access to buckets and objects using Amazon Simple Storage Service (Amazon S3). You can retrieve the canonical user ID for your AWS account as either the root user or an IAM user.
- You must be authenticated with AWS to view these identifiers.

#### Warning

**Do not provide your AWS credentials** (including passwords and access keys) to a third party that needs your AWS account identifiers to share AWS resources with you. Doing so would give them the same access to the AWS account that you have.
