---
title : "2. Triển khai Single Table Design"
date : "2025-08-11"
weight : 2 
chapter : true
pre : " <b> 2. </b> "
---

# Triển khai Single Table Design

📊 **Học cách triển khai các mẫu Single Table Design của DynamoDB để tối ưu hiệu suất và hiệu quả chi phí**

## Tổng quan

- Single Table Design là một phương pháp cách mạng trong việc mô hình hóa dữ liệu trong DynamoDB. Thay vì sử dụng nhiều bảng như trong cơ sở dữ liệu quan hệ, chúng ta lưu trữ tất cả các loại entity (Users, Products, Orders) trong một bảng sử dụng composite keys cho các mối quan hệ.

## Tại sao Single Table Design?

#### Vấn đề của Phương pháp Quan hệ Truyền thống:
- **Nhiều bảng** = Nhiều truy vấn = Độ trễ cao hơn
- **JOINs rất tốn kém** và không có sẵn trong DynamoDB
- **Hiệu suất không nhất quán** qua các mẫu truy vấn khác nhau
- **Chi phí cao hơn** từ việc quản lý nhiều bảng

#### Lợi ích DynamoDB Single Table:
- **Truy vấn đơn** lấy dữ liệu liên quan
- **Hiệu suất nhất quán** qua tất cả các mẫu truy cập
- **Chi phí thấp hơn** với ít bảng và operations hơn
- **Giao dịch atomic** qua các loại entity

## Mục tiêu Học tập
#### Sau khi hoàn thành module này, bạn sẽ:

- ✅ Hiểu các nguyên tắc và lợi ích của Single Table Design
- ✅ Thiết kế composite keys (PK + SK) cho nhiều loại entity
- ✅ Tạo và truy vấn dữ liệu sử dụng DynamoDB Console
- ✅ Triển khai Global Secondary Indexes (GSI) cho các mẫu truy cập linh hoạt
- ✅ Phân tích metrics hiệu suất và chi phí

## Thời lượng Module: 90 phút

- **Lý thuyết**: 20 phút - Khái niệm và nguyên tắc cốt lõi
- **Demo**: 25 phút - Điều hướng Console và tạo dữ liệu
- **Thực hành**: 35 phút - Tạo dữ liệu e-commerce của riêng bạn
- **Ôn tập**: 10 phút - Phân tích hiệu suất và Q&A

## Tổng quan Mô hình Dữ liệu E-commerce

#### Chúng ta sẽ xây dựng một nền tảng e-commerce đơn giản với các entity này:

| PK | SK | Entity | Data |
|---|---|---|---|
| **USER#user1** | **PROFILE** | User | name, email, phone |
| **USER#user1** | **ORDER#ord1** | Order | status, total, date |
| **PRODUCT#p1** | **DETAILS** | Product | name, price, category |
| **ORDER#ord1** | **ITEM#p1** | OrderItem | quantity, price, product |

## Các Mẫu Truy cập Chúng ta sẽ Triển khai

| Mẫu | Mô tả | Phương pháp Truy vấn |
|---------|-------------|--------------|
| **1** | Lấy user profile | PK = USER#id, SK = PROFILE |
| **2** | Lấy orders của user | PK = USER#id, SK begins_with ORDER# |
| **3** | Lấy chi tiết order với items | PK = ORDER#id |
| **4** | Lấy products theo category | GSI1: CATEGORY# queries |
| **5** | Lấy products theo price range | GSI2: PRICE# queries |
| **6** | Lấy orders theo status | GSI2: STATUS# queries |

## Khái niệm Chính

#### Chiến lược Composite Keys
- **Partition Key (PK)**: Nhóm các items liên quan với nhau
- **Sort Key (SK)**: Cho phép range queries và relationships
- **GSI Keys**: Cho phép các mẫu truy vấn bổ sung

#### Entity Namespacing
- **USER#**: Tất cả dữ liệu liên quan đến user
- **PRODUCT#**: Tất cả dữ liệu liên quan đến product  
- **ORDER#**: Tất cả dữ liệu liên quan đến order
- **CATEGORY#**: Nhóm products
- **STATUS#**: Nhóm trạng thái orders

{{% notice tip %}}
**Triết lý Thiết kế**: Trong Single Table Design, chúng ta mô hình hóa cấu trúc bảng dựa trên **CÁCH chúng ta sẽ truy vấn dữ liệu**, không phải cách chúng ta sẽ lưu trữ nó. Điều này ngược lại với thiết kế cơ sở dữ liệu quan hệ!
{{% /notice %}}

## Những gì Bạn sẽ Xây dựng

#### Đến cuối module này, bạn sẽ đã tạo:

- **User profiles** với cấu trúc key phù hợp
- **Product catalog** với category và price indexing
- **Order management** với item relationships
- **Truy vấn hiệu quả** sử dụng table và GSI patterns
- **Performance insights** từ CloudWatch metrics

{{% notice warning %}}
**An toàn Chi phí**: Tất cả bài tập sử dụng dữ liệu tối thiểu và nằm trong giới hạn AWS Free Tier. Giám sát CloudWatch dashboard để theo dõi usage.
{{% /notice %}}

## Điều kiện Tiên quyết

#### Trước khi bắt đầu module này, đảm bảo bạn có:

- [ ] Đã hoàn thành Module 1: Infrastructure Setup
- [ ] DynamoDB table `demo-ecommerce-freetier` đang Active
- [ ] Quyền truy cập AWS Console với DynamoDB permissions
- [ ] Hiểu biết cơ bản về các khái niệm NoSQL

Sẵn sàng cách mạng hóa phương pháp data modeling của bạn? Hãy khám phá Single Table Design!
