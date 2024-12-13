---
title : "Lambda function ghi dữ liệu"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.2.2 </b> "
---
Trong bước này, chúng ta sẽ tạo một hàm Lambda mới để ghi dữ liệu vào DynamoDB trên SAM.

1. Mở tệp **template.yaml** trong thư mục **fcj-book-shop**.

2. Thêm các đoạn mã sau vào cuối tệp để tạo một hàm Lambda ghi dữ liệu vào DynamoDB.
    - Đầu tiên, chúng ta sẽ tạo các tham số **bookImageShopBucketName** và **bookImageResizeShopBucketName**.
    ```
    bookImageShopBucketName:
      Type: String
      Default: book-image-shop-by-myself

    bookImageResizeShopBucketName:
      Type: String
      Default: book-image-resize-shop-by-myself
    ```
    ![LambdaCreateFunction](/images/temp/1/38.png?width=90pc)
    - Tiếp theo, chúng ta sẽ tạo **BookImageShop** và **BookImageResizeShop** để lưu hình ảnh sau khi thay đổi kích thước bằng quy tắc CORS và cài đặt chính sách.
    ```
    BookImageShop:
      Type: AWS::S3::Bucket
      Properties:
        BucketName: !Ref bookImageShopBucketName
        PublicAccessBlockConfiguration:
          BlockPublicAcls: false
          BlockPublicPolicy: false
          IgnorePublicAcls: false
          RestrictPublicBuckets: false

    BookImageResizeShop:
      Type: AWS::S3::Bucket
      Properties:
        BucketName: !Ref bookImageResizeShopBucketName
        PublicAccessBlockConfiguration:
          BlockPublicAcls: false
          BlockPublicPolicy: false
          IgnorePublicAcls: false
          RestrictPublicBuckets: false
        CorsConfiguration:
          CorsRules:
            - AllowedHeaders:
                - "*"
              AllowedMethods:
                - GET
                - PUT
                - POST
                - DELETE
              AllowedOrigins:
                - "*"

    BookImageResizeShopPolicy:
      Type: AWS::S3::BucketPolicy
      Properties:
        Bucket: !Ref BookImageResizeShop
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
                  - !Ref BookImageResizeShop
                  - /*
    ```
    ![LambdaCreateFunction](/images/temp/1/39.png?width=90pc)
    - Sau đó, chúng ta thêm các tập lệnh sau để tạo hàm **BookCreate**.
    ```
    BookCreate:
      Type: AWS::Serverless::Function
      Properties:
        CodeUri: fcj-book-shop/book_create
        Handler: book_create.lambda_handler
        Runtime: python3.11
        Environment:
          Variables:
            BUCKET_NAME: !Ref BookImageShop
            BUCKET_RESIZE_NAME: !Ref BookImageResizeShop
            TABLE_NAME: !Ref BooksTable
        FunctionName: book_create
        Architectures:
          - x86_64
        Policies:
          - Statement:
              - Sid: BookCreateItem
                Effect: Allow
                Action:
                  - dynamodb:PutItem
                  - s3:PutObject
                Resource:
                  - !Sub arn:aws:dynamodb:${AWS::Region}:${AWS::AccountId}:table/${booksTableName}
                  - !Join
                    - ""
                    - - "arn:aws:s3:::"
                      - !Ref BookImageShop
                      - /*
    ```
    ![LambdaCreateFunction](/images/temp/1/40.png?width=90pc)
    
  {{% notice warning %}}
  Nếu tên S3 bucket bạn tạo khác với tên trong bài lab, vui lòng cập nhật **Chính sách | Tài nguyên** của hàm **book_create** bằng tên đó.
  {{% /notice %}}

