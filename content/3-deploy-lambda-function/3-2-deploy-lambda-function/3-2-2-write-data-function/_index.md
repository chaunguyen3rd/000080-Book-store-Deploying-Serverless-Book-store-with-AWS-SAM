---
title : "Writing Lambda function"
date :  2025-02-11
weight : 2
chapter : false
pre : " <b> 3.2.2 </b> "
---
In this step we create a new Lambda function to write data to DynamoDB on SAM.

1. Open **template.yaml** file in **fcj-book-shop** folder.

2. Add the following scripts at the end of the file to create a Lambda function that writes data to DynamoDB.
    - Firstly, we will create **bookImageShopBucketName** and **bookImageResizeShopBucketName** parameters.

      ```yml
      bookImageShopBucketName:
        Type: String
        Default: book-image-shop-by-myself

      bookImageResizeShopBucketName:
        Type: String
        Default: book-image-resize-shop-by-myself
      ```

      ![LambdaCreateFunction](/images/temp/1/38.png?width=90pc)
    - Next, we will create **BookImageShop** and **BookImageResizeShop** which saves the image after resizing with CORS rule and policy settings.

      ```yml
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
    - Then, we add the following scripts to create **BookCreate** function.

      ```yml
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

      > If you create S3 bucket name is different from the one in the lab, please update **Policies | Resources** of **book_create** function with that name.

3. The directory structure is as follows.

    ```txt
    fcj-book-shop
    ├── fcj-book-shop
    │   ├── books_list
    │   │   └── books_list.py
    │   ├── book_create
    │       └── book_create.py
    │       └── requirements.txt
    └── template.yaml
    ```

    - Create **book_create** folder in **fcj-book-shop/fcj-book-shop/** folder.
    - Create **book_create.py** file and copy the below code block to it.

      ```py
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

    - In **fcj-book-shop/fcj-book-shop/book_create** folder, create **requirements.txt** file.
    - Copy the below code block to it.

    ```txt
    multipart
    ```

4. Run the following command to deploy SAM.

    ```bash
    sam build
    sam validate
    sam deploy
    ```

    ![LambdaCreateFunction](/images/temp/1/41.png?width=90pc)

5. Open [AWS Lambda console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions).
    - Click **book_create** function created.
      ![LambdaCreateFunction](/images/temp/1/42.png?width=90pc)
    - At **book_create** page.
      - Click **Configuration** tab.
      - Select **Permissions** on the left menu.
      - Click on the role that the function is executing.
        ![LambdaCreateFunction](/images/temp/1/43.png?width=90pc)
    - At **fcj-book-shop-BookCreateRole-...** page.
      - Check the permissions granted to the function.
        ![LambdaCreateFunction](/images/temp/1/44.png?width=90pc)

6. Open [Amazon S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1&region=ap-southeast-1).
    - The **book-image-resize-shop-by-myself** and **book-image-shop-by-myself** has been created.
      ![LambdaCreateFunction](/images/temp/1/45.png?width=90pc)
    - Click on **book-image-resize-shop-by-myself** bucket. At **book-image-resize-shop-by-myself** page.
      - Click **Permissions** tab.
      - Check the **Bucket policy** information.
        ![LambdaCreateFunction](/images/temp/1/46.png?width=90pc)
      - Scroll down to the bottom, and check the **Cross-origin resource sharing (CORS)**.
        ![LambdaCreateFunction](/images/temp/1/47.png?width=90pc)
