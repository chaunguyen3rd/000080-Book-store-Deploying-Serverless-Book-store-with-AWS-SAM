---
title : "Cleanup"
date :  2025-02-11
weight : 7
chapter : false
pre : " <b> 7. </b> "
---
1. Empty S3 bucket.

    - Open [AWS S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1)
    - Select **fcj-book-shop-by-myself**.
    - Click **Empty**.
    - Enter **permanently delete**.
    - Click **Empty**.
    - Do the same for bucket starting with **aws-sam-cli-managed-default-**, **book-image-shop-by-myself** and **book-image-resize-shop-by-myself**.

2. Delete CloudFormation stacks.

    - Execute the below command to delete the AWS SAM application.

      ```bash
      sam delete --stack-name fcj-book-shop
      sam delete --stack-name aws-sam-cli-managed-default
      ```

    - If you have issues when deleting with command. Open [AWS Cloudformation console](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/getting-started). Then, delete all stacks related to this workshop.
