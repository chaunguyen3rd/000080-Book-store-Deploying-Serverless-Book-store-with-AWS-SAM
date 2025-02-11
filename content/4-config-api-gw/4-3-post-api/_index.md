---
title : "Create POST API"
date :  2025-02-11
weight : 3
chapter : false
pre : " <b> 4.3. </b> "
---
1. Open **template.yaml** file in **fcj-book-shop** folder.

2. Add the following script at the end of the file that creates the **POST** method.
    - Firstly, we need to refresh to create a new deployment version for **POST** Api in a few next steps. Comment **BookApiDeployment** block.

    ```yml
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
    - Run the following command to deploy SAM.

    ```bash
    sam build
    sam validate
    sam deploy
    ```

    ![CreatePostAPI](/images/temp/1/73.png?&width=90pc)
    - Next, we will create **BookApiCreate** and **BookApiCreateInvokePermission**.

    ```yml
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
    - Then, we uncomment the codeblock that we commented above.

    ```yml
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

3. Run the following command to deploy SAM.

    ```bash
    sam build
    sam validate
    sam deploy
    ```

    ![CreatePostAPI](/images/temp/1/76.png?&width=90pc)

4. Open [AWS API Gateway console](https://us-east-1.console.aws.amazon.com/apigateway/home?region=us-east-1).
    - Click **fcj-serverless-api** REST api.
    ![PrepRestApi](/images/temp/1/64.png?width=90pc)
    - At **fcj-serverless-api** resources page.
      - Click **Resources**.
      - Select **POST**.
      - Click **Lambda integration** and check the **book_create** function.
      ![CreatePostAPI](/images/temp/1/77.png?&width=90pc)
      - Click **Stages**.
      - Select **POST**.
      - Copy and save the **Invoke URL**.
      ![CreatePostAPI](/images/temp/1/78.png?&width=90pc)
