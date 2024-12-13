---
title : "Tạo bảng trong DynamoDB"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---
1. Mở tệp **template.yaml** trong thư mục **fcj-book-shop**.

2. Sao chép các đoạn mã sau vào tệp đó.
    ```
    booksTableName:
      Type: String
      Default: Books
    ```
    ![CreateDynamoDBTable](/images/temp/1/26.png?width=90pc)

    ```
    BooksTable:
      Type: AWS::DynamoDB::Table
      Properties:
        TableName: !Ref booksTableName
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
    ![CreateDynamoDBTable](/images/temp/1/27.png?width=90pc)
    - Đoạn mã trên tạo bảng **Books** trong DynamoDB với khóa phân vùng là id, khóa sắp xếp là rv_id và một Chỉ mục phụ cục bộ.

3. Chạy lệnh sau để triển khai SAM.
    ```
    sam build
    sam validate
    sam deploy
    ```
    ![CreateDynamoDBTable](/images/temp/1/28.png?width=90pc)

4. Quay lại bảng điều khiển DynamoDB. Tại trang **Tables**.
    - Nhấp vào bảng **Books**.
    ![CreateDynamoDBTable](/images/temp/1/29.png?width=90pc)
    - Tại trang **Books**.
      - Kiểm tra thông tin của bảng này.
      ![CreateDynamoDBTable](/images/temp/1/30.png?width=90pc)
      - Nhấp vào tab **Indexes**.
      - Kiểm tra thông tin **Local secondary indexes**.
      ![CreateDynamoDBTable](/images/temp/1/31.png?width=90pc)
      Vậy là bạn đã tạo bảng **Books** với chỉ mục phụ cục bộ **name-index**.    

5. Để thêm dữ liệu vào bảng, bạn có thể tải xuống tệp dưới đây. Sau đó, mở tệp và thay thế tất cả **AWS-REGION** bằng vùng mà bạn đã tạo S3 bucket - **book-image-resize-shop-by-myself**, chẳng hạn như: `us-east-1`.
    {{%attachments title="Data" pattern=".*\.(json)$"/%}}

6. Chạy lệnh sau tại thư mục nơi bạn lưu tệp **dynamoDB.json**.
    ```
    aws dynamodb batch-write-item --request-items file://dynamoDB.json
    ```
    ![CreateDynamoDBTable](/images/temp/1/32.png?width=90pc)