---
title : "Lambda function chỉnh ảnh"
date :  2025-02-11
weight : 4
chapter : false
pre : " <b> 3.2.4 </b> "
---
Trong bước này, chúng ta sẽ tạo một hàm Lambda mới để chỉnh kích thước ảnh sau khi người dùng tải lên.

1. Mở file **template.yaml** trong thư mục **fcj-book-shop**.

2. Thêm đoạn mã sau vào cuối file để tạo hàm chỉnh kích thước ảnh.
    - Đầu tiên, chúng ta sẽ tạo các tham số **width** và **height**.

      ```yml
      width:
        Type: String
        Default: 200px

      height:
        Type: String
        Default: 280px
      ```

      ![LambdaResizeFunction](/images/temp/1/53.png?width=90pc)
    - Sau đó, chúng ta thêm các đoạn mã sau để tạo hàm **ImageResize**.

      ```yml
      ImageResize:
        Type: AWS::Serverless::Function
        Properties:
          CodeUri: fcj-book-shop/resize_image/function.zip
          PackageType: Zip
          Handler: index.handler
          Runtime: nodejs20.x
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
                    - !Sub "arn:aws:s3:::${bookImageShopBucketName}/*"
                    - !Sub "arn:aws:s3:::${bookImageResizeShopBucketName}/*"
          Events:
            ResizeImage:
              Type: S3
              Properties:
                Bucket: !Ref BookImageShop
                Events: s3:ObjectCreated:*
          Environment:
            Variables:
              WIDTH: !Ref width
              HEIGHT: !Ref height
              DES_BUCKET: !Ref BookImageResizeShop
      ```

      ![LambdaResizeFunction](/images/temp/1/54.png?width=90pc)
    - Tiếp theo, thêm đoạn mã sau vào cuối file để cấp quyền cho bucket **book-image-shop-by-myself** sử dụng hàm này.

      ```yml
      ImageResizeInvokePermission:
        Type: "AWS::Lambda::Permission"
        Properties:
          FunctionName: !Ref ImageResize
          Action: "lambda:InvokeFunction"
          Principal: "s3.amazonaws.com"
          SourceAccount: !Sub ${AWS::AccountId}
          SourceArn: !GetAtt BookImageShop.Arn
      ```

      ![LambdaResizeFunction](/images/temp/1/55.png?width=90pc)

    {{% notice warning %}}
    Nếu bạn tạo tên bucket S3 khác với tên trong bài lab, vui lòng kiểm tra **Chính sách | Tài nguyên** hoặc **Môi trường** của các tài nguyên và cập nhật.
    {{% /notice %}}

3. Cấu trúc thư mục như sau.

    ```txt
    fcj-book-shop
    ├── fcj-book-shop
    │   ├── books_list
    │   │   └── books_list.py
    │   ├── book_create
    │       └── requirements.txt
    │   │   └── book_create.py
    │   ├── book_delete
    │   │   └── book_delete.py
    │   ├── resize_image
    │       └── function.zip
    └── template.yaml
    ```

    - Tạo thư mục **resize_image** trong thư mục **fcj-book-shop/fcj-book-shop/**.
    - Tải file dưới đây và sao chép vào thư mục này.

    {{%attachments title="Source code" pattern=".*\.(zip)$"/%}}

4. Chạy lệnh sau để triển khai SAM.

    ```bash
    sam build
    sam validate
    sam deploy
    ```

    ![LambdaResizeFunction](/images/temp/1/56.png?width=90pc)

5. Mở [AWS Lambda console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions).
    - Nhấp vào hàm **resize_image** đã tạo.
      ![LambdaResizeFunction](/images/temp/1/57.png?width=90pc)
    - Tại trang **resize_image**.
      - Nhấp vào tab **Configuration**.
      - Chọn **Triggers** trong menu bên trái.
      - Nhấp vào **Details**.
      - Kiểm tra chi tiết **s3:ObjectCreated** mà chúng ta đã tạo trước đó.
        ![LambdaResizeFunction](/images/temp/1/60.png?width=90pc)
      - Nhấp vào tab **Configuration**.
      - Chọn **Permissions** trong menu bên trái.
      - Nhấp vào vai trò mà hàm đang thực thi.
        ![LambdaResizeFunction](/images/temp/1/58.png?width=90pc)
    - Tại trang **fcj-book-shop-ImageResizeRole-...**.
      - Kiểm tra các quyền đã được cấp cho hàm.
        ![LambdaResizeFunction](/images/temp/1/59.png?width=90pc)
