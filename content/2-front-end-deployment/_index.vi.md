---
title : "Triển khai front-end"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 2. </b> "
---
Bước phần này, chúng ta sẽ tạo một S3 bucket với Static web hosting được kích hoạt và được truy cập công cộng dựa trên SAM:

1. Mở tệp **template.yaml** trong thư mục **fcj-book-shop** mà chúng ta vừa tạo ở phần 1.
    - Xoá phần không cần thiết:
![CreateS3Bucket](/images/1/11.png?width=90pc)

2. Sao chép đoạn script sau vào tệp đó: 
      ```
      FcjBookShop:
          Type: AWS::S3::Bucket
          Properties:
            AccessControl: PublicRead
            BucketName: fcj-book-shop
            WebsiteConfiguration:
              IndexDocument: index.html

        FcjBookShopPolicy:
          Type: AWS::S3::BucketPolicy
          Properties:
            Bucket: !Ref FcjBookShop
            PolicyDocument:
              Version: 2012-10-17
              Statement:
                - Action: 
                    - 's3:GetObject'
                  Effect: Allow
                  Principal: '*'
                  Resource: !Join
                    - ''
                    - - 'arn:aws:s3:::'
                      - !Ref FcjBookShop
                      - /*
      ```
    Đoạn script định nghĩa một S3 bucket **fcj-book-shop** với policy là **FcjBookShopPolicy** - cho phép truy cập công cộng
![CreateS3Bucket](/images/1/12.png?width=90pc)

3. Chạy dòng lệnh dưới đây:
    - Để build tại thư mục của SAM project: **fcj-book-shop**
        ```
        sam build
        ```
    - Để kiểm tra tính chính xác của SAM template
        ```
        sam validate
        ```
      ![CreateS3Bucket](/images/1/13.png?width=90pc)

    - Để triển khai SAM
        ```
        sam deploy --guided
        ```
    - Nhập tên cho stack: **fcj-book-shop**
    - Nhập vùng mà bạn muốn triển khai, ví dụ: **us-east-1**
    - Sau đó nhập các thông tin khác như hình dưới đây:
![CreateS3Bucket](/images/1/14.png?width=90pc)
    - Đợi một lúc để tạo CloudFormation stack changeset
    - Nhập "y" khi được hỏi **Deploy this changeset?**
![CreateS3Bucket](/images/1/15.png?width=90pc)

4. Mở bảng điều khiển của [Amazon S3](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1&region=ap-southeast-1)
    - Kiểm tra xem bucket đã được tạo hay chưa.
    - Ấn vào bucket **fcj-book-shop**
![CreateS3Bucket](/images/1/16.png?width=90pc)

6. Ấn sang tab **Properties**. Kéo xuống cuối trang, kiểm tra trạng thái của **Static website hosting**
    - Ghi lại endpoint của website
![CreateS3Bucket](/images/1/17.png?width=90pc)

8. Ấn sang tab **Permissions**
    - Thấy policy đã được thêm
![CreateS3Bucket](/images/1/18.png?width=90pc)

9. Mở bảng điều khiển của [CloudFormation](https://ap-southeast-1.console.aws.amazon.com/cloudformation/home?region=ap-southeast-1#/stacks?filteringStatus=active&filteringText=&viewNested=true&hideStacks=false). Hai stack đã được tạo
    - Ấn vào stack **fcj-book-shop**
![CreateS3Bucket](/images/1/19.png?width=90pc)
10. Ấn sang tab **Resource**, thấy những resource mà CloudFormation đã khởi tạo
![CreateS3Bucket](/images/1/20.png?width=90pc)
    - Ấn sang stack còn lại:
![CreateS3Bucket](/images/1/21.png?width=90pc)

11. Tải code **fcj-serverless-frontend** về máy của bạn
    - Mở terminal trên máy bạn tại thư mục bạn muốn lưu source code
    - Sao chép câu lệnh dưới đây
        ```
        git clone https://github.com/AWS-First-Cloud-Journey/FCJ-Serverless-Workshop.git
        cd FCJ-Serverless-Workshop
        yarn build
        ```
12. Chúng ta đã build xong front-end. Tiếp theo thực hiện câu lệnh sau để tải thư mục **build** lên S3
      ```
      aws s3 cp build s3://fcj-book-shop --recursive
      ```
Kết quả sau khi tải xong:
![CreateS3Bucket](/images/1/22.png?width=90pc)


