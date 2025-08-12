---
title : "7. Cleanup Resources"
date : "2025-08-11"
weight : 7
chapter : true
pre : " <b> 7. </b> "
---

# Resource Cleanup

In this section, you will learn how to clean up resources on AWS Cloud to prevent incurring unnecessary costs.

## Why Cleanup is Important

- **Cost Control**: Prevent unexpected AWS charges
- **Resource Management**: Remove unused infrastructure
- **Best Practices**: Learn proper resource lifecycle management

## 1. Delete the DynamoDB Table Created in the Lab

- Access the **DynamoDB Management Console**
- On the left navigation bar, select **Tables**
- Select the DynamoDB table `demo-ecommerce-freetier` related to the lab
- Click on **Actions**
- Choose **Delete table**
- Type the table name to confirm
- Confirm by clicking **Delete**

## 2. Delete CloudWatch Resources Created in the Lab

- Access the **CloudWatch Management Console**
- On the left navigation bar, go to **Dashboards**
- Select all dashboards related to the lab
- Click on **Actions**
- Choose **Delete dashboards**
- Confirm deletion by clicking **Delete**
- On the left navigation bar, go to **Alarms**
- Select all alarms related to the lab
- Click on **Actions**
- Choose **Delete**
- Confirm deletion by clicking **Delete**

## 3. Delete Lambda Functions (if created)

- Access the **Lambda Management Console**
- On the left navigation bar, navigate to **Functions**
- Select the Lambda functions related to the lab
- Click on **Actions**
- Choose **Delete function**
- Confirm by clicking **Delete**

## 4. Delete SNS Topics (if created)

- Access the **SNS Management Console**
- On the left navigation bar, select **Topics**
- Select all SNS topics related to the lab
- Click on **Actions**
- Choose **Delete**
- Type "delete me" to confirm
- Confirm deletion by clicking **Delete**

## 5. Verify Billing and Costs

- Access the **Billing and Cost Management Console**
- Check the current month charges
- Verify that all charges show **$0.00**
- Confirm no unexpected services are running

## Final Verification

✅ **Cleanup Checklist:**

- DynamoDB table deleted
- CloudWatch dashboards and alarms removed
- Lambda functions deleted (if any)  
- SNS topics deleted (if any)
- Billing shows $0.00 charges

---

**Congratulations!** You have successfully completed the DynamoDB Advanced Patterns workshop and cleaned up all resources.
