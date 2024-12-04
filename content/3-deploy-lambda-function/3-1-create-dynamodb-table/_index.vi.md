---
title : "Tạo bảng trong DynamoDB"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---
1. Mở tệp **template.yaml** trong thư mục **fcj-book-store**
2. Thêm đoạn script sau vào cuối tệp để tạo một bảng đơn giản trong DynamoDB:
      ```
        SimpleTable:
          Type: AWS:Serverless::SimpleTable
          Properties:
            TableName: SimpleTable
            PrimaryKey:
              Name: id
              Type: String
      ```
    - Đoạn script định nghĩa một bảng **SimpleTable** trong DynamoDB với Partition key là **id**
![CreateDynamoDBTable](/images/1/23.png?width=90pc)
3. Chạy dòng lệnh dưới đây triển khai SAM
    ```
    sam build
    sam deploy
    ```

4. Mở bảng điều khiển của [DynamoDB](https://ap-southeast-1.console.aws.amazon.com/dynamodbv2/home?region=ap-southeast-1#dashboard)
5. Chọn **Tables** ở menu phía bên trái. Bạn sẽ thấy **SimpleTable** đã được tạo
    ![CreateDynamoDBTable](/images/1/24.png?width=90pc)

6. Nhưng bảng của chúng ta cần nhiều thiết lập hơn. Vì vậy bạn hãy xoá đoạn script trên đi thay bằng đoạn sau:
      ```
        BooksTable:
          Type: AWS::DynamoDB::Table
          Properties:
            TableName: Books
            BillingMode: PAY_PER_REQUEST
            AttributeDefinitions:
              - AttributeName: id
                AttributeType: S
              - AttributeName: rv_id
                AttributeType: N
              - AttributeName: name
                AttributeType: S
            KeySchema:
              - AttributeName: id
                KeyType: HASH
              - AttributeName: rv_id
                KeyType: RANGE
            LocalSecondaryIndexes:
              - IndexName: name-index
                KeySchema:
                  - AttributeName: id
                    KeyType: HASH
                  - AttributeName: name
                    KeyType: RANGE
                Projection:
                  ProjectionType: ALL
      ```
    - Đoạn script trên giúp tạo bảng **Books** trong DynamoDB với partition key là id, sort key là rv_id và một Local Scondary Index.
![CreateDynamoDBTable](/images/1/25.png?width=90pc)


7. Chạy dòng lệnh dưới đây triển khai SAM
      ```
      sam build
      sam deploy
      ```
    ![CreateDynamoDBTable](/images/1/26.png?width=90pc)
8. Quay lại với bảng điều khiển của DynamoDB. Bảng **Books** đã được tạo và bảng **SimpleTable** đã xoá.
![CreateDynamoDBTable](/images/1/27.png?width=90pc)
9. Ấn chọn bảng Books. Kiểm tra các thông tin của bảng
![CreateDynamoDBTable](/images/1/28.png?width=90pc)
    - Ấn sang tab **Indexes**
![CreateDynamoDBTable](/images/1/29.png?width=90pc)
Vậy là bạn đã tạo xong bảng **Books** với Local secondary index là **name-index**

10. Để thêm dữ liệu cho bảng, bạn có thể tải tệp dưới đây. Mở tệp, sau đó thay toàn bộ **AWS-REGION** bằng vùng mà bạn tạo S3 bucket **book-image-resize-store**, ví dụ: `ap-southeast-1`

  {{%attachments title="Data" pattern=".*\.(json)$"/%}}


11. Chạy câu lệnh sau tại thư mục mà bạn lưu tệp **dynamoDB.json**
    ```
    aws dynamodb batch-write-item --request-items file://dynamoDB.json
    ```
    ![CreateDynamoDBTable](/images/1/30.png?width=90pc)


