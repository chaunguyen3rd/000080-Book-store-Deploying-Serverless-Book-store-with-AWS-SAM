---
title : "Create DynamoDB table"
date :  2025-02-11
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---
1. Open **template.yaml** file in **fcj-book-shop** folder.

2. Copy the following scripts into that file.

    ```yml
    booksTableName:
      Type: String
      Default: Books
    ```

    ![CreateDynamoDBTable](/images/temp/1/26.png?width=90pc)

    ```yml
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
    - The above script creates the **Books** table in DynamoDB with the partition key of id, the sort key of rv_id and a Local Secondary Index.

3. Run the following command to deploy SAM.

    ```bash
    sam build
    sam validate
    sam deploy
    ```

    ![CreateDynamoDBTable](/images/temp/1/28.png?width=90pc)

4. Back to DynamoDB console. At **Tables** page.
    - Click **Books** table.
    ![CreateDynamoDBTable](/images/temp/1/29.png?width=90pc)
    - At **Books** page.
      - Check information of this table.
      ![CreateDynamoDBTable](/images/temp/1/30.png?width=90pc)
      - Click **Indexes** tab.
      - Check the **Local secondary indexes** information.
      ![CreateDynamoDBTable](/images/temp/1/31.png?width=90pc)
      So you have created the **Books** table with the Local secondary index **name-index**.

5. To add data to the table, you can download the below file. Then, open file and replace all **AWS-REGION** with the region that create S3 bucket - **book-image-resize-shop-by-myself**, such as: `us-east-1`.
    {{%attachments title="Data" pattern=".*\.(json)$"/%}}

6. Run the following command at the directory where you save the **dynamoDB.json** file.

    ```bash
    aws dynamodb batch-write-item --request-items file://dynamoDB.json
    ```

    ![CreateDynamoDBTable](/images/temp/1/32.png?width=90pc)
