---
title : "Lambda function xoá dữ liệu"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3.2.3 </b> "
---
Chúng ta sẽ tạo một hàm Lambda để xoá tất cả các mục với khoá phân vùng và khoá sắp xếp được chỉ định trong bảng DynamoDB. Và xoá tệp hình ảnh trong bucket S3.

1. Mở tệp **template.yaml** trong thư mục **fcj-book-shop**.

2. Thêm đoạn mã sau vào cuối tệp để tạo một hàm Lambda xoá dữ liệu của bảng DynamoDB.
    ```
    BookDelete:
      Type: AWS::Serverless::Function
      Properties:
        CodeUri: fcj-book-shop/book_delete
        Handler: book_delete.lambda_handler
        Runtime: python3.11
        FunctionName: book_delete
        Environment:
          Variables:
            BUCKET_NAME: !Ref BookImageResizeShop
            TABLE_NAME: !Ref BooksTable
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
                  - !Sub arn:aws:dynamodb:${AWS::Region}:${AWS::AccountId}:table/${booksTableName}
                  - !Join
                    - ""
                    - - "arn:aws:s3:::"
                      - !Ref BookImageResizeShop
                      - /*
    ```
    ![LambdaDeleteFunction](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/48.png?width=90pc)

3. Cấu trúc thư mục như sau.
    ```
    fcj-book-shop
    ├── fcj-book-shop
    │   ├── books_list
    │   │   └── books_list.py
    │   ├── book_create
    │   │   └── book_create.py
    |       └── requirements.txt
    │   └── book_delete
    │       └── book_delete.py
    └── template.yaml
    ```
    - Tạo thư mục **book_delete** trong thư mục **fcj-book-shop/fcj-book-shop/**.
    - Tạo tệp **book_delete.py** và sao chép đoạn mã dưới đây vào đó.
    ```
    import boto3
    import os

    BUCKET = os.environ['BUCKET_NAME']
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


    def lambda_handler(event, context):
        delete_id = event.get('pathParameters', {})
        delete_id['rv_id'] = 0

        try:
            # Get item from id
            try:
                delete_item = table.get_item(Key=delete_id)
                image_path = delete_item['Item'].get('image', '')
                image_name = image_path.split('/')[-1]

            except Exception as e:
                print(f"Error getting item with that {delete_id['id']}")
                raise Exception(f"Error getting item with that {delete_id['id']}")

            # Delete item in DynamoDB and s3 bucket
            try:
                items_with_same_id = table.query(
                    TableName=TABLE,
                    ProjectionExpression='rv_id',
                    KeyConditionExpression='id = :id',
                    ExpressionAttributeValues={':id': delete_id['id']}
                )

                for item in items_with_same_id['Items']:
                    delete_id['rv_id'] = item['rv_id']
                    table.delete_item(Key=delete_id)

                s3_client.delete_object(Bucket=BUCKET, Key=image_name)

            except Exception as e:
                print(f"Error getting item with that {delete_id['id']}")
                raise Exception(f"Error getting item with that {delete_id['id']}")

            return {
                'statusCode': 200,
                'body': 'Successfully delete item!',
                'headers': header_res
            }

        except Exception as e:
            print(f'Error deleting item: {e}')
            raise Exception(f'Error deleting item: {e}')
    ```

4. Chạy lệnh sau để triển khai SAM.
    ```
    sam build
    sam validate
    sam deploy
    ```
    ![LambdaDeleteFunction](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/49.png?width=90pc)

5. Mở [AWS Lambda console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions).
    - Nhấp vào hàm **book_delete** đã được tạo.
    ![LambdaDeleteFunction](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/50.png?width=90pc)
    - Tại trang **book_delete**.
      - Nhấp vào tab **Configuration**.
      - Chọn **Permissions** ở menu bên trái.
      - Nhấp vào vai trò mà hàm đang thực thi.     
      ![LambdaDeleteFunction](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/51.png?width=90pc)
    - Tại trang **fcj-book-shop-BookDeleteRole-...**.
      - Kiểm tra các quyền được cấp cho hàm.
      ![LambdaDeleteFunction](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/52.png?width=90pc)
