---
title : "Resizing image Lambda function"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 3.2.4 </b> "
---
In this step we create a new Lambda function that resizes the image after the user uploads it.

1. Open **template.yaml** file in **fcj-book-shop** folder.

2. Add the following script at the end of the file to create resizing image function.
    - Firstly, we will create **width** and **height** parameters.
    ```
    width:
      Type: String
      Default: 200px

    height:
      Type: String
      Default: 280px
    ```
    ![LambdaResizeFunction](/images/temp/1/53.png?width=90pc)
    - Then, we add the following scripts to create **ImageResize** function.
    ```
    ImageResize:
      Type: AWS::Serverless::Function
      Properties:
        CodeUri: fcj-book-shop/resize_image/function.zip
        PackageType: Zip
        Handler: index.handler
        Runtime: nodejs20.x
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
                  - !Sub "arn:aws:s3:::${bookImageShopBucketName}/*"
                  - !Sub "arn:aws:s3:::${bookImageResizeShopBucketName}/*"
        Events:
          ResizeImage:
            Type: S3
            Properties:
              Bucket: !Ref BookImageShop
              Events: s3:ObjectCreated:*
        Environment:
          Variables:
            WIDTH: !Ref width
            HEIGHT: !Ref height
            DES_BUCKET: !Ref BookImageResizeShop
    ```
    ![LambdaResizeFunction](/images/temp/1/54.png?width=90pc)
    - Next, add the following script at the end of the file to grant permission to the **book-image-shop-by-myself** bucket to use this function.
    ```
    ImageResizeInvokePermission:
      Type: "AWS::Lambda::Permission"
      Properties:
        FunctionName: !Ref ImageResize
        Action: "lambda:InvokeFunction"
        Principal: "s3.amazonaws.com"
        SourceAccount: !Sub ${AWS::AccountId}
        SourceArn: !GetAtt BookImageShop.Arn
    ```
    ![LambdaResizeFunction](/images/temp/1/55.png?width=90pc)

{{% notice warning %}}
If you create S3 bucket names that are different from the ones in the lab, please check **Policies | Resources** or **Environment** of resources and update.
{{% /notice %}}

3. The directory structure is as follows:
    ```
    fcj-book-shop
    ├── fcj-book-shop
    │   ├── books_list
    │   │   └── books_list.py
    │   ├── book_create
    │       └── requirements.txt
    │   │   └── book_create.py
    │   ├── book_delete
    │   │   └── book_delete.py
    │   ├── resize_image
    │       └── function.zip
    └── template.yaml
    ```
    - Create **resize_image** folder in **fcj-book-shop/fcj-book-shop/** folder.
    - Download the below file and copy to this folder.

    {{%attachments title="Source code" pattern=".*\.(zip)$"/%}}

4. Run the following command to deploy SAM.
    ```
    sam build
    sam validate
    sam deploy
    ```
    ![LambdaResizeFunction](/images/temp/1/56.png?width=90pc)

5. Open [AWS Lambda console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions).
    - Click **resize_image** function created
    ![LambdaResizeFunction](/images/temp/1/57.png?width=90pc)
    - At **resize_image** page.
      - Click **Configuration** tab.
      - Select **Permissions** on the left menu.
      - Click on the role that the function is executing.
      ![LambdaResizeFunction](/images/temp/1/58.png?width=90pc)
    - At **fcj-book-shop-ImageResizeRole-...** page.
      - Check the permissions granted to the function.
      ![LambdaResizeFunction](/images/temp/1/59.png?width=90pc)