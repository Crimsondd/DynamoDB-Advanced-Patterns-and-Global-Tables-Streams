---
title : "4.2 Lambda Function Setup"
date : "2025-08-11"
weight : 42
chapter : false
pre : " <b> 4.2 </b> "
---

## Tạo Lambda Xử Lý Stream

⚙️ **Xây dựng hàm Lambda để xử lý các sự kiện stream DynamoDB trong thời gian thực**

### Tổng quan

AWS Lambda cung cấp khả năng tính toán serverless để xử lý các sự kiện stream DynamoDB. Hàm của bạn sẽ tự động kích hoạt khi các mục trong bảng thay đổi, cho phép các mẫu xử lý thời gian thực.

### Yêu cầu của Hàm

**Cấu hình Tối ưu Free Tier**:
- **Runtime**: Python 3.9 (đáng tin cậy và được hỗ trợ tốt)
- **Memory**: 128 MB (tối thiểu cho Free Tier)
- **Timeout**: 30 giây (đủ cho xử lý stream)
- **Concurrent executions**: 10 (an toàn trong Free Tier)

## Bài tập 1: Tạo Hàm Lambda

### Bước 1: Truy cập Lambda Console

**Điều hướng đến dịch vụ Lambda**:

1. **AWS Console**: Tìm kiếm "Lambda"
2. **Functions**: Nhấp vào "Functions" trong thanh bên trái
3. **Create function**: Nhấp vào nút "Create function"
4. **Author from scratch**: Chọn tùy chọn này

![4.2.1](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/4.2.1.png?featherlight=false&width=90pc)

### Bước 2: Cấu hình Cài đặt Cơ bản

**Cấu hình hàm**:

1. **Function name**: `demo-dynamodb-stream-processor`
2. **Runtime**: Chọn "Python 3.9"
3. **Architecture**: Để mặc định là "x86_64" 
4. **Permissions**: "Create a new role with basic Lambda permissions"
5. **Create function**: Nhấp để tiếp tục

![4.2.2](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/4.2.2.png?featherlight=false&width=90pc)

### Bước 3: Cấu hình Cài đặt Hàm

**Tối ưu hóa cho Free Tier**:

1. **Configuration tab**: Nhấp sau khi tạo hàm
2. **General configuration**: Nhấp vào "Edit"
3. **Memory**: Đặt thành 128 MB
4. **Timeout**: Đặt thành 30 giây
5. **Save**: Nhấp để áp dụng thay đổi

![4.2.3](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/4.2.3.png?featherlight=false&width=90pc)

## Bài tập 2: Thêm Mã Xử Lý Stream

### Bước 1: Thay thế Mã Hàm

**Điều hướng đến trình chỉnh sửa mã**:

1. **Code tab**: Nhấp để mở trình chỉnh sửa mã
2. **lambda_function.py**: Thay thế mã hiện có bằng:

```python
import json
import boto3
import logging
from datetime import datetime

# Cấu hình logging
logger = logging.getLogger()
logger.setLevel(logging.INFO)

def lambda_handler(event, context):
    """
    Xử lý các sự kiện Stream DynamoDB
    Tối ưu hóa cho AWS Free Tier
    """
    try:
        processed_records = 0
        
        # Xử lý từng bản ghi trong batch
        for record in event['Records']:
            event_name = record['eventName']
            
            # Xử lý các sự kiện INSERT, MODIFY, REMOVE
            if event_name in ['INSERT', 'MODIFY', 'REMOVE']:
                process_stream_record(record)
                processed_records += 1
        
        # Trả về phản hồi thành công
        return {
            'statusCode': 200,
            'body': json.dumps({
                'message': f'Successfully processed {processed_records} records',
                'timestamp': datetime.utcnow().isoformat(),
                'processed_count': processed_records
            })
        }
        
    except Exception as e:
        logger.error(f"Error processing stream records: {str(e)}")
        # Ném lại lỗi để Lambda retry
        raise e

def process_stream_record(record):
    """
    Xử lý từng bản ghi stream
    Thêm logic nghiệp vụ của bạn tại đây
    """
    event_name = record['eventName']
    
    # Trích xuất thông tin chính
    if 'dynamodb' in record:
        keys = record['dynamodb'].get('Keys', {})
        pk = keys.get('PK', {}).get('S', '')
        sk = keys.get('SK', {}).get('S', '')
        
        logger.info(f"Processing {event_name} for item: {pk}#{sk}")
        
        # Xử lý các loại sự kiện khác nhau
        if event_name == 'INSERT':
            handle_insert_event(record)
        elif event_name == 'MODIFY':
            handle_modify_event(record)
        elif event_name == 'REMOVE':
            handle_remove_event(record)

def handle_insert_event(record):
    """
    Xử lý việc tạo mục mới
    """
    logger.info("Processing INSERT event")
    
    # Lấy dữ liệu mục mới
    new_image = record['dynamodb'].get('NewImage', {})
    
    # Ví dụ: Gửi thông báo cho người dùng mới
    if 'USER#' in str(new_image.get('PK', {})):
        logger.info("New user created - could send welcome email")
    
    # Ví dụ: Cập nhật kho cho sản phẩm mới
    elif 'PRODUCT#' in str(new_image.get('PK', {})):
        logger.info("New product created - could update search index")
    
    # Ví dụ: Xử lý đơn hàng mới
    elif 'ORDER#' in str(new_image.get('SK', {})):
        logger.info("New order created - could trigger fulfillment")

def handle_modify_event(record):
    """
    Xử lý cập nhật mục
    """
    logger.info("Processing MODIFY event")
    
    # Lấy hình ảnh trước và sau
    old_image = record['dynamodb'].get('OldImage', {})
    new_image = record['dynamodb'].get('NewImage', {})
    
    # Ví dụ: Kiểm tra thay đổi trạng thái
    old_status = old_image.get('status', {}).get('S', '')
    new_status = new_image.get('status', {}).get('S', '')
    
    if old_status != new_status:
        logger.info(f"Status changed from {old_status} to {new_status}")
        # Có thể kích hoạt thông báo, cập nhật bộ nhớ đệm, v.v.
    
    # Ví dụ: Phát hiện thay đổi giá
    old_price = old_image.get('price', {}).get('N', '0')
    new_price = new_image.get('price', {}).get('N', '0')
    
    if old_price != new_price:
        logger.info(f"Price changed from {old_price} to {new_price}")
        # Có thể làm mới bộ nhớ đệm, cập nhật gợi ý, v.v.

def handle_remove_event(record):
    """
    Xử lý xóa mục
    """
    logger.info("Processing REMOVE event")
    
    # Lấy dữ liệu mục đã xóa
    old_image = record['dynamodb'].get('OldImage', {})
    
    # Ví dụ: Dọn dẹp dữ liệu liên quan
    if 'USER#' in str(old_image.get('PK', {})):
        logger.info("User deleted - could cleanup user data")
    
    # Ví dụ: Xóa khỏi chỉ mục tìm kiếm
    elif 'PRODUCT#' in str(old_image.get('PK', {})):
        logger.info("Product deleted - could remove from search")
```

