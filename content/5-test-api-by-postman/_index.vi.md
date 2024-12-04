---
title : "Kiểm tra API với Postman"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 5. </b> "
---
Trong bước này, chúng ta sẽ kiểm tra hoạt động của các API bằng công cụ [Postman](https://www.postman.com/downloads/)
#### Kiểm tra API đọc
1. Ấn vào dấu **+** để thêm 1 tab mới
    - Chọn **GET** method
    - Nhập InvokURL của GET API đã ghi lại từ bước trước
    - Ấn nút **Send**
    - Kết quả trả về là toàn bộ dữ liệu của bảng **Books** đã qua xử lý
![TestListAPI](/images/1/75.png?&width=90pc)
#### Kiểm tra API ghi
1. Tương tự tạo một tab mới
    - Chọn **POST** method
    - Nhập InvokURL của POST API đã ghi lại từ bước trước
    - Trong mục **Body**, chọn **raw**
    - Sao chép đoạn dưới đây:
    - Đợi một chút, xem kết quả trả về
    ```
    {
        "id": "5",
        "rv_id": 0,
        "name": "Amazon Web Services in Action 2nd Edition",
        "author": "Andreas Wittig",
        "price": "59.99",
        "category": "IT",
        "description": "Amazon Web Services in Action, Second Edition is a comprehensive introduction to computing, storing, and networking in the AWS cloud. You'll find clear, relevant coverage of all the essential AWS services you to know, emphasizing best practices for security, high availability and scalability.",
        "image": "https://book-image-resize-shop.s3.ap-southeast-2.amazonaws.com/aws.jpg"
    }
    ```
    ![TestListAPI](/images/1/76.png?&width=90pc)

4. Mở bảng **Books** trong bảng điều khiển của DynamoDB kiểm tra dữ liệu
    - Trước khi gọi API ghi
![TestListAPI](/images/1/77.png?&width=90pc)
    - Sau khi gọi API ghi
    ![TestListAPI](/images/1/78.png?&width=90pc)
#### Kiểm tra API xoá
Vì Lambda function xoá khi thực hiện sẽ xoá ảnh được tải lên bởi người dùng, nên chúng ta tải thủ công ảnh lên S3 bucket để API có thể chạy đúng.
1. Mở bảng điều khiển của [Amazon S3](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-2&region=ap-southeast-2)
2. Ấn vào bucket **book-image-shop** 
    ![TestListAPI](/images/1/79.png?&width=90pc)

3. Ấn nút **Add file**
    ![TestListAPI](/images/1/80.png?&width=90pc)

4. Tải ảnh sau về máy của bạn và chọn nó để tải lên bucket

{{%attachments title="Image" pattern=".*\.(jpg)$"/%}}
5. Ấn nút **Upload**
        ![TestListAPI](/images/1/81.png?&width=90pc)


6. Sau khi tải xong, chuyển sang bucket **book-image-resize-shop** kiểm tra. Đây là kết quả chạy của reszie_image Lambda funtion
![TestListAPI](/images/1/82.png?&width=90pc)
8. Trở lại với Postman, thêm một tab mới để thực hiện API xoá
    - Chọn **DELETE** method
    - Nhập InvokeURL của DELETE API đã ghi lại từ bước trước, thay **/{id}** bằng **/5**
    - Ấn nút **Send**
    - Kiểm tra kết quả trả về:
    ![TestListAPI](/images/1/83.png?&width=90pc)

10. Mở bảng **Books** trong bảng điều khiển của DynamoDB kiểm tra dữ liệu
    ![TestListAPI](/images/1/84.png?&width=90pc)

11. Mở bucket **book-image-resize-shop** kiểm tra kết quả. Ảnh **aws.jpg** đã xoá.
    ![TestListAPI](/images/1/85.png?&width=90pc)