3. Cấu trúc thư mục như sau.
    ```
    fcj-book-shop
    ├── fcj-book-shop
    │   ├── books_list
    │   │   └── books_list.py
    │   ├── book_create
    │       └── book_create.py
    │       └── requirements.txt
    └── template.yaml
    ```
    - Tạo thư mục **book_create** trong thư mục **fcj-book-shop/fcj-book-shop/**.
    - Tạo tệp **book_create.py** và sao chép đoạn mã dưới đây vào đó.
    ```
    import base64
    from multipart import MultipartParser
    from io import BytesIO
    import boto3
    import os

    BUCKET = os.environ['BUCKET_NAME']
    BUCKET_RESIZE = os.environ['BUCKET_RESIZE_NAME']
    TABLE = os.environ['TABLE_NAME']

    s3_client = boto3.client('s3')
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table(TABLE)

    header_res = {
        "Content-Type": "application/json",
        "Access-Control-Allow-Origin": "*",
        "Access-Control-Allow-Methods": "OPTIONS,POST,GET,DELETE",
        "Access-Control-Allow-Headers": "Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token",
    }


    def parse_multipart(body, boundary):
        parser = MultipartParser(stream=body, boundary=boundary)

        fields = {}
        files = {}

        for part in parser:
            if part.filename:
                files[part.name] = {
                    'file-name': part.filename,
                    'content': part.raw
                }

            else:
                fields[part.name] = part.value

        return fields, files


    def upload_to_s3(key, bucket, body):
        try:
            s3_client.put_object(Key=key, Bucket=bucket, Body=body)
        except Exception as e:
            print(f"Error uploading image to S3: {e}")
            raise Exception(f'Error uploading image to S3: {e}')


    def create_new_item(table, item):
        try:
            table.put_item(Item=item)
        except Exception as e:
            print(f"Error creating new item in DynamoDB table: {e}")
            raise Exception(f'Error creating new item in DynamoDB table: {e}')


    def lambda_handler(event, context):
        content_type = event['headers'].get(
            'Content-Type', '') or event['headers'].get('content-type', '')

        try:
            body = event['body']

            if event['isBase64Encoded']:
                body = BytesIO(base64.b64decode(body))

            boundary = content_type.split("boundary=")[1]

            fields, files = parse_multipart(body, boundary)

            # Upload image to s3
            file_name = files['image']['file-name']
            file_content = files['image']['content']

            upload_to_s3(file_name, BUCKET, file_content)
            s3_url = f'https://{BUCKET_RESIZE}.s3.amazonaws.com/{file_name}'

            # Create new item in DB
            item = {
                'id': fields.get('id', 0),
                'rv_id': 0,
                'name': fields.get('name', ''),
                'author': fields.get('author', ''),
                'price': fields.get('price', ''),
                'category': fields.get('category', ''),
                'description': fields.get('description', ''),
                'image': s3_url
            }

            create_new_item(table, item)

            return {
                "statusCode": 200,
                "headers": header_res,
                "body": "Successfully created item!",
            }

        except Exception as e:
            print(f"Error processing form data: {e}")
            raise Exception(f'Error processing form data: {e}')
    ```
    - Trong thư mục **fcj-book-shop/fcj-book-shop/book_create**, tạo tệp **requirements.txt**.
    - Sao chép đoạn mã dưới đây vào đó.
    ```
    multipart
    ```
4. Chạy lệnh sau để triển khai SAM.
    ```
    sam build
    sam validate
    sam deploy
    ```
    ![LambdaCreateFunction](/images/temp/1/41.png?width=90pc)

5. Mở [AWS Lambda console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions).
    - Nhấp vào hàm **book_create** đã được tạo.
    ![LambdaCreateFunction](/images/temp/1/42.png?width=90pc)
    - Tại trang **book_create**.
      - Nhấp vào tab **Configuration**.
      - Chọn **Permissions** trên menu bên trái.
      - Nhấp vào vai trò mà hàm đang thực thi.     
      ![LambdaCreateFunction](/images/temp/1/43.png?width=90pc)
    - Tại trang **fcj-book-shop-BookCreateRole-...**.
      - Kiểm tra các quyền đã được cấp cho hàm.     
      ![LambdaCreateFunction](/images/temp/1/44.png?width=90pc)

6. Mở [Amazon S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1&region=ap-southeast-1).
    - Các bucket **book-image-resize-shop-by-myself** và **book-image-shop-by-myself** đã được tạo.
    ![LambdaCreateFunction](/images/temp/1/45.png?width=90pc)
    - Nhấp vào bucket **book-image-resize-shop-by-myself**. Tại trang **book-image-resize-shop-by-myself**.
      - Nhấp vào tab **Permissions**.
      - Kiểm tra thông tin **Bucket policy**.     
      ![LambdaCreateFunction](/images/temp/1/46.png?width=90pc)
      - Cuộn xuống cuối trang và kiểm tra **Chia sẻ tài nguyên giữa các nguồn (CORS)**.     
      ![LambdaCreateFunction](/images/temp/1/47.png?width=90pc)
