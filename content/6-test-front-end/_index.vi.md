---
title : "Kiểm tra APIs với front-end"
date :  2025-02-11
weight : 6
chapter : false
pre : " <b> 6. </b> "
---
Sau khi kiểm tra rằng các API hoạt động đúng với Postman, chúng ta sẽ kiểm tra các API được gọi với front-end được xây dựng từ phần 2.

1. Mở **config.js** trong thư mục **fcj-serverless-frontend** đã tải xuống từ phần 2.
    - Thay đổi giá trị của **APP_API_URL** bằng URL của bạn.
      ![CreateRestAPI](/images/temp/1/91.png?width=90pc)
      ![CreateRestAPI](/images/temp/1/92.png?width=90pc)

2. Mở **package.json** trong thư mục **fcj-serverless-frontend** đã tải xuống từ phần 2.
    - Thay đổi giá trị của **"proxy"** thành URL Invoke của API gateway của bạn.
    > Đảm bảo bạn làm theo mẫu URL như dưới đây.

      ![CreateRestAPI](/images/temp/1/103.png?width=90pc)

3. Mở **App.js** trong **fcj-serverless-frontend/src/**, thay đổi giá trị của **isAdmin** thành **true**.
    ![CreateRestAPI](/images/temp/1/93.png?width=90pc)

4. Chạy các lệnh dưới đây trong terminal của bạn.

    ```bash
    yarn build
    aws s3 rm s3://fcj-book-shop-by-myself --recursive
    aws s3 cp build s3://fcj-book-shop-by-myself --recursive
    ```

5. Dán endpoint của S3 static web vào trình duyệt của bạn. Ứng dụng đã hiển thị thông tin sách, nhưng vẫn chưa có hình ảnh vì chúng ta chưa tải lên hình ảnh.
    ![CreateRestAPI](/images/temp/1/94.png?width=90pc)
Vì vậy, API liệt kê đang hoạt động đúng.

6. Kiểm tra API ghi.
    - Nhấp vào tab **Management**.
    - Nhấp vào **Update**.
      ![CreateRestAPI](/images/temp/1/95.png?width=90pc)
    - Chỉnh sửa bất cứ điều gì bạn muốn ngoại trừ **id**.
    - Nhấp vào **Choose image**.
    - Tải lên hình ảnh dưới đây vào bucket:
    {{%attachments title="Image" pattern=".*\.(jpeg)$"/%}}
    - Nhấp vào **Update**.
    - Nhấp vào **OK**.
      ![CreateRestAPI](/images/temp/1/96.png?width=90pc)
    - Hình ảnh và thông tin đã được cập nhật.
      ![CreateRestAPI](/images/temp/1/97.png?width=90pc)
    - Nhấn vào tab **Create new book** để viết dữ liệu mới vào cơ sở dữ liệu.
    - Enter id with `5`
    - Enter name: `Amazon Web Services in Action`
    - Enter the author: `Andreas Wittig`
    - Enter category: `IT`
    - Enter price: `59.99`
    - Enter a description: `Amazon Web Services in Action, Second Edition is a comprehensive introduction to computing, storing, and networking in the AWS cloud. You'll find clear, relevant coverage of all the essential AWS services you to know, emphasizing best practices for security, high availability, and scalability.`

    {{%attachments title="Image" pattern=".*\.(jpg)$"/%}}

    - Nhấn nút **Choose File** để tải lên hình ảnh.
    - Nhấn nút **Create**.
    - Nhấp vào **OK**.
      ![CreateRestAPI](/images/temp/1/98.png?width=90pc)
    - Hiển thị thông tin mới tạo.
      ![CreateRestAPI](/images/temp/1/99.png?width=90pc)

7. Kiểm tra API xóa.
    - Nhấp vào tab **Management**.
    - Nhấp vào **Update**.
      ![CreateRestAPI](/images/temp/1/100.png?width=90pc)
    - Nhấp vào **Delete**.
    - Nhấp vào **OK** để xác nhận xóa.
      ![CreateRestAPI](/images/temp/1/101.png?width=90pc)
    - Xem kết quả sau khi xóa: không có thông tin sách xuất hiện.
      ![CreateRestAPI](/images/temp/1/102.png?width=90pc)
  
Chúng ta đã hoàn thành việc xây dựng một ứng dụng web đơn giản dựa trên SAM theo mô hình serverless.
