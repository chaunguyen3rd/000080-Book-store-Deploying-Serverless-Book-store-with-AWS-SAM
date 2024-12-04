---
title : "Deleting Lambda function"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3.2.3 </b> "
---
We will create a Lambda function that deletes all items with the specified partition key and sort key in the DynamoDB table. And delete the image file in the S3 bucket:
1. Open **template.yaml** file in **fcj-book-shop** folder
2. Add the following script at the end of the file to create a Lambda function that deletes data of DynamoDB table
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
    - Add the following script at the end of the file to create an S3 bucket that saves the image after resizing with CORS rule and policy settings
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
If you create S3 bucket names that are different from the ones in the lab, please check **Policies | Resources** of services and update.
{{% /notice %}}

![LambdaDeleteFunction](/images/1/40.png?width=90pc)
![LambdaDeleteFunction](/images/1/41.png?width=90pc)

3. The directory structure is as follows:
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
    - Create **book_delete** folder in **fcj-book-shop/fcj-book-shop/** folder
    - Create **book_delete.py** file and copy the below code block to it 
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

4. Run the following command to deploy SAM
    ```
    sam build
    sam deploy
    ```
5. Back to Lambda console
    - Click **book_delete** function created
![LambdaDeleteFunction](/images/1/42.png?width=90pc)

6. Click **Configuration** tab
    - Select **Permissions** on the left menu
    - Click on the role that the function is executing
![LambdaDeleteFunction](/images/1/43.png?width=90pc)

7. Expand the policy, see the permissions granted to the function
![LambdaDeleteFunction](/images/1/44.png?width=90pc)

8. Open [Amazon S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1&region=ap-southeast-1), the **book-image-resize-shop** bucket created
    - Click to this bucket
![LambdaDeleteFunction](/images/1/45.png?width=90pc)

9. Click **Permissions** tab, see the policy added to the bucket
![LambdaDeleteFunction](/images/1/46.png?width=90pc)

10. Scroll down to bottom, see **Cross-origin resource sharing** have been configed
![LambdaDeleteFunction](/images/1/47.png?width=90pc)
