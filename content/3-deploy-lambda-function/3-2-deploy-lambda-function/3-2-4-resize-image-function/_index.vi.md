---
title : "Lambda function chỉnh ảnh"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 3.2.4 </b> "
---
Trong bước này chúng ta tạo một Lambda function mới chỉnh kích thước ảnh sau khi người dùng tải lên.

1. Mở tệp **template.yaml** trong thư mục **fcj-book-shop**
2. Thêm đoạn script sau vào cuối tệp để tạo một function chỉnh ảnh
    ```
    ImageResizer:
        Type: AWS::Serverless::Function
        Properties:
          CodeUri: fcj-book-shop/resize_image/function.zip
          PackageType: Zip
          Handler: index.handler
          Runtime: nodejs16.x
          FunctionName: resize_image
          Architectures:
            - x86_64
          Policies:
            - Statement:
                - Sid: ResizeUploadImage
                  Effect: Allow
                  Action:
                    - s3:GetObject
                    - s3:PutObject
                    - s3:DeleteObject
                  Resource:
                    - arn:aws:s3:::book-image-shop/*
                    - arn:aws:s3:::book-image-resize-shop/*
          Events:
            ResizeImage:
              Type: S3
              Properties:
                Bucket: !Ref BookImageShop
                Events: s3:ObjectCreated:*
          Environment:
            Variables:
              WIDTH: 200
              HEIGHT: 280
              DES_BUCKET: book-image-resize-shop
    ```

{{% notice warning %}}
Nếu bạn tạo tên của S3 bucket khác với tên trong bài lab, vui lòng kiểm tra **Chính sách | Tài nguyên** hoặc **Môi trường** của tài nguyên và cập nhật.
{{% /notice %}}

  - Thêm đoạn script sau vào cuối tệp để cấp quyền cho S3 bucket **books-image-shop** sử dụng function này
    ```
    LambdaInvokePermission:
        Type: "AWS::Lambda::Permission"
        Properties:
          FunctionName: !GetAtt ImageResizer.Arn
          Action: "lambda:InvokeFunction"
          Principal: "s3.amazonaws.com"
          SourceAccount: !Sub ${AWS::AccountId}
          SourceArn: !GetAtt BookImageShop.Arn
    ```
     ![LambdaDeleteFunction](/images/1/48.png?width=90pc)     
3. Cấu trúc thư mục như sau:
    ```
    fcj-book-shop
    ├── fcj-book-shop
    │   ├── books_list
    │   │   └── books_list.py
    │   ├── book_create
    │   │   └── book_create.py
    │   ├── book_delete
    │   │   └── book_delete.py
    │   ├── resize_image
    │       └── function.zip
    └── template.yaml

    ```
    - Tạo thư mục tên **resize_image** trong thư mục **fcj-book-shop/fcj-book-shop/**
    - Tải tệp dưới đây và sao chép vào thư mục trên

    {{%attachments title="Source code" pattern=".*\.(zip)$"/%}}

4. Chạy dòng lệnh dưới đây triển khai SAM
    ```
    sam build
    sam deploy
    ```
    ![LambdaDeleteFunction](/images/1/49.png?width=90pc)

5. Trở lại với bảng điều khiển của Lambda
    - Ấn vào function **resize_image** đã được tạo
 ![LambdaDeleteFunction](/images/1/50.png?width=90pc) 
6. Ấn sang tab **Configuration**
    - Chọn mục **Trigger** ở bên menu bên trái, xuất hiện S3 bucket **book-image-shop**

7. Chọn mục **Permissions** ở menu bên trái
    - Ấn vào role mà function đang thực hiện.
![LambdaDeleteFunction](/images/1/51.png?width=90pc) 
8. Mở rộng policy, thấy các quyền đã được cấp cho function
![LambdaDeleteFunction](/images/1/52.png?width=90pc) 



