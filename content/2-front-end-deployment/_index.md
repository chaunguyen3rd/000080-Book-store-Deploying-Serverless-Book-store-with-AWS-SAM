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

    
    ```
    The above script defines an S3 bucket is  **fcj-book-shop** with **FcjBookShopPolicy** policy - allow public access
![CreateS3Bucket](/images/1/12.png?width=90pc)

1. Run the below command: 
    - To build at the directory of the SAM project: **fcj-book-shop**
      ```
      sam build
      ```
    - To check the validation of the SAM template
      ```
      sam validate
      ```
      ![CreateS3Bucket](/images/1/13.png?width=90pc)

    - To deploy SAM
      ```
      sam deploy --guided
      ```
    - Enter stack name: `fcj-book-shop`
    - Enter the deployemnt region, such as: `us-east-1`- should be the same as the default region
    - Then enter other information as shown below
![CreateS3Bucket](/images/1/14.png?width=90pc)
    - Wait a while to create the CloudFormation stack changeset
    - Enter "y" when **Deploy this changeset?** display
![CreateS3Bucket](/images/1/15.png?width=90pc)

1. Open [Amazon S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1&region=ap-southeast-1)
    - Check if the bucket has been created or not.
    - Click **fcj-book-shop** bucket
![CreateS3Bucket](/images/1/16.png?width=90pc)

1. Click **Properties** tab. Then scroll down, check state of **Static website hosting**
    - Record the endpoint of the website
![CreateS3Bucket](/images/1/17.png?width=90pc)

1. Click **Permissions** tab
    - See the policy has been added
![CreateS3Bucket](/images/1/18.png?width=90pc)

1. Open [CloudFormation console](https://ap-southeast-1.console.aws.amazon.com/cloudformation/home?region=ap-southeast-1#/stacks?filteringStatus=active&filteringText=&viewNested=true&hideStacks=false). Two stacks have been created
    - Click **fcj-book-shop** stack
![CreateS3Bucket](/images/1/19.png?width=90pc)

1.  Click **Resource** tab, see the resources that CloudFormation has initialized
![CreateS3Bucket](/images/1/20.png?width=90pc)

    - Click to other stack:
![CreateS3Bucket](/images/1/21.png?width=90pc)

1.  Download **fcj-serverless-frontend** code to your device
    - Open a terminal on your computer at the directory where you want to save the source code
    - Copy the below command
        ```
        git clone https://github.com/AWS-First-Cloud-Journey/FCJ-Serverless-Workshop.git
        cd FCJ-Serverless-Workshop
        yarn build
        ```
2.  We have finished building the front-end. Next, execute the following command to upload the **build** folder to S3
      ```
      aws s3 cp build s3://fcj-book-shop --recursive
      ```
Result after uploading:
![CreateS3Bucket](/images/1/22.png?width=90pc)



