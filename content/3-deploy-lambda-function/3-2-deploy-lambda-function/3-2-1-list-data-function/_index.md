---
title : "Listing Lambda function"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 3.2.1 </b> "
---
We will create a Lambda function that reads all the data in the DynamoDB table.

1. Open **template.yaml** file in **fcj-book-shop** folder.

2. Add the following script at the end of the file.
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
    ![LambdaListFunction](/images/temp/1/33.png?width=90pc)

3. The directory structure is as follows.
    ```
    fcj-book-shop
    ├── fcj-book-shop
    │   ├── books_list
    │       └── books_list.py
    └── template.yaml

    ```
    - Create **fcj-book-shop/books_list** folder in **fcj-book-shop** folder.
    - Create **books_list.py** file and copy the below code block to it.
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

4. Run the following command to deploy SAM.
    ```
    sam build
    sam validate
    sam deploy
    ```
    ![LambdaListFunction](/images/temp/1/34.png?width=90pc)

5. Open [AWS Lambda console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions). 
    - Click **books_list** function created.
    ![LambdaListFunction](/images/temp/1/35.png?width=90pc)
    - At **books_list** page.
      - Click **Configuration** tab.
      - Select **Permissions** on the left menu.
      - Click on the role that the function is executing.
      ![LambdaListFunction](/images/temp/1/36.png?width=90pc)
    - At **fcj-book-shop-BooksListRole-...** page.
      - Check the permissions granted to the function.
      ![LambdaListFunction](/images/temp/1/37.png?width=90pc)


