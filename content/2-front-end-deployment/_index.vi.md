---
title : "Deploy front-end"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 2. </b> "
---
Trong bước này, chúng ta sẽ tạo một S3 bucket với tính năng lưu trữ web tĩnh được bật và có thể truy cập công khai dựa trên SAM.

1. Mở tệp **template.yaml** trong thư mục **fcj-book-shop** mà chúng ta đã tạo ở phần 1.
    - Xóa phần không cần thiết.
![CreateS3Bucket](/images/temp/1/12.png?width=90pc)

2. Sao chép các đoạn mã sau vào tệp đó.
    ```
    Parameters:
      fcjBookShopBucketName:
        Type: String
        Default: fcj-book-shop-by-myself    
    ```
    ![CreateS3Bucket](/images/temp/1/13.png?width=90pc)

    ```
    FcjBookShop:
      Type: AWS::S3::Bucket
      Properties:
        BucketName: !Ref fcjBookShopBucketName
        WebsiteConfiguration:
          IndexDocument: index.html
        PublicAccessBlockConfiguration:
          BlockPublicAcls: false
          BlockPublicPolicy: false
          IgnorePublicAcls: false
          RestrictPublicBuckets: false

    FcjBookShopPolicy:
      Type: AWS::S3::BucketPolicy
      Properties:
        Bucket: !Ref FcjBookShop
        PolicyDocument:
          Version: 2012-10-17
          Statement:
            - Action:
                - "s3:GetObject"
              Effect: Allow
              Principal: "*"
              Resource: !Join
                - ""
                - - "arn:aws:s3:::"
                  - !Ref FcjBookShop
                  - /*
    ```

    Tập lệnh trên định nghĩa một S3 bucket là **fcj-book-shop** với chính sách **FcjBookShopPolicy** - cho phép truy cập công khai.
    ![CreateS3Bucket](/images/temp/1/14.png?width=90pc)

3. Chạy lệnh dưới đây.
    - Để build tại thư mục của dự án SAM: **fcj-book-shop**.
      ```
      sam build
      ```
    - Để kiểm tra tính hợp lệ của template SAM.
      ```
      sam validate
      ```
      ![CreateS3Bucket](/images/temp/1/15.png?width=90pc)

    - Để triển khai SAM.
      ```
      sam deploy --guided
      ```
      - Nhập tên stack: `fcj-book-shop`
      - Nhập khu vực triển khai, ví dụ: `us-east-1` - nên giống với khu vực mặc định.
      - Sau đó nhập các thông tin khác như hình dưới đây.
      ![CreateS3Bucket](/images/temp/1/16.png?width=90pc)
      - Chờ một lúc để tạo thay đổi stack CloudFormation.
      - Nhập "y" khi **Deploy this changeset?**.
      ![CreateS3Bucket](/images/temp/1/17.png?width=90pc)

4. Mở [Amazon S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1&region=ap-southeast-1).
    - Kiểm tra xem bucket đã được tạo hay chưa, nhấp vào bucket **fcj-book-shop-by-myself**.
    ![CreateS3Bucket](/images/temp/1/18.png?width=90pc)

5. Tại trang **fcj-book-shop-by-myself**.
    - Nhấp vào tab **Properties**.
    ![CreateS3Bucket](/images/temp/1/19.png?width=90pc)
    - Sau đó cuộn xuống, kiểm tra trạng thái của **Static website hosting**.
    - Ghi lại endpoint của trang web.
    ![CreateS3Bucket](/images/temp/1/20.png?width=90pc)
    - Nhấp vào tab **Permissions**.
    - Kiểm tra chính sách đã được thêm vào.
    ![CreateS3Bucket](/images/temp/1/21.png?width=90pc)

6. Mở [CloudFormation console](https://ap-southeast-1.console.aws.amazon.com/cloudformation/home?region=ap-southeast-1#/stacks?filteringStatus=active&filteringText=&viewNested=true&hideStacks=false). Hai stack đã được tạo.
    - Nhấp vào stack **fcj-book-shop**.
    ![CreateS3Bucket](/images/temp/1/22.png?width=90pc)

7. Tại trang **fcj-book-shop**.
    - Nhấp vào tab **Resource**, xem các tài nguyên mà CloudFormation đã khởi tạo.
    ![CreateS3Bucket](/images/temp/1/23.png?width=90pc)
    - Nhấp vào stack khác và xem các tài nguyên khác.
    ![CreateS3Bucket](/images/temp/1/24.png?width=90pc)

8. Tải mã nguồn **fcj-serverless-frontend** về thiết bị của bạn
    - Mở terminal trên máy tính tại thư mục nơi bạn muốn lưu mã nguồn.
    - Sao chép và chạy lệnh dưới đây.
      ```
      git clone https://github.com/AWS-First-Cloud-Journey/FCJ-Serverless-Workshop.git
      cd FCJ-Serverless-Workshop
      yarn
      yarn build
      ```
9. Chúng ta đã hoàn thành việc xây dựng front-end. Tiếp theo, thực hiện lệnh sau để tải thư mục **build** lên S3.
    ```
    aws s3 cp build s3://fcj-book-shop-by-myself --recursive
    ```
    Kết quả sau khi tải lên:
    ![CreateS3Bucket](/images/temp/1/25.png?width=90pc)