2. **Deploy**: Nhấp vào "Deploy" để lưu mã

![4.2.4](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/4.2.4.png?featherlight=false&width=90pc)

### Bước 2: Kiểm tra Cú pháp Hàm

**Xác thực mã**:

1. **Test tab**: Nhấp vào "Test" tab
2. **Create test event**: Nhấp vào "Create new event"
3. **Event template**: Chọn mẫu "DynamoDB Update"
4. **Event name**: `test-stream-event`
5. **Test**: Nhấp vào "Test" để xác thực cú pháp

![4.2.5](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/4.2.5.png?featherlight=false&width=90pc)

## Bài tập 3: Cấu hình Kích hoạt Nguồn Sự kiện

### Bước 1: Thêm Kích hoạt DynamoDB

**Kết nối Lambda với Stream DynamoDB**:

1. **Function overview**: Trong Lambda console
2. **Add trigger**: Nhấp vào nút "Add trigger"
3. **Trigger configuration**: 
   - **Source**: Chọn "DynamoDB"
   - **DynamoDB table**: Chọn `demo-ecommerce-freetier`
   - **Batch size**: Đặt thành 10 (tối ưu Free Tier)
   - **Starting position**: Chọn "Trim horizon"
4. **Add**: Nhấp để tạo kích hoạt

![4.2.6](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/4.2.6.png?featherlight=false&width=90pc)

### Bước 2: Xác minh Cấu hình Nguồn Sự kiện

**Kiểm tra cấu hình kích hoạt**:

1. **Function overview**: Nên hiển thị kích hoạt DynamoDB
2. **Configuration**: Xác minh cài đặt:
   - **Batch size**: 10 bản ghi
   - **Starting position**: Trim horizon
   - **Status**: Enabled
   - **State**: Creating → Enabled

![4.2.7](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/4.2.7.png?featherlight=false&width=90pc)

### Bước 3: Cấu hình Quyền IAM

**Cập nhật vai trò thực thi Lambda**:

1. **Configuration tab**: Nhấp vào "Permissions"
2. **Execution role**: Nhấp vào liên kết tên vai trò
3. **IAM console**: Mở trong tab mới
4. **Attach policies**: Thêm `AWSLambdaDynamoDBExecutionRole`
5. **Save**: Quay lại Lambda console

![4.2.8](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/4.2.8.png?featherlight=false&width=90pc)

## Bài tập 4: Kiểm tra Xử Lý Stream

### Bước 1: Tạo Mục Kiểm tra

**Tạo sự kiện stream**:

1. **DynamoDB console**: Mở trong tab mới
2. **Items tab**: Điều hướng đến bảng của bạn
3. **Create item**: Thêm dữ liệu kiểm tra:

```json
{
  "PK": "LAMBDA#test-processing",
  "SK": "EVENT#001",
  "event_type": "lambda_test",
  "description": "Testing Lambda stream processing",
  "created_at": "2025-08-11T16:30:00Z",
  "test_purpose": "verify_lambda_trigger"
}
```

