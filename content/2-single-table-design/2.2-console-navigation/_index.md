---
title : "2.2 Console Navigation"
date : "2025-08-11"
weight : 22
chapter : false
pre : " <b> 2.2 </b> "
---

# DynamoDB Console Navigation

🖥️ **Quick guide to navigate DynamoDB Console for Single Table Design**

## Accessing Your Table

### Step 1: Navigate to DynamoDB Service

1. **Open AWS Console**: https://console.aws.amazon.com
2. **Verify Region**: Ensure you're in **US East (N. Virginia)**
3. **Access DynamoDB**: Services → Database → DynamoDB

![DynamoDB-search](/images/2/DynamoDB-search.png?featherlight=false&width=90pc)

### Step 2: Find Your Workshop Table

1. **Click "Tables"** in the left sidebar
2. **Find table**: `demo-ecommerce-freetier`
3. **Verify Status**: Should show "Active"
4. **Click table name** to enter table details

![DynamoDB-Server](/images/2/DynamoDB-Server.png?featherlight=false&width=90pc)

## Table Overview Dashboard

### Understanding the Table Layout

When you click on your table, you'll see several tabs:

| Tab | Purpose | What You'll Use It For |
|-----|---------|------------------------|
| **Overview** | Table configuration | Check status, keys, capacity |
| **Items** | Data management | Create, view, edit items |
| **Metrics** | Performance data | Monitor usage and costs |
| **Indexes** | GSI management | View Global Secondary Indexes |
| **Global tables** | Multi-region setup | Check replication status |

{{% notice info %}}
**Screenshot Location**: Add screenshot of table overview page showing all tabs
{{% /notice %}}

### Key Information to Note

**Table Configuration**:
- **Table name**: `demo-ecommerce-freetier`
- **Partition key**: `PK` (String)
- **Sort key**: `SK` (String)
- **Table status**: Active
- **Item count**: Currently 0 (empty table)

{{% notice info %}}
**Screenshot Location**: Add screenshot of table details showing PK and SK configuration
{{% /notice %}}

## Items Tab - Your Data Workspace

### Accessing the Items View

1. **Click "Items" tab** 
2. **View table structure**: Currently empty
3. **Note the columns**: PK, SK, and any additional attributes

This is where you'll:
- ✅ Create new items (users, products, orders)
- ✅ View existing data
- ✅ Edit item attributes
- ✅ Delete items if needed

{{% notice info %}}
**Screenshot Location**: Add screenshot of empty Items tab showing PK and SK columns
{{% /notice %}}

### Creating Items Interface

**To create a new item**:
1. **Click "Create item"** button
2. **Choose input method**:
   - **Form view**: Point-and-click interface
   - **JSON view**: Direct JSON editing (recommended)
3. **Switch to JSON view** for easier data entry

{{% notice info %}}
**Screenshot Location**: Add screenshot of "Create item" dialog showing Form vs JSON view options
{{% /notice %}}

## JSON View for Data Entry

### Understanding JSON Format

When creating items, you'll use this JSON structure:

```json
{
  "PK": "USER#user123",
  "SK": "PROFILE",
  "name": "John Doe",
  "email": "john@example.com",
  "created_at": "2025-08-11T10:00:00Z"
}
```

**Key points**:
- **PK and SK**: Always required (your composite primary key)
- **Additional attributes**: Add as needed for each entity type
- **Data types**: Strings, numbers, booleans, lists, maps supported

{{% notice info %}}
**Screenshot Location**: Add screenshot of JSON view in create item dialog with sample data
{{% /notice %}}

## Query Interface

### Accessing Query Functionality

**To query your table**:
1. **Go to Items tab**
2. **Click "Query" button** (next to Create item)
3. **Choose query type**:
   - **Table query**: Query main table
   - **Index query**: Query GSI

{{% notice info %}}
**Screenshot Location**: Add screenshot showing Query button location and query type selection
{{% /notice %}}

### Query Parameters

**For table queries, you'll specify**:
- **Partition key (PK)**: Exact value (e.g., `USER#user123`)
- **Sort key (SK)**: Optional conditions:
  - Exact match: `PROFILE`
  - Begins with: `ORDER#`
  - Between: Range queries

{{% notice info %}}
**Screenshot Location**: Add screenshot of query dialog showing PK and SK input fields
{{% /notice %}}

## Global Secondary Index (GSI) Navigation

### Viewing GSI Configuration

1. **Click "Indexes" tab**
2. **View GSI1**: Used for category-based queries
3. **View GSI2**: Used for status/price-based queries

**GSI Structure**:
- **GSI1**: `GSI1PK` (Partition) + `GSI1SK` (Sort)
- **GSI2**: `GSI2PK` (Partition) + `GSI2SK` (Sort)

{{% notice info %}}
**Screenshot Location**: Add screenshot of Indexes tab showing GSI1 and GSI2 configuration
{{% /notice %}}

### Querying GSIs

**To query a GSI**:
1. **Click "Query" in Items tab**
2. **Select "Query (index)"**
3. **Choose GSI**: GSI1 or GSI2
4. **Enter GSI key values**

{{% notice info %}}
**Screenshot Location**: Add screenshot of GSI query interface with index selection dropdown
{{% /notice %}}

## Metrics and Monitoring

### Checking Usage and Performance

1. **Click "Metrics" tab**
2. **Monitor key metrics**:
   - **Consumed read/write capacity**
   - **Throttled requests** (should be 0)
   - **Item count** (increases as you add data)

**Why this matters**:
- ✅ Stay within Free Tier limits
- ✅ Monitor performance
- ✅ Detect any issues early

{{% notice info %}}
**Screenshot Location**: Add screenshot of Metrics tab showing capacity utilization graphs
{{% /notice %}}

## Quick Actions Reference

### Common Console Actions

| Action | Location | Purpose |
|--------|----------|---------|
| **Create Item** | Items tab → Create item | Add new data |
| **Query Table** | Items tab → Query | Search by PK/SK |
| **Query GSI** | Items tab → Query (index) | Search by GSI keys |
| **View Metrics** | Metrics tab | Monitor performance |
| **Check Capacity** | Overview tab | Verify provisioned capacity |

## Console Tips

### Efficiency Tips

1. **Use JSON view** for faster item creation
2. **Copy/paste** item structures for consistency
3. **Use Query, not Scan** for better performance
4. **Check metrics regularly** to monitor usage

### Navigation Shortcuts

- **Tables list**: DynamoDB home → Tables
- **Quick table access**: Bookmark your table URL
- **Region switching**: Use region selector in top-right
- **Service search**: Use Ctrl+K for quick service access

{{% notice tip %}}
**Pro Tip**: Keep the DynamoDB console open in a separate browser tab during the workshop for quick access between exercises.
{{% /notice %}}

## Ready for Data Creation

Now that you're familiar with the console interface, you're ready to start creating your e-commerce data model. In the next section, we'll create users, products, and orders using the patterns you've learned.

{{% notice warning %}}
**Before Starting**: Make sure you're in the correct table (`demo-ecommerce-freetier`) and understand the difference between Query and Scan operations.
{{% /notice %}}
