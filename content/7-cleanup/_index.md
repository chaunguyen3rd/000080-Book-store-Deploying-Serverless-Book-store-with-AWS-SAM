---
title : "Cleanup"
date :  "`r Sys.Date()`" 
weight : 7
chapter : false
pre : " <b> 7. </b> "
---
1. Empty S3 bucket
- Open [AWS S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1)
- Select **fcj-book-store**
- Click **Empty**
- Enter **permanently delete**
- Click **Empty**
- Do the same for bucket starting with **aws-sam-cli-managed-default-**
2. Delete CloudFormation stacks
- Execute the below command to delete the AWS SAM application
```
sam delete --stack-name fcj-book-store
sam delete --stack-name aws-sam-cli-managed-default
```