---
title : "Chuẩn bị"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
Để bắt đầu xây dựng các ứng dụng dựa trên SAM, trước tiên chúng ta phải cài đặt SAM CLI, cài đặt thông tin xác thực AWS và khởi tạo một ứng dụng SAM đơn giản.

1. Cài đặt ``sam`` CLI.
{{% notice note %}}
Bạn có thể tham khảo: https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html
{{% /notice %}}

2. Thiết lập thông tin xác thực AWS.
{{% notice note %}}
Nếu bạn đã cài đặt thông tin xác thực AWS từ các bài viết trước, bạn có thể bỏ qua bước này.
{{% /notice %}}
    - Mở [IAM console](https://us-east-1.console.aws.amazon.com/iamv2/home?region=us-east-1#/home)
    - Nhấp vào **User** trên menu bên trái.
    - Nhấp vào nút **Create user**.
    ![IAMConsole](/images/temp/1/1.png?width=90pc)
    - Tại trang **Step 1: Specify user details**.
      - Nhập tên người dùng, ví dụ: `sam-admin`.
      - Chọn tùy chọn **Provide user access to the AWS Management Console**.
      - Chọn **I want to create an IAM user**.
      - Chọn **Custom password**, sau đó nhập mật khẩu của bạn.
      ![IAMConsole](/images/temp/1/2.png?width=90pc)
      - Bỏ chọn **User must create a new password at next sign-in**.
      - Nhấp vào **Next**.
      ![IAMConsole](/images/temp/1/3.png?width=90pc)
    - Tại trang **Step 2: Set permissions**.
      - Chọn **Attach policies directly**.
      - Chọn chính sách **AdministratorAccess** để người dùng có toàn quyền truy cập vào các dịch vụ.
      - Nhấp vào **Next**.
      ![IAMConsole](/images/temp/1/4.png?width=90pc)
    - Tại trang **Step 3: Review and create**.
      - Xem lại cấu hình và nhấp vào **Create user**.
      ![IAMConsole](/images/temp/1/5.png?width=90pc)
    - Tại trang **Step 4: Review and create**, nhấp vào **Return to users list**.
    - Tại trang **sam-admin**.
      - Nhấp vào **Create access key**.
      ![IAMConsole](/images/temp/1/6.png?width=90pc)
    - Tại **Step 1: Access key best practices & alternatives**.
      - Đối với **Use case**, chọn **Command Line Interface(CLI)**.
      ![IAMConsole](/images/temp/1/7.png?width=90pc)
      - Cuộn xuống dưới cùng, chọn **I understand the above recommendation ...**.
      - Nhấp vào **Next**.
      ![IAMConsole](/images/temp/1/8.png?width=90pc)
    - Tại **Step 2: Set description tag**.
      - Nhấp vào **Create access key**.
      ![IAMConsole](/images/temp/1/9.png?width=90pc)
    - Tại **Step 3: Retrieve access keys**.
      - Nhấp vào **Download .csv file**.
      - Sau đó, nhấp vào **Done**.
      ![IAMConsole](/images/temp/1/10.png?width=90pc)
    - Chạy lệnh sử dụng terminal trên thiết bị của bạn.
      ```
      aws configure
      ```
      - Nhập thông tin tương ứng với các cột trong tệp thông tin xác thực bạn đã tải xuống.
      - **AWS Access key ID**: Nhập access key ID.
      - **AWS Secret access key**: Nhập secret access key.
      - **Default region name**: Nhập khu vực gần bạn nhất.
      - **Default output format**: Có thể bỏ qua.

3. Sau đó, tạo một dự án mẫu ``sam``.
    - Chạy các lệnh dưới đây tại thư mục nơi bạn muốn triển khai ứng dụng.
      ```
      #Step 1 - Download a sample application
      sam init
      ```
    - Sau đó chọn theo các tùy chọn:
      ```
      Which template source would you like to use?
        1 - AWS Quick Start Templates
        2 - Custom Template Location
      Choice: 1

      Choose an AWS Quick Start application template
        1 - Hello World Example
        2 - Multi-step workflow
        3 - Serverless API
        4 - Scheduled task
        5 - Standalone function
        6 - Data processing
        7 - Infrastructure event management
        8 - Machine Learning
      Template: 1

      Use the most popular runtime and package type? (Python and zip) [y/N]: y

      Would you like to enable X-Ray tracing on the function(s) in your application?  [y/N]: N

      Project name [sam-app]:  fcj-book-shop
      ```
      ![IAMConsole](/images/temp/1/11.png?width=90pc)
Bạn đã tạo một dự án SAM mẫu. Tiếp theo, chúng ta sẽ chỉnh sửa dự án đó theo kiến trúc ứng dụng của chúng ta.
