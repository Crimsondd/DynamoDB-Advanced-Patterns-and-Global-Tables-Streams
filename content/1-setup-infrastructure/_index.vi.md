---
title : "1. Thiết lập & Triển khai Hạ tầng"
date : "2025-08-11"
weight : 1
chapter : true
pre : " <b> 1. </b> "
---

# Thiết lập & Triển khai Hạ tầng

🚀 **Hướng dẫn hoàn chỉnh thiết lập hạ tầng AWS cho workshop DynamoDB Advanced Patterns**

Module này cung cấp thiết lập nền tảng cần thiết cho workshop DynamoDB Advanced Patterns, đảm bảo tất cả học viên có môi trường hoạt động sử dụng AWS Free Tier.

## 📋 Mục tiêu Học tập

#### Sau khi hoàn thành module này, bạn sẽ:

- ✅ Xác minh tài khoản AWS và tính đủ điều kiện Free Tier
- ✅ Điều hướng AWS Console hiệu quả
- ✅ Triển khai hạ tầng qua CloudFormation
- ✅ Xác minh tất cả tài nguyên được tạo thành công
- ✅ Thiết lập giám sát và cảnh báo billing

## 🏗️ Tổng quan Kiến trúc

#### Chúng ta sẽ triển khai hạ tầng trên hai vùng AWS:

![global-tables-architecture](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/1/global-tables-architecture-improved.png?featherlight=false&width=90pc)

## 🎯 Những gì chúng ta sẽ xây dựng

#### Chúng ta đang xây dựng một nền tảng thương mại điện tử đơn giản với:

- **Users**: Hồ sơ khách hàng và xác thực
- **Products**: Danh mục với các danh mục và giá cả
- **Orders**: Giỏ hàng và quản lý đơn hàng
- **Real-time processing**: Cập nhật dựa trên stream
- **Global availability**: Triển khai đa vùng

## 📦 Tài nguyên được tạo

#### Template CloudFormation này sẽ tạo:

- **DynamoDB Table**: Global table với streams được kích hoạt
- **Lambda Function**: Stream processor cho cập nhật thời gian thực
- **IAM Roles**: Chính sách truy cập bảo mật
- **CloudWatch Dashboard**: Giám sát và metrics
- **Billing Alerts**: Cơ chế bảo vệ chi phí

## 🚀 Điều kiện tiên quyết

#### Trước khi bắt đầu, đảm bảo bạn có:

- Tài khoản AWS với tính đủ điều kiện Free Tier
- Quyền truy cập quản trị vào AWS Console
- Hiểu biết cơ bản về các dịch vụ AWS
- Trình duyệt web hiện đại (Chrome, Firefox, Safari)

#### Hãy bắt đầu với thiết lập hạ tầng sẽ hỗ trợ triển khai DynamoDB đa vùng của chúng ta.

- Bạn có thể chọn tài khoản **Personal** hoặc **Business**

![Create Account](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/1/0009.png?featherlight=false&width=90pc)

#### Thêm phương thức thanh toán

- Nhập thông tin thẻ tín dụng của bạn và chọn **Verify and Add**.
    - ***Lưu ý**: Bạn có thể chọn địa chỉ khác cho tài khoản của mình bằng cách chọn **Use a new address** trước **Verify and Add**.*

![Create Account](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/1/00010.png?featherlight=false&width=90pc)

#### Xác minh số điện thoại của bạn

1. Nhập số điện thoại.
2. Nhập mã kiểm tra bảo mật sau đó chọn **Call me now**.
3. AWS sẽ liên hệ và xác minh việc mở tài khoản.

![Create Account](/DynamoDB-Advanced-Patterns-and-Global-Tables-Streams/images/1/00011.png?featherlight=false&width=90pc)

#### Chọn Support Plan

- Trong trang **Select a support plan**, chọn một plan hiệu quả, để so sánh các plan, xem [Compare AWS Support Plans](https://aws.amazon.com/premiumsupport/plans/).

#### Đợi tài khoản của bạn được kích hoạt

- Sau khi chọn **Support plan**, tài khoản thường được kích hoạt sau vài phút, nhưng quá trình có thể mất đến 24 giờ. Bạn vẫn có thể đăng nhập vào tài khoản AWS lúc này, trang chủ AWS có thể hiển thị nút "Complete Sign Up" trong thời gian này, ngay cả khi bạn đã hoàn thành tất cả các bước trong phần đăng ký.
- Sau khi nhận được email xác nhận tài khoản của bạn đã được kích hoạt, bạn có thể truy cập tất cả dịch vụ AWS.       
  
#### Quan trọng

- Các hành động AWS Identity and Access Management (IAM) sau đây sẽ đạt đến cuối hỗ trợ tiêu chuẩn vào tháng 7 năm 2023: `aws-portal:ModifyAccount` và `aws-portal:ViewAccount`. Xem [Using fine-grained AWS Billing actions](link_to_documentation) để thay thế các hành động này bằng các hành động chi tiết để bạn có quyền truy cập vào AWS Billing, AWS Cost Management và bảng điều khiển tài khoản AWS.
- Nếu bạn tạo tài khoản AWS hoặc tài khoản AWS Organizations Management trước ngày 6 tháng 3 năm 2023, các hành động chi tiết sẽ có hiệu lực từ tháng 7 năm 2023. Chúng tôi khuyên bạn thêm các hành động chi tiết, nhưng không loại bỏ quyền hiện có với tiền tố `aws-portal` hoặc `purchase-orders`.
- Nếu bạn tạo tài khoản AWS hoặc tài khoản AWS Organizations Management vào hoặc sau ngày 6 tháng 3 năm 2023, các hành động chi tiết có hiệu lực ngay lập tức.
- AWS gán các định danh duy nhất sau cho mỗi tài khoản AWS:
- **AWS account ID**: Một số 12 chữ số, chẳng hạn như `012345678901`, định danh duy nhất cho tài khoản AWS. Nhiều tài nguyên AWS bao gồm ID tài khoản trong Amazon Resource Names (ARNs) của chúng. Phần ID tài khoản phân biệt tài nguyên trong một tài khoản với tài nguyên trong tài khoản khác. Nếu bạn là người dùng AWS Identity and Access Management (IAM), bạn có thể đăng nhập vào AWS Management Console bằng ID tài khoản hoặc bí danh tài khoản. Mặc dù ID tài khoản, như bất kỳ thông tin nhận dạng nào, nên được sử dụng và chia sẻ cẩn thận, chúng không được coi là thông tin bí mật, nhạy cảm hoặc bảo mật.
- **Canonical user ID**: Một định danh chữ và số, chẳng hạn như `79a59df900b949e55d96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2be`, là dạng che giấu của ID tài khoản AWS. Bạn có thể sử dụng ID này để xác định tài khoản AWS khi cấp quyền truy cập chéo tài khoản vào buckets và objects bằng Amazon Simple Storage Service (Amazon S3). Bạn có thể lấy canonical user ID cho tài khoản AWS của mình với tư cách là người dùng root hoặc người dùng IAM.
- Bạn phải được xác thực với AWS để xem các định danh này.

#### Cảnh báo

**Không cung cấp thông tin xác thực AWS của bạn** (bao gồm mật khẩu và access keys) cho bên thứ ba cần định danh tài khoản AWS của bạn để chia sẻ tài nguyên AWS với bạn. Làm như vậy sẽ cấp cho họ quyền truy cập tương tự vào tài khoản AWS như bạn có.

