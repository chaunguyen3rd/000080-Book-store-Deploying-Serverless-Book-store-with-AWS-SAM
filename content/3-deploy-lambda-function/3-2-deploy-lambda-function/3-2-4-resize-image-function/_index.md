---
title : "Resizing image Lmabda function "
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 3.2.4 </b> "
---
In this step we create a new Lambda function that resizes the image after the user uploads it.

1. Open **template.yaml** file in **fcj-book-shop** folder
2. Add the following script at the end of the file to create resizing image function
    ```
    ImageResizer:
        Type: AWS::Serverless::Function
        Properties:
          CodeUri: fcj-book-shop/resize_image/function.zip
          PackageType: Zip
          Handler: index.handler
          Runtime: nodejs16.x
          FunctionName: resize_image
          Architectures:
            - x86_64
          Policies:
            - Statement:
                - Sid: ResizeUploadImage
                  Effect: Allow
                  Action:
                    - s3:GetObject
                    - s3:PutObject
                    - s3:DeleteObject
                  Resource:
                    - arn:aws:s3:::book-image-shop/*
                    - arn:aws:s3:::book-image-resize-shop/*
          Events:
            ResizeImage:
              Type: S3
              Properties:
                Bucket: !Ref BookImageShop
                Events: s3:ObjectCreated:*
          Environment:
            Variables:
              WIDTH: 200
              HEIGHT: 280
              DES_BUCKET: book-image-resize-shop
    ```

{{% notice warning %}}
If you create S3 bucket names that are different from the ones in the lab, please check **Policies | Resources** or **Environment** of resources and update.
{{% /notice %}}


  - Add the following script at the end of the file to grant permission to the **books-image-shop** bucket to use this function

    ```
    LambdaInvokePermission:
        Type: "AWS::Lambda::Permission"
        Properties:
          FunctionName: !GetAtt ImageResizer.Arn
          Action: "lambda:InvokeFunction"
          Principal: "s3.amazonaws.com"
          SourceAccount: !Sub ${AWS::AccountId}
          SourceArn: !GetAtt BookImageShop.Arn
    ```
    ![LambdaDeleteFunction](/images/1/48.png?width=90pc)  

3. The directory structure is as follows:
    ```
    fcj-book-shop
    ├── fcj-book-shop
    │   ├── books_list
    │   │   └── books_list.py
    │   ├── book_create
    │   │   └── book_create.py
    │   ├── book_delete
    │   │   └── book_delete.py
    │   ├── resize_image
    │       └── function.zip
    └── template.yaml

    ```
    - Create **resize_image** folder in **fcj-book-shop/fcj-book-shop/** folder
    - Download the below file and copy to this folder

    {{%attachments title="Source code" pattern=".*\.(zip)$"/%}}

4. Run the following command to deploy SAM
    ```
    sam build
    sam deploy
    ```
    ![LambdaDeleteFunction](/images/1/49.png?width=90pc) 

5. Back to Lambda console
    - Click **resize_image** function created
 ![LambdaDeleteFunction](/images/1/50.png?width=90pc) 

6. Click **Configuration** tab
    - Select **Trigger** on the left menu, display S3 bucket - **book-image-shop**

7. Select **Permissions** on the left menu
    - Click on the role that the function is executing
![LambdaDeleteFunction](/images/1/51.png?width=90pc) 

8. Expand the policy, see the permissions granted to the function
![LambdaDeleteFunction](/images/1/52.png?width=90pc) 



