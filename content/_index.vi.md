---
title : "Serverless - Triển khai ứng dụng bằng SAM"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
---
# Serverless - Triển khai ứng dụng trên SAM

### Tổng quan

Trong hai bài đầu tiên của series này, chúng ta đã tìm hiểu cách xây dựng một ứng dụng web đơn giản theo mô hình serverless trên cách sử dụng bảng điều khiển của AWS. Để xây dựng ứng dụng serverless nhanh hơn, AWS cung cấp cho người dùng dịch vụ AWS Serverless Application Model (SAM) - là một open-source framework để xây dựng một ứng dụng serverless. SAM cung cấp cú pháp để diễn đạt các hàm, API, cơ sở dữ liệu và event source mappings. Chỉ với vài dòng cho mỗi tài nguyên, bạn có thể xác định ứng dụng bạn muốn và lập mô hình ứng dụng đó bằng YAML. Trong quá trình triển khai, SAM chuyển đổi và mở rộng cú pháp SAM thành cú pháp của AWS CloudFormation. Sau đó, CloudFormation cung cấp các tài nguyên cho người dùng.  

Trong bài số 3 này, chúng ta sẽ cùng nhau xây dựng lại ứng dụng web ở bài số 2 với AWS SAM.

Kiến trúc của ứng dụng chúng ta sẽ xây dựng:

![WebArchitect](/images/serverless-architect-diagram.png?featherlight=false&width=50pc)

### Nội dung

 1. [Chuẩn bị](1-preparation/)
 2. [Triển khai front-end](2-front-end-deployment/)
 3. [Triển khai Lambda function](3-deploy-lambda-function/)
 4. [Cấu hình API Gateway](4-config-api-gw/)
 5. [Kiểm tra API với Postman](5-test-api-by-postman/)
 6. [Kiểm tra API với front-end](6-test-front-end/)
 7. [Dọn dẹp tài nguyên](7-cleanup)