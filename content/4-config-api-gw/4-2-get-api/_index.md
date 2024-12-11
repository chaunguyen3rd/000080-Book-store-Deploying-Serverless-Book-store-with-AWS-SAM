---
title : "Create GET API"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 4.2. </b> "
---
1. Open **template.yaml** in **fcj-book-shop** folder.

2. Add the following script at the end of the file creating a REST API and **GET** method.
    - Firstly, we will create **stage** parameter.
    ```
    stage:
      Type: String
      Default: staging
    ```
    ![CreateGetAPI](/images/temp/1/67.png?width=90pc)
    - Next, add the following scripts to create **BookApiGet** method, **BookApiDeployment** deployment, **BookApiStage** stage and **BookApiGetInvokePermission** permission.
    ```
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

3. Run the following command to deploy SAM.
    ```
    sam build
    sam validate
    sam deploy
    ```
    ![CreateGetAPI](/images/temp/1/69.png?width=90pc)

4. Open [AWS API Gateway console](https://us-east-1.console.aws.amazon.com/apigateway/home?region=us-east-1).
    - Click **fcj-serverless-api** REST api.
    ![PrepRestApi](/images/temp/1/64.png?width=90pc)
    - At **fcj-serverless-api** resources page.
      - Click **Resources**.
      - Select **GET**.
      - Click **Lambda integration** and check the **books_list** function.
      ![CreateGetAPI](/images/temp/1/70.png?width=90pc) 
      - Click **Stages**.
      - Select **GET**.
      - Copy and save the **Invoke URL**.
      ![CreateGetAPI](/images/temp/1/71.png?width=90pc)
