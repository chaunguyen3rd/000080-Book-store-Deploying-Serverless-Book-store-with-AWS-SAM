---
title : "Chuẩn bị"
date :  2025-02-11
weight : 1
chapter : false
pre : " <b> 4.1. </b> "
---
Trong bước này, chúng ta thực hiện một số bước chuẩn bị để tạo các REST API sau này.

1. Mở **template.yaml** trong thư mục **fcj-book-shop**.

2. Thêm đoạn mã sau vào cuối tệp.
    - Đầu tiên, chúng ta sẽ tạo các tham số **apiType**, **binaryMediaType** và **getOrPostPathPart**.

      ```yml
      apiType:
        Type: String
        Default: REGIONAL

      binaryMediaType:
        Type: String
        Default: multipart/form-data

      getOrPostPathPart:
        Type: String
        Default: books

      deletePathPart:
        Type: String
        Default: "{id}"
      ```

      ![PrepRestApi](/images/temp/1/61.png?width=90pc)
    - Tiếp theo, chúng ta sẽ tạo **BookApi** RestApi và **BookApiResource** Resource.

      ```yml
      BookApi:
        Type: AWS::ApiGateway::RestApi
        Properties:
          Name: fcj-serverless-api
          EndpointConfiguration:
            Types:
              - !Ref apiType
          BinaryMediaTypes:
            - !Ref binaryMediaType

      BookApiResource:
        Type: AWS::ApiGateway::Resource
        Properties:
          RestApiId: !Ref BookApi
          ParentId: !GetAtt BookApi.RootResourceId
          PathPart: !Ref getOrPostPathPart

      BookDeleteApiResource:
        Type: AWS::ApiGateway::Resource
        Properties:
          RestApiId: !Ref BookApi
          ParentId: !Ref BookApiResource
          PathPart: !Ref deletePathPart
      ```

      ![PrepRestApi](/images/temp/1/62.png?width=90pc)

3. Chạy lệnh sau để triển khai SAM.

    ```bash
    sam build
    sam validate
    sam deploy
    ```

    ![PrepRestApi](/images/temp/1/63.png?width=90pc)

4. Mở [AWS API Gateway console](https://us-east-1.console.aws.amazon.com/apigateway/home?region=us-east-1).
    - Nhấp vào **fcj-serverless-api** REST api.
      ![PrepRestApi](/images/temp/1/64.png?width=90pc)
    - Tại trang tài nguyên **fcj-serverless-api**.
      - Nhấp vào **Resources**.
      - Chọn **/books**.
      - Kiểm tra thông tin **Resource details**.
        ![PrepRestApi](/images/temp/1/65.png?width=90pc)
      - Chọn **/{id}**.
      - Kiểm tra thông tin **Resource details**.
        ![PrepRestApi](/images/temp/1/66.png?width=90pc)
      - Nhấp vào **API settings**.
      - Kiểm tra loại phương tiện **multipart/form-data** tại **Binary media types**.
        ![PrepRestApi](/images/temp/1/84.png?width=90pc)

Vậy là chúng ta đã hoàn thành một số bước chuẩn bị. Tiếp theo, chúng ta sẽ tạo các API GET, POST và DELETE.
