---
title : "Lambda function ghi dữ liệu"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.2.2 </b> "
---
Trong bước này chúng ta tạo một Lambda function mới để ghi dữ liệu vào DynamoDB trên SAM

1. Mở tệp **template.yaml** trong thư mục **fcj-book-shop**
2. Thêm đoạn script sau vào cuối tệp để tạo Lambda function ghi dữ liệu vào DynamoDB
    ```
      BookCreate:
        Type: AWS::Serverless::Function
        Properties:
          CodeUri: fcj-book-shop/book_create
          Handler: book_create.lambda_handler
          Runtime: python3.9
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
                    - !Sub arn:aws:dynamodb:${AWS::Region}:${AWS::AccountId}:table/Books
                    - arn:aws:s3:::book-image-shop/*
    ```
    - Thêm đoạn script sau vào cuối tệp để tạo S3 bucket lưu tập ảnh người dùng tải lên
    ```
      BookImageShop:
        Type: AWS::S3::Bucket
        Properties:
          BucketName: book-image-shop
    ```
{{% notice warning %}}
Nếu bạn tạo các bộ chứa S3 có tên khác với tên trong bài lab, vui lòng cập nhật **Policies | Resource** của function **book_create** với tên đó
{{% /notice %}}

3. Cấu trúc thư mục như sau:
    ```
    fcj-book-shop
    ├── fcj-book-shop
    │   ├── books_list
    │   │   └── books_list.py
    │   ├── book_create
    │       └── book_create.py
    └── template.yaml

    ```
    - Tạo thư mục tên **book_create** trong thư mục **fcj-book-shop/fcj-book-shop/**
    - Tạo file **book_create.py** và sao chép đoạn code sau vào nó
    ```
    import boto3
    import json
    import base64
    import io
    import cgi
    import os

    s3 = boto3.client('s3')
    client = boto3.resource('dynamodb')
    runtime_region = os.environ['AWS_REGION']

    def get_data_from_request_body(content_type, body):
        fp = io.BytesIO(base64.b64decode(body)) # decode
        environ = {"REQUEST_METHOD": "POST"}
        headers = {
            "content-type": content_type,
            "content-length": len(body),
        }

        fs = cgi.FieldStorage(fp=fp, environ=environ, headers=headers) 
        return [fs, None]
        
    def lambda_handler(event, context):
        content_type = event['headers'].get('Content-Type', '') or event['headers'].get('content-type', '')
        
        if content_type == 'application/json':
            book_item = json.loads(event["body"])
        else:
            book_data, book_data_error = get_data_from_request_body(
                content_type=content_type, body=event["body"]
            )
        
            name = book_data['image'].filename
            image = book_data['image'].value
            s3.put_object(Bucket='book-image-shop', Key=name, Body=image)
            image_path = "https://{}.s3.{}.amazonaws.com/{}".format("book-image-resize-shop", runtime_region, name)
            
            book_item = {
                "id": book_data['id'].value,
                "rv_id": 0,
                "name": book_data['name'].value,
                "author": book_data['author'].value,
                "price" : book_data['price'].value,
                "category": book_data['category'].value,
                "description": book_data['description'].value,
                "image": image_path
            }
        
        table = client.Table('Books')
        table.put_item(Item = book_item)
        
        response = {
            'statusCode': 200,
            'body': 'successfully created item!',
            'headers': {
                'Content-Type': 'application/json',
                "Access-Control-Allow-Headers": "Access-Control-Allow-Headers, Origin, Accept, X-Requested-With, Content-Type, Access-Control-Request-Method,X-Access-Token, XKey, Authorization",
                "Access-Control-Allow-Origin": "*",
                "Access-Control-Allow-Methods": "GET,PUT,POST,DELETE,OPTIONS"
            },
        }
      
        return response
    ```

4. Chạy dòng lệnh dưới đây triển khai SAM
    ```
    sam build
    sam deploy
    ```
    ![LambdaCreateFunction](/images/1/35.png?width=90pc)

5. Trở lại với bảng điều khiển của Lambda
    - Ấn vào function **book_create** đã được tạo
![LambdaCreateFunction](/images/1/36.png?width=90pc)
6. Ấn sang tab **Configuration**
    - Chọn mục **Permissions** ở menu bên trái
    - Ấn vào role mà function đang thực hiện.
![LambdaCreateFunction](/images/1/37.png?width=90pc)

7. Mở rộng policy, thấy các quyền đã được cấp cho function
![LambdaCreateFunction](/images/1/38.png?width=90pc)

8. Mở bảng điều khiển của [Amazon S3](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1&region=ap-southeast-1), bucket **book-image-shop** đã được tạo
![LambdaCreateFunction](/images/1/39.png?width=90pc)






