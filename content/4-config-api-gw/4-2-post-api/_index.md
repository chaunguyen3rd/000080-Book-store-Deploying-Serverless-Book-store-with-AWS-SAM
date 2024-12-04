---
title : "Create POST API"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 4.2. </b> "
---
1. Open **template.yaml** file in **fcj-book-shop** folder
2. Add the following script at the end of the file that create the POST method
      ```
                  post:
                    responses:
                      "200":
                        description: 200 response
                        headers:
                          Access-Control-Allow-Origin:
                            type: string
                    x-amazon-apigateway-integration:
                      uri:
                        Fn::Sub: "arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${BookCreate.Arn}/invocations"
                      responses:
                        default:
                          statusCode: 200
                          responseParameters:
                            method.response.header.Access-Control-Allow-Origin: "'*'"
                      passthroughBehavior: when_no_match
                      httpMethod: POST #always POST
                      type: aws_proxy
      ```
      ![CreatePostAPI](/images/1/60.png?&width=90pc)

- Add the following script at the end of the **BookCreate** function
    ```
          Events:
            CreateBook:
              Type: Api
              Properties:
                Path: /books/
                Method: post
                RestApiId:
                  Ref: BookApi
    ```
    ![CreatePostAPI](/images/1/61.png?&width=90pc)

2. Run the following command to deploy SAM
    ```
    sam build
    sam deploy --guided
    ```
    ![CreatePostAPI](/images/1/62.png?&width=90pc)   
{{% notice note %}}
Enter "y" if asked "BookCreate may not have authorization defined, Is this okay? [y/N]:"
{{% /notice %}}

3. Open **book_create** function console
    - Click **API Gateway**
    ![CreatePostAPI](/images/1/63.png?&width=90pc)

4. Show API Gateway being interacted with this function
    - Click this API Gateway
    ![CreatePostAPI](/images/1/64.png?&width=90pc)

5. Display the resources and POST method
    ![CreatePostAPI](/images/1/65.png?&width=90pc)

6. Click **Stages** on the left menu
    - Click **staging**
    - Click **POST**
    - Record **InvokeURL** of POST method
    ![CreatePostAPI](/images/1/66.png?&width=90pc)






