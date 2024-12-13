---
title : "Deploy front-end"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 2. </b> "
---
In this step, we will create an S3 bucket with Static web hosting enabled and publicly accessible based on SAM.

1. Open **template.yaml** file in **fcj-book-shop** folder that we created in part 1.
    - Delete unnecessary part.
![CreateS3Bucket](/images/temp/1/12.png?width=90pc)

2. Copy the following scripts into that file.
    ```
    Parameters:
      fcjBookShopBucketName:
        Type: String
        Default: fcj-book-shop-by-myself    
    ```
    ![CreateS3Bucket](/images/temp/1/13.png?width=90pc)

    ```
    FcjBookShop:
      Type: AWS::S3::Bucket
      Properties:
        BucketName: !Ref fcjBookShopBucketName
        WebsiteConfiguration:
          IndexDocument: index.html
        PublicAccessBlockConfiguration:
          BlockPublicAcls: false
          BlockPublicPolicy: false
          IgnorePublicAcls: false
          RestrictPublicBuckets: false

    FcjBookShopPolicy:
      Type: AWS::S3::BucketPolicy
      Properties:
        Bucket: !Ref FcjBookShop
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
                  - !Ref FcjBookShop
                  - /*
    ```

    The above script defines an S3 bucket is  **fcj-book-shop** with **FcjBookShopPolicy** policy - allow public access.
    ![CreateS3Bucket](/images/temp/1/14.png?width=90pc)

3. Run the below command.
    - To build at the directory of the SAM project: **fcj-book-shop**.
      ```
      sam build
      ```
    - To check the validation of the SAM template.
      ```
      sam validate
      ```
      ![CreateS3Bucket](/images/temp/1/15.png?width=90pc)

    - To deploy SAM.
      ```
      sam deploy --guided
      ```
      - Enter stack name: `fcj-book-shop`
      - Enter the deployemnt region, such as: `us-east-1`- should be the same as the default region.
      - Then enter other information as shown below.
      ![CreateS3Bucket](/images/temp/1/16.png?width=90pc)
      - Wait a while to create the CloudFormation stack changeset.
      - Enter "y" when **Deploy this changeset?**.
      ![CreateS3Bucket](/images/temp/1/17.png?width=90pc)

4. Open [Amazon S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1&region=ap-southeast-1).
    - Check if the bucket has been created or not, click **fcj-book-shop-by-myself** bucket.
    ![CreateS3Bucket](/images/temp/1/18.png?width=90pc)

5. At **fcj-book-shop-by-myself** page.
    - Click **Properties** tab.
    ![CreateS3Bucket](/images/temp/1/19.png?width=90pc)
    - Then scroll down, check state of **Static website hosting**.
    - Record the endpoint of the website.
    ![CreateS3Bucket](/images/temp/1/20.png?width=90pc)
    - Click **Permissions** tab.
    - Check the policy has been added.
    ![CreateS3Bucket](/images/temp/1/21.png?width=90pc)

6. Open [CloudFormation console](https://ap-southeast-1.console.aws.amazon.com/cloudformation/home?region=ap-southeast-1#/stacks?filteringStatus=active&filteringText=&viewNested=true&hideStacks=false). Two stacks have been created.
    - Click **fcj-book-shop** stack.
    ![CreateS3Bucket](/images/temp/1/22.png?width=90pc)

7. At **fcj-book-shop** page.
    - Click **Resource** tab, see the resources that CloudFormation has initialized.
    ![CreateS3Bucket](/images/temp/1/23.png?width=90pc)
    - Click to other stack and see other resources.
    ![CreateS3Bucket](/images/temp/1/24.png?width=90pc)

8. Download **fcj-serverless-frontend** code to your device
    - Open a terminal on your computer at the directory where you want to save the source code.
    - Copy and run the below command.
      ```
      git clone https://github.com/AWS-First-Cloud-Journey/FCJ-Serverless-Workshop.git
      cd FCJ-Serverless-Workshop
      yarn
      yarn build
      ```
9. We have finished building the front-end. Next, execute the following command to upload the **build** folder to S3.
    ```
    aws s3 cp build s3://fcj-book-shop-by-myself --recursive
    ```
    Result after uploading:
    ![CreateS3Bucket](/images/temp/1/25.png?width=90pc)



