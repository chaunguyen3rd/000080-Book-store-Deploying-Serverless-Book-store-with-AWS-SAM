---
title : "Preparations"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 4.1. </b> "
---
In this step, we do some preparations steps to create REST APIs later.

1. Open **template.yaml** in **fcj-book-shop** folder.

2. Add the following script at the end of the file.
    - Firstly, we will create **apiType**, **binaryMediaType** and **getOrPostPathPart** parameters.
    ```
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
    ![PrepRestApi](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/61.png?width=90pc)
    - Next, we will create **BookApi** RestApi and **BookApiResource** Resource.
    ```
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
    ![PrepRestApi](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/62.png?width=90pc)
    
3. Run the following command to deploy SAM.
    ```
    sam build
    sam validate
    sam deploy
    ```
    ![PrepRestApi](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/63.png?width=90pc)

4. Open [AWS API Gateway console](https://us-east-1.console.aws.amazon.com/apigateway/home?region=us-east-1).
    - Click **fcj-serverless-api** REST api.
    ![PrepRestApi](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/64.png?width=90pc)
    - At **fcj-serverless-api** resources page.
      - Click **Resources**.
      - Select **/books**.
      - Check **Resource details** information.
      ![PrepRestApi](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/65.png?width=90pc)
      - Select **/{id}**.
      - Check **Resource details** information.
      ![PrepRestApi](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/66.png?width=90pc)
      - Click **API settings**.
      - Check **multipart/form-data** Media type at **Binary media types**.
      ![PrepRestApi](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/84.png?width=90pc)

So we finish some preparation steps. Next, we will create GET, POST and DELETE api.