4. **Create**: Lưu mục

![4.2.9](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/4.2.9.png?featherlight=false&width=90pc)

### Bước 2: Giám sát Thực thi Lambda

**Kiểm tra việc gọi hàm**:

1. **Lambda console**: Quay lại hàm của bạn
2. **Monitor tab**: Nhấp để xem các chỉ số
3. **Invocations**: Nên hiển thị 1 lần gọi mới
4. **Duration**: Thường < 1 giây
5. **Errors**: Nên là 0

![4.2.10](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/4.2.10.png?featherlight=false&width=90pc)

### Bước 3: Kiểm tra Nhật ký Xử lý

**Xem nhật ký chi tiết**:

1. **CloudWatch logs**: Nhấp vào "View CloudWatch logs"
2. **Log stream**: Nhấp vào luồng nhật ký mới nhất
3. **Log entries**: Tìm kiếm:
   - START RequestId: [uuid]
   - Processing INSERT for item: LAMBDA#test-processing#EVENT#001
   - Processing INSERT event
   - Successfully processed 1 records
   - END RequestId: [uuid]

![4.2.11](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/4.2.11.png?featherlight=false&width=90pc)

## Bài tập 5: Cấu hình Nâng cao

### Cấu hình Xử lý Lỗi

**Cấu hình retry và xử lý lỗi**:

1. **Event source mapping**: Chỉnh sửa kích hoạt DynamoDB của bạn
2. **Additional settings**:
   - **Retry attempts**: 3 (mặc định)
   - **Maximum record age**: 3600 giây
   - **Split batch on error**: Bật
   - **Dead letter queue**: Cấu hình SNS/SQS (tùy chọn)

### Tối ưu hóa Hiệu suất

**Cài đặt tối ưu Free Tier**:

- **Parallelization factor**: 1 (tránh đồng thời quá mức)
- **Batch size**: 10 bản ghi (cân bằng độ trễ và chi phí)
- **Reserved concurrency**: 10 (kiểm soát chi phí)
- **Provisioned concurrency**: 0 (không cần cho streams)

### Giám sát và Cảnh báo

**Thiết lập giám sát cơ bản**:

1. **CloudWatch Alarms**: Tạo cho:
   - Lỗi hàm > 0
   - Thời gian thực thi hàm > 20 giây
   - Tuổi iterator > 30 giây
2. **Notifications**: Chủ đề SNS cho cảnh báo
3. **Dashboard**: Thêm các chỉ số vào bảng điều khiển CloudWatch

![4.2.12](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/4/4.2.12.png?featherlight=false&width=90pc)

## Mẫu Kiểm tra Hàm

### Kiểm tra Các Loại Sự kiện Khác nhau

**Kiểm tra toàn diện**:

1. **INSERT**: Tạo mục mới
2. **MODIFY**: Cập nhật mục hiện có
3. **REMOVE**: Xóa mục
4. **Batch**: Nhiều thay đổi nhanh chóng

### Danh sách Kiểm tra Xác thực

**Xác minh cài đặt của bạn**:

- ✅ **Hàm Lambda đã được tạo** với runtime chính xác
- ✅ **Kích hoạt stream đã được cấu hình** với quyền truy cập thích hợp
- ✅ **Mã đã được triển khai** và cú pháp đã được xác thực
- ✅ **Kiểm tra thành công** với dữ liệu mẫu
- ✅ **Nhật ký hiển thị** chi tiết xử lý
- ✅ **Chỉ số cho thấy** thực thi khỏe mạnh

## Khắc phục sự cố Các Vấn đề Thường gặp

### Lambda Không Kích hoạt

**Kiểm tra các mục sau**:

1. **Stream enabled**: DynamoDB stream đang hoạt động
2. **Permissions**: Lambda có quyền đọc stream
3. **Event source mapping**: Kích hoạt đang được bật
4. **Function state**: Lambda đang hoạt động (không bị lỗi)

### Lỗi Xử lý

**Các bước gỡ lỗi**:

1. **CloudWatch logs**: Kiểm tra các thông báo lỗi
2. **Timeout issues**: Tăng thời gian chờ nếu cần
3. **Memory errors**: Giám sát mức sử dụng bộ nhớ
4. **Permissions**: Xác minh tất cả các quyền cần thiết

### Vấn đề Hiệu suất

**Mẹo tối ưu hóa**:

1. **Batch size**: Điều chỉnh dựa trên thời gian xử lý
2. **Memory allocation**: Định kích thước phù hợp cho tải công việc của bạn
3. **Cold starts**: Cân nhắc sử dụng provisioned concurrency nếu cần
4. **Error handling**: Triển khai logic thử lại thích hợp

{{% notice success %}}
**Lambda Ready**: Function của bạn bây giờ processing DynamoDB stream events trong real-time!
{{% /notice %}}

## Next Steps

Với Lambda function của bạn processing stream events, bạn ready để practice với real data changes và explore different event-driven patterns. Next section covers hands-on stream processing exercises.
