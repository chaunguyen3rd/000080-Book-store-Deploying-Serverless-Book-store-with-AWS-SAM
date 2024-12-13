---
title : "Tạo POST API"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 4.3. </b> "
---
1. Mở tệp **template.yaml** trong thư mục **fcj-book-shop**.

2. Thêm đoạn mã sau vào cuối tệp để tạo phương thức **POST**.
    - Đầu tiên, chúng ta cần làm mới để tạo một phiên bản triển khai mới cho **POST** Api trong vài bước tiếp theo. Bình luận khối **BookApiDeployment**.
    ```
    # BookApiDeployment:
    #   Type: AWS::ApiGateway::Deployment
    #   Properties:
    #     RestApiId: !Ref BookApi
    #   DependsOn:
    #     - BookApiGet

    BookApiStage:
      Type: AWS::ApiGateway::Stage
      Properties:
        RestApiId: !Ref BookApi
        StageName: !Ref stage
        # DeploymentId: !Ref BookApiDeployment
    ```
    ![CreatePostAPI](/images/temp/1/72.png?&width=90pc)
    - Chạy lệnh sau để triển khai SAM.
    ```
    sam build
    sam validate
    sam deploy
    ```
    ![CreatePostAPI](/images/temp/1/73.png?&width=90pc)
    - Tiếp theo, chúng ta sẽ tạo **BookApiCreate** và **BookApiCreateInvokePermission**.
    ```
    BookApiCreate:
      Type: AWS::ApiGateway::Method
      Properties:
        HttpMethod: POST
        RestApiId: !Ref BookApi
        ResourceId: !Ref BookApiResource
        AuthorizationType: NONE
        Integration:
          Type: AWS_PROXY
          IntegrationHttpMethod: POST # For Lambda integrations, you must set the integration method to POST
          Uri: !Sub >-
            arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${BookCreate.Arn}/invocations
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

    BookApiCreateInvokePermission:
      Type: AWS::Lambda::Permission
      Properties:
        FunctionName: !Ref BookCreate
        Action: lambda:InvokeFunction
        Principal: apigateway.amazonaws.com
        SourceAccount: !Ref "AWS::AccountId"
    ```
    ![CreatePostAPI](/images/temp/1/74.png?&width=90pc)
    - Sau đó, chúng ta bỏ ghi chú khối mã mà chúng ta đã ghi chú ở trên.
    ```
    BookApiDeployment:
      Type: AWS::ApiGateway::Deployment
      Properties:
        RestApiId: !Ref BookApi
      DependsOn:
        - BookApiGet
        - BookApiCreate

    BookApiStage:
      Type: AWS::ApiGateway::Stage
      Properties:
        RestApiId: !Ref BookApi
        StageName: !Ref stage
        DeploymentId: !Ref BookApiDeployment
    ```
    ![CreatePostAPI](/images/temp/1/75.png?&width=90pc)

3. Chạy lệnh sau để triển khai SAM.
    ```
    sam build
    sam validate
    sam deploy
    ```
    ![CreatePostAPI](/images/temp/1/76.png?&width=90pc)

4. Mở [AWS API Gateway console](https://us-east-1.console.aws.amazon.com/apigateway/home?region=us-east-1).
    - Nhấp vào **fcj-serverless-api** REST api.
    ![PrepRestApi](/images/temp/1/64.png?width=90pc)
    - Tại trang tài nguyên **fcj-serverless-api**.
      - Nhấp vào **Resources**.
      - Chọn **POST**.
      - Nhấp vào **Lambda integration** và kiểm tra hàm **book_create**.     
      ![CreatePostAPI](/images/temp/1/77.png?&width=90pc)
      - Nhấp vào **Stages**.
      - Chọn **POST**.
      - Sao chép và lưu **Invoke URL**.     
      ![CreatePostAPI](/images/temp/1/78.png?&width=90pc)
