---
title : "Create GET API"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 4.1. </b> "
---
1. Open **template.yaml** in **fcj-book-shop** folder
2. Add the following script at the end of the file creating a REST API and GET method
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

  - Add the following script at the end of the **BooksList** function
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

2. Run the following command to deploy SAM
    ```
    sam build
    sam deploy
    ```
    ![CreateGetAPI](/images/1/55.png?width=90pc)
{{% notice note %}}
Enter "y" if asked "BooksList may not have authorization defined, Is this okay? [y/N]: "
{{% /notice %}}


3. Open Lambda console, click **books_list** function
    - Click **API Gateway**
    ![CreateGetAPI](/images/1/56.png?width=90pc)

4. Show API Gateway being interacted with function
    - Click this API Gateway
    ![CreateGetAPI](/images/1/57.png?width=90pc)

5. Display resources and GET method
    ![CreateGetAPI](/images/1/58.png?width=90pc)

6. Click **Stages** on the left menu
    - Click **staging**
    - Click **GET**
    - Record **InvokeURL** of GET method
    ![CreateGetAPI](/images/1/59.png?width=90pc)






