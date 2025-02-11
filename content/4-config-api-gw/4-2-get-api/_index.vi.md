---
title : "Tạo GET API"
date :  2025-02-11
weight : 2
chapter : false
pre : " <b> 4.2. </b> "
---
1. Mở **template.yaml** trong thư mục **fcj-book-shop**.

2. Thêm đoạn mã sau vào cuối tệp để tạo REST API và phương thức **GET**.
    - Đầu tiên, chúng ta sẽ tạo tham số **stage**.

    ```yml
    stage:
      Type: String
      Default: staging
    ```

    ![CreateGetAPI](/images/temp/1/67.png?width=90pc)
    - Tiếp theo, thêm các đoạn mã sau để tạo phương thức **BookApiGet**, triển khai **BookApiDeployment**, giai đoạn **BookApiStage** và quyền **BookApiGetInvokePermission**.

    ```yml
    BookApiGet:
      Type: AWS::ApiGateway::Method
      Properties:
        HttpMethod: GET
        RestApiId: !Ref BookApi
        ResourceId: !Ref BookApiResource
        AuthorizationType: NONE
        Integration:
          Type: AWS_PROXY
          IntegrationHttpMethod: POST # For Lambda integrations, you must set the integration method to POST
          Uri: !Sub >-
            arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${BooksList.Arn}/invocations
          IntegrationResponses:
            - StatusCode: "200"
              ResponseParameters:
                method.response.header.Access-Control-Allow-Origin: "'*'"
                method.response.header.Access-Control-Allow-Methods: "'GET,POST,OPTIONS'"
                method.response.header.Access-Control-Allow-Headers: "'Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token'"
        MethodResponses:
          - StatusCode: "200"
            ResponseParameters:
              method.response.header.Access-Control-Allow-Origin: "'*'"
              method.response.header.Access-Control-Allow-Methods: "'GET,POST,OPTIONS'"
              method.response.header.Access-Control-Allow-Headers: "'Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token'"

    BookApiDeployment:
      Type: AWS::ApiGateway::Deployment
      Properties:
        RestApiId: !Ref BookApi
      DependsOn:
        - BookApiGet

    BookApiStage:
      Type: AWS::ApiGateway::Stage
      Properties:
        RestApiId: !Ref BookApi
        StageName: !Ref stage
        DeploymentId: !Ref BookApiDeployment

    BookApiGetInvokePermission:
      Type: AWS::Lambda::Permission
      Properties:
        FunctionName: !Ref BooksList
        Action: lambda:InvokeFunction
        Principal: apigateway.amazonaws.com
        SourceAccount: !Ref "AWS::AccountId"
    ```

    ![CreateGetAPI](/images/temp/1/68.png?width=90pc)

3. Chạy lệnh sau để triển khai SAM.

    ```bash
    sam build
    sam validate
    sam deploy
    ```

    ![CreateGetAPI](/images/temp/1/69.png?width=90pc)

4. Mở [AWS API Gateway console](https://us-east-1.console.aws.amazon.com/apigateway/home?region=us-east-1).
    - Nhấp vào **fcj-serverless-api** REST api.
    ![PrepRestApi](/images/temp/1/64.png?width=90pc)
    - Tại trang tài nguyên **fcj-serverless-api**.
      - Nhấp vào **Resources**.
      - Chọn **GET**.
      - Nhấp vào **Lambda integration** và kiểm tra hàm **books_list**.
      ![CreateGetAPI](/images/temp/1/70.png?width=90pc)
      - Nhấp vào **Stages**.
      - Chọn **GET**.
      - Sao chép và lưu **Invoke URL**.
      ![CreateGetAPI](/images/temp/1/71.png?width=90pc)
