---
title : "Dọn dẹp tài nguyên"
date :  "`r Sys.Date()`" 
weight : 7
chapter : false
pre : " <b> 7. </b> "
---
1. Dọn dẹp S3 bucket.
- Mở [AWS S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1)
- Chọn **fcj-book-shop-by-myself**.
- Nhấn **Empty**.
- Nhập **permanently delete**.
- Nhấn **Empty**.
- Thực hiện tương tự với bucket bắt đầu bằng **aws-sam-cli-managed-default-**.

2. Xóa các stack CloudFormation.
- Thực thi lệnh dưới đây để xóa ứng dụng AWS SAM.
```
sam delete --stack-name fcj-book-shop
sam delete --stack-name aws-sam-cli-managed-default
```