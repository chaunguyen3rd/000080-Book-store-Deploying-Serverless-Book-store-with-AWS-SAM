---
title : "Tạo GET API"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 4.2. </b> "
---
1. Mở tệp **template.yaml** trong thư mục **fcj-book-shop**
2. Thêm đoạn script sau vào cuối tệp tạo một REST API và GET method
    ```
      BookApi:
        Type: AWS::Serverless::Api
        Name: fcj-serverless-api
        Properties:
          StageName: staging
          Cors: "'*'"      # enable CORS for API
          DefinitionBody:
            openapi: 3.0.1
            info:
              description: "This is the APIs for book shop web app"
              version: "1.0.0"
              title: "API Gateway REST API to Lambda"
            paths:
              /books:
                get:
                  responses:
                    "200":
                      description: 200 response
                      headers:
                        Access-Control-Allow-Origin:
                          type: string
                  x-amazon-apigateway-integration:
                    uri:
                      Fn::Sub: "arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${BooksList.Arn}/invocations"
                    responses:
                      default:
                        statusCode: 200
                        responseParameters:
                          method.response.header.Access-Control-Allow-Origin: "'*'"
                    passthroughBehavior: when_no_match
                    httpMethod: POST #always POST
                    type: aws_proxy
    ```
    ![CreateGetAPI](/images/1/53.png?width=90pc)

  - Thêm đoạn script sau vào cuối của function **BooksList** 
    ```
          Events:
            ListBook:
              Type: Api
              Properties:
                Path: /books/
                Method: get
                RestApiId:
                  Ref: BookApi
    ```
    ![CreateGetAPI](/images/1/54.png?width=90pc)

2. Chạy dòng lệnh dưới đây triển khai SAM
    ```
    sam build
    sam deploy
    ```
    ![CreateGetAPI](/images/1/55.png?width=90pc)
{{% notice note %}}
Nhập "y" nếu được hỏi "BooksList may not have authorization defined, Is this okay? [y/N]: "
{{% /notice %}}


3. Mở bảng điều khiển của function **books_list**
    - Ấn vào **API Gateway**
    ![CreateGetAPI](/images/1/56.png?width=90pc)

4. Hiện thị API Gateway đang được tương tác với function
    - Ấn vào API Gateway đó
    ![CreateGetAPI](/images/1/57.png?width=90pc)

5. Hiện thị các resource và GET method
    ![CreateGetAPI](/images/1/58.png?width=90pc)

6. Chọn tab **Stages** ở menu phía bên trái
    - Ấn chọn **staging**
    - Ấn chọn **GET**
    - Ghi lại **InvokeURL** của method GET
![CreateGetAPI](/images/1/59.png?width=90pc)






