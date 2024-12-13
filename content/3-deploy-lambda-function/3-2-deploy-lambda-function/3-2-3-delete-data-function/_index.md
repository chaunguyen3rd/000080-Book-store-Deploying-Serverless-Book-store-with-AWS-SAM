---
title : "Deleting Lambda function"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3.2.3 </b> "
---
We will create a Lambda function that deletes all items with the specified partition key and sort key in the DynamoDB table. And delete the image file in the S3 bucket.

1. Open **template.yaml** file in **fcj-book-shop** folder.

2. Add the following script at the end of the file to create a Lambda function that deletes data of DynamoDB table.
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

3. The directory structure is as follows.
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
    - Create **book_delete** folder in **fcj-book-shop/fcj-book-shop/** folder.
    - Create **book_delete.py** file and copy the below code block to it.
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

4. Run the following command to deploy SAM.
    ```
    sam build
    sam validate
    sam deploy
    ```
    ![LambdaDeleteFunction](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/49.png?width=90pc)

5. Open [AWS Lambda console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions).
    - Click **book_delete** function created.
    ![LambdaDeleteFunction](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/50.png?width=90pc)
    - At **book_delete** page.
      - Click **Configuration** tab.
      - Select **Permissions** on the left menu.
      - Click on the role that the function is executing.
      ![LambdaDeleteFunction](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/51.png?width=90pc)
    - At **fcj-book-shop-BookDeleteRole-...** page.
      - Check the permissions granted to the function.
      ![LambdaDeleteFunction](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/52.png?width=90pc)