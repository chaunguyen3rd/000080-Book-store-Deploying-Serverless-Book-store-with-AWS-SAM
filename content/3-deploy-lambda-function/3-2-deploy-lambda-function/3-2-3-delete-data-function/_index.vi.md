---
title : "Lambda function xoá dữ liệu"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3.2.3 </b> "
---
Chúng ta sẽ tạo một Lambda function xoá toàn bộ item có partition key và sort key được chỉ định trong bảng của DynamoDB. Và xoá cả tệp ảnh trong S3 bucket:
1. Mở tệp **template.yaml** trong thư mục **fcj-book-shop**
2. Thêm đoạn script sau vào cuối tệp để tạo Lambda function xoá dữ liệu của DynamoDB
    ```
      BookDelete:
        Type: AWS::Serverless::Function
        Properties:
          CodeUri: fcj-book-shop/book_delete
          Handler: book_delete.lambda_handler
          Runtime: python3.9
          FunctionName: book_delete
          Architectures:
            - x86_64
          Policies:
            - Statement:
                - Sid: VisualEditor0
                  Effect: Allow
                  Action:
                    - dynamodb:DeleteItem
                    - dynamodb:GetItem
                    - dynamodb:Query
                    - s3:DeleteObject
                  Resource:
                    - !Sub arn:aws:dynamodb:${AWS::Region}:${AWS::AccountId}:table/Books
                    - arn:aws:s3:::book-image-resize-shop/*
    ```
    - Thêm đoạn script sau vào cuối tệp để tạo S3 bucket lưu tập ảnh sau khi thay đổi kích thước với thiết lập CORS rule và policy
    ```
      BookImageResizeShop:
        Type: AWS::S3::Bucket
        Properties:
          AccessControl: PublicRead
          BucketName: book-image-resize-shop
          CorsConfiguration:
            CorsRules:
            - AllowedHeaders:
                - '*'
              AllowedMethods:
                - GET
                - PUT
                - POST
                - DELETE
              AllowedOrigins:
                - '*'

      BookImageResizeShopPolicy:
        Type: AWS::S3::BucketPolicy
        Properties:
          Bucket: !Ref BookImageResizeShop
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
                    - !Ref BookImageResizeShop
                    - /*
    ```

{{% notice warning %}}
Nếu bạn tạo tên của S3 bucket khác với tên trong bài lab, vui lòng kiểm tra **Policies | Resources** của các dịch vụ và cập nhật.
{{% /notice %}}

![LambdaDeleteFunction](/images/1/40.png?width=90pc)
![LambdaDeleteFunction](/images/1/41.png?width=90pc)

3. Cấu trúc thư mục như sau:
    ```
    fcj-book-shop
    ├── fcj-book-shop
    │   ├── books_list
    │   │   └── books_list.py
    │   ├── book_create
    │   │   └── book_create.py
    │   └── book_delete
    │       └── book_delete.py
    └── template.yaml

    ```
    - Tạo thư mục tên **book_delete** trong thư mục **fcj-book-shop/fcj-book-shop/**
    - Tạo file **book_delete.py** và sao chép đoạn code sau vào nó
    ```
    import boto3
    import json

    s3 = boto3.client('s3')
    client = boto3.resource('dynamodb')

    def get_image_name(image_path):
        str_image = image_path.split("/")
        for image_path_item in str_image:
            image_name = image_path_item
            
        return image_name;
        
    def lambda_handler(event, context):
        error = None
        status = 200
        delete_id = event['pathParameters']
        delete_id['rv_id'] = 0
        
        table = client.Table("Books")
        image_path = ""

        try:
            data = table.get_item(Key = delete_id)
            image_path = data['Item']['image']
            image_name = get_image_name(image_path)
        except Exception as e:
            error = e
        
        try:
            response = table.query(
                ProjectionExpression="rv_id", 
                KeyConditionExpression="id = :id", 
                ExpressionAttributeValues={":id": delete_id['id']})
                
            for item in response['Items']:
                delete_id['rv_id'] = item['rv_id']
                print(delete_id)
                table.delete_item(Key = delete_id)
            
            print(image_name)
            s3.delete_object(Bucket='book-image-resize-shop', Key=image_name)
            
        except Exception as e:
            error = e
        
        if error is None:
            message = 'successfully deleted item!'
        else:
            message = 'delete item fail'
            status = 400

        return {
                'statusCode': status,
                'body': message,
                'headers': {
                    'Content-Type': 'application/json',
                    'Access-Control-Allow-Origin': '*'
                },
            }
    ```

4. Chạy dòng lệnh dưới đây triển khai SAM
    ```
    sam build
    sam deploy
    ```

5. Trở lại với bảng điều khiển của Lambda
    - Ấn vào function **book_delete** đã được tạo
![LambdaDeleteFunction](/images/1/42.png?width=90pc)
6. Ấn sang tab **Configuration**
    - Chọn **Permissions** ở menu phía bên trái
    - Ấn vào role mà function đang thực hiện
![LambdaDeleteFunction](/images/1/43.png?width=90pc)

7. Mở rộng policy, thấy các quyền đã được cấp cho function
![LambdaDeleteFunction](/images/1/44.png?width=90pc)

8. Mở bảng điều khiển của [Amazon S3](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1&region=ap-southeast-1), bucket **book-image-resize-shop** đã được tạo
    - Ấn chọn bucket đó
![LambdaDeleteFunction](/images/1/45.png?width=90pc)

9. Ấn sang tab **Permissions**, thấy policy đã thêm vào bucket
![LambdaDeleteFunction](/images/1/46.png?width=90pc)

10. Kéo xuống dưới cuối trang, thấy **Cross-origin resource sharing** đã được thiết lập
![LambdaDeleteFunction](/images/1/47.png?width=90pc)
