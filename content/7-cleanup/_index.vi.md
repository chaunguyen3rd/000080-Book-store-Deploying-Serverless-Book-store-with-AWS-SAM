---
title : "Dọn dẹp tài nguyên"
date :  "`r Sys.Date()`" 
weight : 7
chapter : false
pre : " <b> 7. </b> "
---
1. Làm rỗng S3 bucket
- Mở bảng điều khiển của [AWS S3](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1)
- Chọn **fcj-book-store**
- Ấn **Empty**
- Nhập **permanently delete**
- Ấn **Empty**
- Làm tương tự với bucket bắt đầu bằng **aws-sam-cli-managed-default-**
2. Xoá stack của CloudFormation
- Chạy câu lệnh dưới đây để xoá ứng dụng AWS SAM
```
sam delete --stack-name fcj-book-store
sam delete --stack-name aws-sam-cli-managed-default
```
