---
title : "Lambda function đọc dữ liệu"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 3.2.1 </b> "
---
Chúng ta sẽ tạo một Lambda function đọc toàn bộ dữ liệu trong bảng của DynamoDB:
1. Mở tệp **template.yaml** trong thư mục **fcj-book-shop**
2. Thêm đoạn script sau vào cuối tệp
    ```
    BooksList:
        Type: AWS::Serverless::Function
        Properties:
        CodeUri: fcj-book-shop/books_list
        Handler: books_list.lambda_handler
        Runtime: python3.9
        FunctionName: books_list
        Architectures:
            - x86_64
        Policies:
            - Statement:
                - Sid: ReadDynamoDB
                Effect: Allow
                Action:
                    - dynamodb:Scan
                    - dynamodb:Query
                Resource:
                    - !Sub arn:aws:dynamodb:${AWS::Region}:${AWS::AccountId}:table/Books
    ```
    ![LambdaListFunction](/images/1/31.png?width=90pc)
3. Cấu trúc thư mục như sau:
    ```
    fcj-book-shop
    ├── fcj-book-shop
    │   ├── books_list
    │       ├── books_list.py
    └── template.yaml

    ```
    - Tạo thư mục tên **fcj-book-shop/books_list** trong thư mục **fcj-book-shop**
    - Tạo file **books_list.py** và sao chép đoạn code sau vào nó
    ```
    import json
    import boto3
    from decimal import *
    from boto3.dynamodb.types import TypeDeserializer

    client = boto3.client('dynamodb') 
    serializer = TypeDeserializer()

    class DecimalEncoder(json.JSONEncoder):
        def default(self, obj):
            if isinstance(obj, Decimal):
                return str(obj)
            return json.JSONEncoder.default(self, obj)
                
    def deserialize(data):
        if isinstance(data, list):
            return [deserialize(v) for v in data]

        if isinstance(data, dict):
            try:
                return serializer.deserialize(data)
            except TypeError:
                return {k: deserialize(v) for k, v in data.items()}
        else:
            return data

    def lambda_handler(event, context):
        data_books = client.scan(
            TableName='Books',
            IndexName='name-index'
        )
        format_data_books = deserialize(data_books["Items"])
        for book in format_data_books:
            data_comment = client.query(
                TableName="Books", 
                KeyConditionExpression="id = :id AND rv_id > :rv_id", 
                ExpressionAttributeValues={
                    ":id": {"S": book['id']}, 
                    ":rv_id": {"N": "0"}
                }
            )
            format_data_comment = deserialize(data_comment['Items'])
            print(data_comment['Items'])
            book["comments"] = format_data_comment
                
        print (format_data_books)
        return {
            "statusCode": 200,
            "headers": {
                "Content-Type": "application/json",
                "Access-Control-Allow-Origin": "*",
                "Access-Control-Allow-Methods": "GET,PUT,POST,DELETE, OPTIONS",
                "Access-Control-Allow-Headers": "Access-Control-Allow-Headers, Origin,Accept, X-Requested-With, Content-Type, Access-Control-Request-Method,X-Access-Token,XKey,Authorization"
            },
            "body": json.dumps(format_data_books, cls=DecimalEncoder)
        }
    ```
4. Chạy dòng lệnh dưới đây triển khai SAM
    ```
    sam build
    sam deploy
    ```

5. Mở bảng điều khiển của [AWS Lambda](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions). Ấn vào **books_list** function đã được tạo
![LambdaListFunction](/images/1/32.png?width=90pc)


7. Ấn sang tab **Configuration**
    - Chọn **Permissions** ở menu phía bên trái
    - Ấn vào role mà function đang thực hiện
![LambdaListFunction](/images/1/33.png?width=90pc)

8. Mở rộng policy, thấy các quyền đã được cấp cho function
![LambdaListFunction](/images/1/34.png?width=90pc)




