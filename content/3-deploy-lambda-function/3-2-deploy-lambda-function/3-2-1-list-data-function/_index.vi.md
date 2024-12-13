---
title : "Lambda function đọc dữ liệu"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 3.2.1 </b> "
---
Chúng ta sẽ tạo một hàm Lambda để đọc tất cả dữ liệu trong bảng DynamoDB.

1. Mở tệp **template.yaml** trong thư mục **fcj-book-shop**.

2. Thêm đoạn mã sau vào cuối tệp.
    ```
    BooksList:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: fcj-book-shop/books_list
      Handler: books_list.lambda_handler
      Runtime: python3.11
      FunctionName: books_list
      Environment:
        Variables:
          TABLE_NAME: !Ref BooksTable
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
                - !Sub arn:aws:dynamodb:${AWS::Region}:${AWS::AccountId}:table/${booksTableName}
    ```
    ![LambdaListFunction](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/33.png?width=90pc)

3. Cấu trúc thư mục như sau.
    ```
    fcj-book-shop
    ├── fcj-book-shop
    │   ├── books_list
    │       └── books_list.py
    └── template.yaml

    ```
    - Tạo thư mục **fcj-book-shop/books_list** trong thư mục **fcj-book-shop**.
    - Tạo tệp **books_list.py** và sao chép đoạn mã dưới đây vào tệp đó.
    ```
    import boto3
    import os
    import simplejson as json

    TABLE = os.environ['TABLE_NAME']

    # Get the service resource
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table(TABLE)

    header_res = {
        "Content-Type": "application/json",
        "Access-Control-Allow-Origin": "*",
        "Access-Control-Allow-Methods": "OPTIONS,POST,GET,DELETE",
        "Access-Control-Allow-Headers": "Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token",
    }

    secondary_index = "name-index"


    def lambda_handler(event, context):
        try:
            books_data = table.scan(
                TableName=TABLE,
                IndexName=secondary_index
            )

            books = books_data.get('Items', [])

            for book in books:
                data_comment = table.query(
                    TableName=TABLE,
                    KeyConditionExpression="id = :id AND rv_id > :rv_id",
                    ExpressionAttributeValues={
                        ":id": book['id'],
                        ":rv_id": 0
                    }
                )

                book['comments'] = data_comment['Items']

            return {
                "statusCode": 200,
                "headers": header_res,
                "body": json.dumps(books, use_decimal=True)
            }
        except Exception as e:
            print(f'Error getting items: {e}')
            raise Exception(f'Error getting items: {e}')
    ```

4. Chạy lệnh sau để triển khai SAM.
    ```
    sam build
    sam validate
    sam deploy
    ```
    ![LambdaListFunction](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/34.png?width=90pc)

5. Mở [AWS Lambda console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions).
    - Nhấp vào hàm **books_list** đã được tạo.
    ![LambdaListFunction](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/35.png?width=90pc)
    - Tại trang **books_list**.
      - Nhấp vào tab **Configuration**.
      - Chọn **Permissions** từ menu bên trái.
      - Nhấp vào vai trò mà hàm đang thực thi.     
      ![LambdaListFunction](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/36.png?width=90pc)
    - Tại trang **fcj-book-shop-BooksListRole-...**.
      - Kiểm tra các quyền được cấp cho hàm.     
      ![LambdaListFunction](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/37.png?width=90pc)
