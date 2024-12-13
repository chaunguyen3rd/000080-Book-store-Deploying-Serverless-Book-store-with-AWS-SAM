---
title : "Kiểm tra API với Postman"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 5. </b> "
---
Trong bước này, chúng ta sẽ kiểm tra hoạt động của các API bằng công cụ [Postman](https://www.postman.com/downloads/)

#### Kiểm tra API đọc
1. Nhấn vào **+** để thêm 1 tab mới.
    - Chọn giao thức **GET**.
    - Nhập URL của API liệt kê mà lưu lại từ bước trước.
    - Nhấn vào **Send**.
![TestListAPI](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/85.png?width=90pc)
Kết quả trả về là toàn bộ dữ liệu của bảng **Books** đã qua xử lý.

#### Kiểm tra API ghi
1. Thực hiện tương tự để tạo ra 1 tab mới.
    - Chọn giao thức **POST**.
    - Nhập URL của API liệt kê mà lưu lại từ bước trước.
    - Chọn **Body**.
    - Chọn **form-data**.
    - Điền **Key** và **Value** như bên dưới. Thay đổi loại của **image** thành **File** như hình bên dưới. Và giá trị của **image**, chọn đường dẫn của tệp trong máy của bạn.
      - id: 5
      - name: Amazon Web Services in Action 2nd Edition
      - author: Andreas Wittig
      - price: 59.99
      - category: IT
      - description: Amazon Web Services in Action, Second Edition is a comprehensive introduction to computing, storing, and networking in the AWS cloud. You'll find clear, relevant coverage of all the essential AWS services you to know, emphasizing best practices for security, high availability and scalability
      - image: aws-logo.png
    - Nhấn nút **Send**. Đợi một lúc, xem kết quả trả về **Successfully created item**.
![TestListAPI](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/86.png?width=90pc)

2. Mở bảng **Books** ở bảng điều khiển DynamoDB để kiểm tra dữ liệu.
    - Trước khi gọi API ghi.
  ![TestListAPI](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/87.png?width=90pc)
    - Sau khi gọi API ghi.
  ![TestListAPI](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/88.png?width=90pc)

#### Kiểm tra API xoá
1. Thực hiện tương tự để tạo ra 1 tab mới.
    - Chọn giao thức **DELETE**.
    - Nhập URL của API liệt kê mà lưu lại từ bước trước. Thay thế **{id}** thành book_id mà bạn muốn xóa, ví dụ **5**.
    - Nhấn nút **Send**. Đợi một lúc, xem kết quả trả về **Successfully delete item!**.
![TestListAPI](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/89.png?width=90pc)

2. Mở bảng **Books** ở bảng điều khiển DynamoDB để kiểm tra dữ liệu.
![TestListAPI](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/87.png?width=90pc)

3. Mở bucket **book-image-resize-stores-by-myself** để kiểm tra đối tượng. **aws-logo.jpg** đã bị xóa.
![TestListAPI](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/90.png?width=90pc)